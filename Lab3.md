# Лабораторная работа 3: Network, Regisry, Portainer

В этой лабораторной работе мы научимся выполнять основные операции по управлению контейнерами Docker,
разверачивать Docker Registry и Shipyard, а также установить ограничения для контейнера с Apache.

## Выполненные шаги

1. Остановка всех контейнеров, кроме Portainer
Сначала остановил все активные контейнеры:
```bash
docker stop $(docker ps -a -q)
```
Затем нашел ID контейнера Portainer командой:
```bash
docker ps
```
После этого остановил все контейнеры, кроме Portainer.

2. Удаление всех контейнеров
Удалил все остановленные контейнеры:
```bash
docker container rm $(docker ps -a -q)
```
3. Удаление всех образов
Удалил все образы, чтобы освободить место:
```bash
docker image prune -a
```
4. Удаление всех томов
Удалил все Docker-тома:
```bash
docker volume rm $(docker volume ls -q)
```
5. Полная очистка системы Docker
Для очистки системы Docker удалил неиспользуемые контейнеры и образы:
```bash
docker system prune -a
```
6. Установка Docker Registry
Развернул Docker Registry, используя официальный образ:
```bash
docker run -d -p 5000:5000 --restart=always --name registry registry:2
```
7. Установка Shipyard и подключение к Docker API
Запустил Shipyard для управления контейнерами и подключил его к Docker API:
```bash
docker run -d --name shipyard --link registry:registry -v /var/run/docker.sock:/var/run/docker.sock shipyard/shipyard
```
После этого открыл интерфейс Shipyard через браузер и настроил управление контейнерами.

8. Установка ограничения на контейнер Apache
Запустил контейнер Apache с установленным лимитом на количество открытых файлов:
```bash
docker run -d -p 80:80 --name my-apache httpd:latest
docker exec -it my-apache /bin/bash
ulimit -n 1024
```
Теперь лимит на количество открытых файлов для контейнера Apache установлен на 1024.

