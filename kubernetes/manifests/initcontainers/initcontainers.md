# Init Containers

## Описание. 
Специальный контейнер в поде, который выполняет ряд задач.

Примеры использования Init-контейнеров:

* Загрузка и настройка зависимостей: Init-контейнеры могут загружать и настраивать зависимости, необходимые для основного контейнера приложения до его запуска.
* Создание схемы базы данных: Init-контейнер может использоваться для создания схемы базы данных.
* Прогрев кеша: можно использовать Init-контейнер для предварительной загрузки часто используемых данных в кеш, например в Redis.
* Настройка сети: Init-контейнеры могут выполнять задачи по настройке сети или установлению соединений с внешними сервисами.
* Клонирование Git-репозиториев: Init-контейнеры могут клонировать Git-репозитории или записывать файлы в присоединённые тома Pod.
* Настройка среды: Init-контейнеры могут выполнять задачи по созданию директорий, установке прав доступа или запуску пользовательских скриптов для настройки среды для основного приложения.
* Ожидание сервисов: Init-контейнеры могут ожидать запуска сервиса перед запуском основного приложения.

## Пример.
```
apiVersion: v1
kind: Pod
metadata:
  name: init-demo
spec:
  containers:
  - name: nginx
    image: nginx
    ports:
    - containerPort: 80
    volumeMounts:
    - name: workdir
      mountPath: /usr/share/nginx/html
  initContainers:
  - name: install
    image: busybox:1.28
    command:
    - wget
    - "-O"
    - "/work-dir/index.html"
    - http://info.cern.ch
    volumeMounts:
    - name: workdir
      mountPath: "/work-dir"
  dnsPolicy: Default
  volumes:
  - name: workdir
    emptyDir: {} 
```
