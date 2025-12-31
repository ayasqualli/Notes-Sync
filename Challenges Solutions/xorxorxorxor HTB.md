 Known plaintex attack (HTB{) and they key is the same for each 4 bytes

### Solution Script:
````python
def xor_decrypt(cipher_hex, known_plaintext=b'HTB{'):

    cipher = bytes.fromhex(cipher_hex)

    key = bytes([cipher[i] ^ known_plaintext[i] for i in range(4)])

    decrypted = b''

    for i in range(len(cipher)):

        decrypted += bytes([cipher[i] ^ key[i % 4]])

    return decrypted.decode(), key.hex()

ciphertext = "134af6e1297bc4a96f6a87fe046684e8047084ee046d84c5282dd7ef292dc9"

plaintext, key = xor_decrypt(ciphertext)

print("Recovered key:", key)

print("Decrypted flag:", plaintext)
```