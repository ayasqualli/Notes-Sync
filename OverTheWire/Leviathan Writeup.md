## levi 1

After enumeration with ls -la we find a folder called ./backup. In this folder there is a HTML file called bookmarks.html
After grepping the file with leviathan we find the password

Password: 3QJ3TgzHDq

## levi 2:
Using ``ltrace`` we can find the program uses `strcmp` to check the password
We find the ``strcmp`` function indeed using ``strings``
![[Pasted image 20251119232657.png]]
We find here that the password is `sex`
After the check is complete we gain privilege to become ``leviathan2`` thus we can get the password from `/etc/leviathan_pass/leviathan2`  as indicated in the instructions

Password: NsN1HwFoyN

## levi 3:
Using ltrace with a valid file we can pinpoint the exploit: in snprintf if we input two files seperated by a space only the first one is read.
So to open the password file, we create a file containing a space, the asscoiate the first word of that name with a symbolic link to the password file so when we open the file with the space the access function checks the whole name and bypass the restriction on the password file

![[Pasted image 20251120005806.png]]

![[Pasted image 20251120005903.png]]

![[Pasted image 20251120010345.png]]

Password: f0n8h2iWLP

## levi 4:
![[Pasted image 20251120010700.png]]

After some testing we figured that the password is ``snlprintf`` as we can see in the last strcmp 
![[Pasted image 20251120011118.png]]

Password: WG1egElCvO

## levi 5:
![[Pasted image 20251120011350.png]]
We convert the binary using any available tool
![[Pasted image 20251120011448.png]]

Password: 0dyxT7F4QD

## levi 6:
![[Pasted image 20251120012047.png]]![[Pasted image 20251120012219.png]]

Password: szo7HDB88w

## levi 7
![[Pasted image 20251120012448.png]]
We need to disassemble the binary to find if the pin is visible in the assembly code
We will use gdb for the dynamic analysis
![[Pasted image 20251120012921.png]]

We add a breakpoint in the `atoi` call to see how the input gets compared
![[Pasted image 20251120013352.png

In line +34 we see that we compare the input with `ebp-0xc` with out initial input from `eax` 
We can calculate the address to it then dump its content![[Pasted image 20251120013718.png]]
We found the pin 

![[Pasted image 20251120013921.png]]

Password: qEs5Io5yM8