# Отчет по выполнению лабораторной работы №9: Docker Compose, NTP и Резервное копирование
# Цель
  Освоить использование Docker Compose для управления контейнерами и работы с volume, развернуть сервер времени (NTP), настроить контейнеры на основе различных операционных систем, а также изучить инструменты для резервного копирования данных.

# Шаги выполнения:
## 1. Резервное копирование volume статического контента сайта с использованием Docker Compose
  Создан файл docker-compose.yml для резервного копирования:
  ```yaml
  version: "3.9"
  services:
    web:
      image: nginx:latest
      volumes:
        - web_content:/usr/share/nginx/html
    backup:
      image: alpine:latest
      volumes:
        - web_content:/backup
      command: sh -c "tar -czvf /backup/backup.tar.gz /backup"
  volumes:
    web_content:
  ```
  Выполнил команды для запуска и резервного копирования:
  ```bash
  docker-compose up -d
  docker-compose run backup
  docker cp $(docker-compose ps -q backup):/backup/backup.tar.gz ./backup.tar.gz
  ```
## 2. Установка NTP-сервера и настройка контейнеров
  Установка NTP-сервера
  
  Создал контейнер на основе ubuntu для NTP-сервера:
  ```bash
  docker run -d --name ntp-server ubuntu:latest
  docker exec -it ntp-server apt update && apt install -y ntp
  docker exec -it ntp-server service ntp start
  ```
  Настройка клиентов:
  
  Созданы два контейнера, один на базе centos, другой на основе `alpine`:
  ```bash
  docker run -d --name centos-client centos:latest
  docker exec -it centos-client yum install -y ntpdate
  docker exec -it centos-client ntpdate ntp-server
  ```
  ```
  docker run -d --name alpine-client alpine:latest
  docker exec -it alpine-client apk add ntp
  docker exec -it alpine-client ntpd -p ntp-server
  ```
## 3. Создание Dockerfile для настройки NTP
  Написан Dockerfile для автоматической настройки NTP:
  ```dockerfile
  FROM ubuntu:latest
  RUN apt update && apt install -y ntp
  COPY ntp.conf /etc/ntp.conf
  CMD ["service", "ntp", "start"]
  ```
  Собран образ:
  ```bash
  docker build -t ntp-server-image .
  docker run -d --name ntp-server ntp-server-image
  ```
## 4. Резервное копирование и восстановление базы данных PostgreSQL
  Резервное копирование
  
  Создан контейнер PostgreSQL с volume для данных:
  ```bash
  docker run -d --name postgres-db -e POSTGRES_PASSWORD=example -v pgdata:/var/lib/postgresql/data postgres:latest
  docker exec -it postgres-db pg_dumpall -U postgres > backup.sql
  ```
  Восстановление:
  
  Восстановление данных в новом контейнере:
  ```bash
  docker run -d --name postgres-db-new -e POSTGRES_PASSWORD=example postgres:latest
  docker exec -i postgres-db-new psql -U postgres < backup.sql
  ```
## 5. Изучение инструментов резервного копирования
  Duplicity: Позволяет создавать зашифрованные и сжатые резервные копии.
  ```bash
  apt install duplicity
  duplicity /data file:///backup
  duplicity restore file:///backup /data-restored
  ```
  Velero: используется для резервного копирования кластеров Kubernetes. 
  
  Пример резервного копирования:
  ```bash
  velero install --provider aws --bucket <bucket-name> --backup-location-config region=<region>
  velero backup create my-backup --include-namespaces <namespace-name>
  velero restore create --from-backup my-backup
  ```
  Restic: используется для резервного копирования данных в файловую систему или облачные хранилища. 
  
  Пример резервного копирования:
  ```bash
  restic init -r /backup
  restic backup /data --repo /backup
  restic restore latest --target /data-restored --repo /backup
  ```
# Вывод
  В ходе лабораторной работы изучены базовые механизмы работы с Docker Compose, настройка NTP-сервера, резервное копирование данных с использованием volume и утилит СУБД. Также рассмотрены инструменты резервного копирования: Duplicity, Velero, Restic, с практическими примерами их использования.
