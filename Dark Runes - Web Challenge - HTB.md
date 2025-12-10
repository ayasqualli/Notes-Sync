We found two CVEs concerning this challenge : **CVE-2023-0835** and **CVE-2024-21501**

The exploit goes as follows:
1- Register as `admin` and log in
2- Exploit **CVE-2024-21501** on the `/document` endpoint
3- Check which filename is the actual `ACEES_PASS`
4-Once we get the right `ACCESS_PASS` and `isAdmin`, exploit **CVE-2023-0835** on the `/document/debug/export` endpoint
