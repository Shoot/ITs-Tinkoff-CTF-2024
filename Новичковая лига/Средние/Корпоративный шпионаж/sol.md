По иконке понимаем, что exe собран с помощью PyInstaller, распаковываем через pyinstxtractor, внутри узнаём точную версию Python (3.10.0rc2) из dll и декомпилируем с помощью pycdc vacuum.pyc. Внутри видим создание двух файлов caqqwe1.exe и fmzasde, причём второй через 0.4 секунды удаляется. Не дадим удалить файл с помощью выставления прав на директорию (работает в windows). caqqwe1.exe разбираем аналогично стартовому бинарнику, видим процесс импорта fmzasde, скачиваем конкретную версию embedded python для корректной работы marshal и выполняем в нём:

```python
import marshal
import types

c = open('fmzasde.bak', 'rb')
pc = c.read()
c.close()

co = marshal.loads(pc)
m = types.ModuleType('name')
exec(co, m.__dict__)

# Here we can see methods
print(m.__dict__)

print(m.generate_passwd())
```
