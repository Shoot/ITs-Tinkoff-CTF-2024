Смотрим исходники в jadx, находим шифрование БД, пишем обратный расшифровщик, в полученном sqlite файле текстом лежит ключ.

```python
import hashlib
import hmac
from Crypto.Cipher import AES
import zlib

with open("MessengerDB.db_enc", "rb") as f:
    data = f.read()

# From jadx
encryptionKey = bytearray([100, 97, 116, 97, 98, 97, 115, 101, 101, 110, 99, 114, 121, 112, 116, 105, 111, 110, 107, 101, 121])

str2_l = int.from_bytes(data[:2], "big")
iv = data[2:18]
enc_key_gen = data[38:str2_l+2]

enc_db = data[str2_l+2:-16]
orig_db_md5 = data[-16:]

#==========

# 16 bytes of 0
# mac = AES, HmacSHA256: enc_key_gen (zeros)
# mac2 = AES, HmacSHA256: enc_key_gen (mac)

mac = hmac.new(bytearray([0 for _ in range(16)]), enc_key_gen, hashlib.sha256).digest()
mac2 = hmac.new(mac, enc_key_gen, hashlib.sha256).digest()[:16]

#==========

# mac = AES, HmacSHA256: encryptionKey (enc_key)
# gen iv
# result = AES/GCM/NoPadding: deflater(src_db) (mac, iv)

mac = hmac.new(mac2, encryptionKey, hashlib.sha256).digest()[:16]

cipher = AES.new(mac, AES.MODE_GCM, iv)
dec = cipher.decrypt(enc_db)

with open("decrypted.sqlite", "wb") as f:
    f.write(zlib.decompress(dec))
```
