```php
<?php

$encrypted = "L7Rv00A8TuwJAr67kITxxcSgnIk25Am/";

$key = 'rcmail-!24ByteDESkey*Str';

// Decode Base64

$data = base64_decode($encrypted);

// Extract IV (first 8 bytes for 3DES)

$iv = substr($data, 0, 8);

$ciphertext = substr($data, 8);

// Decrypt (3DES-CBC)

$password = openssl_decrypt(

$ciphertext,

'des-ede3-cbc', // 3DES algorithm

$key,

OPENSSL_RAW_DATA,

$iv

);

echo "Decrypted password: " . $password;

?>
```