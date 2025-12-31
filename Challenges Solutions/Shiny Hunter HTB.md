Solution:
LGC pseudo random generator is predictable and we can use the MAC address to find the shiny one with the LGC given parameters.

### Solve Script:
```python
from pwn import *
import random
import re
import time

context.log_level = 'error'

HOST = '83.136.249.164'
PORT = 36896

def lcg(seed, a=1664525, c=1013904223, m=2**32):
    return (a * seed + c) % m

def generate_ids(seed):
    random.seed(seed)
    tid = random.randint(0, 65535)
    sid = random.randint(0, 65535)
    return tid, sid

def generate_pokemon(seed, tid, sid):
    random.seed(seed)
    pid = random.randint(0, 2**32 - 1)
    shiny_value = ((tid ^ sid) ^ (pid & 0xFFFF) ^ (pid >> 16))
    return shiny_value < 8, pid

def mac_to_int(mac_str):
    return int(mac_str.replace(":", ""), 16)

def find_shiny(mac_str):
    mac_int = mac_to_int(mac_str)

    for boot_time in range(20):  # Try different boot time offsets
        initial_seed = boot_time + mac_int
        seed = lcg(initial_seed)
        tid, sid = generate_ids(seed)

        for i in range(3):
            shiny, pid = generate_pokemon(seed + i, tid, sid)
            if shiny:
                return {
                    "boot_time": boot_time,
                    "mac": mac_str,
                    "starter_index": i,
                    "pid": pid,
                    "tid": tid,
                    "sid": sid
                }
    return None

def extract_flag():
    io = remote(HOST, PORT)
    banner = io.recvuntil(b"Game Library Synced: OK", timeout=5).decode()

    match = re.search(r"Mac Address: ([0-9a-f:]+)", banner)
    if not match:
        print("MAC address not found.")
        return

    mac = match.group(1)
    shiny_data = find_shiny(mac)
    if not shiny_data:
        print("No shiny found.")
        return

    print(f"[🎉] Shiny starter is #{shiny_data['starter_index'] + 1}")

    # Finish boot and intro dialogs
    io.recvuntil(b"What is your name?")
    io.sendline(b"Versaga")

    # Proceed through dialog with time delays
    time.sleep(1)
    for _ in range(12):  # 12 dialog clears before choices
        io.recvuntil(b"[*]")

    io.recvuntil(b"1. Bulbasaurus")
    choices = io.recvuntil(b"Choose your starter").decode()

    io.sendline(str(shiny_data["starter_index"] + 1).encode())

    # Read until flag
    full_output = io.recv(timeout=5).decode()
    flag_match = re.search(r"HTB\{[^\}]+\}", full_output)
    if flag_match:
        print(f"[🚩] Flag found: {flag_match.group(0)}")
    else:
        print("[❌] Flag not found in output.")

    io.close()

if __name__ == "__main__":
    extract_flag()

```