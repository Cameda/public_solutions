# Alpine 3.16 from scratch

## Описание.
Создадим базовый образ с Alpine.

## Поехали!
Создадим Dockerfile с содержимым ниже.

```
FROM scratch
ADD alpine-minirootfs-3.16.2-x86_64.tar.gz /
CMD ["/bin/sh"]
```

Архив качаем с официального сайта Alpine и кладём в туже директорию, что Dockerfile.
https://dl-cdn.alpinelinux.org/alpine/v3.16/releases/

### Собираем образ.
```
docker build -t alpine:scratch -f Dockerfile .
```

Где alpine:worker является тегом образа.
```
docker images
REPOSITORY   TAG       IMAGE ID       CREATED              SIZE
alpine       scratch   664d6e5e6c1a   About a minute ago   5.55MB
```

## Если надо удалить что-то из архива, то можно его распаковать, удалить что-то и упаковать заново. Например, удалить mkdir из образа.
```
FROM scratch
ADD alpine-no-ps-minirootfs.tar.gz /
RUN adduser -D -g '' worker
CMD ["/bin/sh"]
RUN touch /tmp/files
USER worker
```
