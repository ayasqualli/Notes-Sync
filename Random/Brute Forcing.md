ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt -H "Host: FUZZ.permx.htb" [DOMAIN] -t 200 -ic

ffuf -w /usr/share/wordlists/seclists/Discovery/DNS/bitquark-subdomains-top100000.txt -u http://10.129.96.224 -H "Host: FUZZ.soulmate.htb" -t 50 -ic -mc 200
