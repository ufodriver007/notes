## Основные принципы безопасности
- целостность данных (изменены ли данные?);
- проверка подлинности (это кто?);
- проверка подлинности данных (кем созданы данные?);
- неопровержимость деяния (кто это сделал?);
- предоставление прав (что можно и что нельзя?);
- неразглашение (у кого есть доступ к данным?)

|Фундаментальный принцип|Архитектурное решение|
|--------|--------|
|Целостность данных|Защищенные сетевые протоколы; Система контроля версий; Диспетчер пакетов;|
|Проверка подлинности|Проверка личности; Проверка подлинности системы;|
|Проверка подлинности данных|Регистрация пользователей; Ввод пользователем учетных данных; Механизм сброса и восстановления пароля; Пользовательские сеансы;|
|Неопровержимость деяния|Посылка данных об операции на сервер; Цифровые подписи; Доверенные третьи стороны;|
|Предоставление прав|Выдача прав пользователям; Выдача прав системам; Выдача прав на доступ к файлам и папкам;|
|Неразглашение|Алгоритмы шифрования; Защищенные сетевые протоколы;|



## Набор компонентов Python, создающий стойкую к атакам многоуровневую систему

![[2024-06-15_18-15.png]]

## hashlib
 - Для большинства задач подходит SHA-256.
 - Для обеспечения высокой безопасности подходит SHA3-256, но за это придется заплатить не настолько широкой поддержкой.
- Для объемных сообщений подходит BLAKE2.
- Ни за что не используйте MD5 либо SHA1 для целей безопасности.
- Функции контрольного суммирования ни за что не должны быть использованы для целей обеспечения безопасности. 

```
import hashlib

x = hashlib.sha256(b'message').hexdigest()  
print(x)  # ab530a13e45914982b79f9b7e3fba994cfd1f3fb22f71cea1afbf02b460c6d1d
```

>[!info] Криптографические хеш-функции и функции контрольного суммирования похожи между собой, но, несмотря на это, путать их не стоит. Для криптографической хеш-функции сложно найти коллизию, но и подсчет занимает больше ресурсов. Контрольная же сумма высчитывается быстро, но найти коллизию среди этих сумм несложно.

#### Случайные числа
>[!info] Ни при каких обстоятельствах не используйте модуль random для оборонных и криптографических задач.
```
import secrets

token_hex = secrets.token_hex(16)          # 840f616fcb1a96df1c16f7127518cf4a
token_urlsafe = secrets.token_urlsafe(16)  # qtT3GDpzYQ0LKLEWlaqB0Q
```

>[!info] Если человеку нужно держать ключ в памяти, используйте кодовую фразу.

Генерация кодовой фразы из четырех слов
```
import secrets  
from pathlib import Path

words = Path('/usr/share/dict/words').read_text().splitlines()  
passphrase = ' '.join(secrets.choice(words) for i in range(4))  
print(passphrase)                           # sleepwalk flatters etymologist slipperier
```

#### HMAC-функции
>[!info] Для проверки подлинности данных используется хеширование с ключом.

HMAC-функции (hash-based message authentication code; код проверки подлинности сообщений, использующий хеш-функции) – распространенный способ применить любую хеш-функцию для проверки подлинности данных, как если бы ей можно было передать ключ.

![[hmac.png]]

```
import hashlib
import hmac

hmac_sha256 = hmac.new(b'key', msg=b'message', digestmod=hashlib.sha256)
print(hmac_sha256.hexdigest())  
# 6e9ef29b75fffc5b7abae527d58fdadb2fe42e7219011976917343065f58ed4a
```

Пример взаимодействия
- Алиса и Боб согласовали общий секретный ключ;
- Боб высчитывает хеш документа через HMAC-функцию;
- Боб отправляет документ и его хеш Алисе;
- Алиса высчитывает хеш документа через HMAC-функцию;
- Алиса сравнивает свой хеш с хешем Боба.

#### Атака по времени
Если хэши сравниваются с помощью оператора `==` это даёт возможность провести атаку по времени. Дело в том что оператор `==` выдает False, как только повстречает первое несоответствие между его операндами. Можно проводить множество сравнений и собирать статистику по времени. И в конце концов обнаружить, что для определённого символа, если он  первый(первые), сравнение идёт дольше. 
>[!info] Безопасные системы сравнивают хеши за постоянное время. Модуль hmac содержит функцию `compare_digest`, которая сравнивает хеши за одинаковое время. Всегда сравнивайте хеши через `compare_digest`.

#### Неразглашение данных. Пакет cryptography
```
pip install cryptography
```
Авторы пакета разделили доступные в нем возможности на две
категории:
- «взрывчатые вещества», нетривиальные низкоуровневые инструменты;
   Уважительных причин лезть сюда практически нет. Разве что:
    - нужно шифровать файлы, которые не помещаются в оперативной памяти;
    - нужно использовать редкий шифр;
- «готовые рецепты», высокоуровневые и несложные в использовании.

###### Пример использования `fernet` для шифрования текста и проверки подлинности
![[fernet.png]]

```
from cryptography.fernet import Fernet  
  
key = Fernet.generate_key()  
f = Fernet(key)  
token = f.encrypt(b"A really secret message. Not for prying eyes.")  

print(token)  # b'gAAAAABmcwq9kisDN4h83Z-GY4oQty8n6sNKriGSoTcHkVhehHqZWjOGKwANUFfz07zzc-O0v1zDyx4c4y7M7HiM6tuYBS2nvNMa1pJwgQaP2IT0xDHR8MDoxIZXA4uCTYjI3geStRA9'

print(f.decrypt(token))  # b'A really secret message. Not for prying eyes.'
```

![[fernet_decrypt.png]]

Для алгоритма со сменой ключа используйте `MultiFernet`
```
from cryptography.fernet import Fernet, MultiFernet
```

>[!info] Если алгоритму шифрования требуется один и тот же ключ как для зашифровки открытого текста, так и для его расшифровки –  такое шифрование называется **симмет­ричным**.