# OpenSSL

## Описание.
Программа для работы с ключами.

## Самоподписанный сертификат.
```
openssl req -x509 -sha256 -nodes -days 365 -newkey rsa:2048 -keyout privateKey.key -out certificate.crt
```

## Сгенерировать временный пароль.
```
openssl rand -base64 16
```
