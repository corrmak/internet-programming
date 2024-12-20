# Отчет по выполнению лабораторной работы №12: Управление стеком и ресурсами Docker Swarm
# Цель
  Освоить навыки работы со стеками в Docker Swarm, управления ресурсами сервисов и использования секретов.

# Выполненные шаги
## 1. Создание собственного стека
  Файл docker-compose.yml:
```yaml
version: "3.8"
services:
  web:
    image: nginx:latest
    ports:
      - "8080:80"
    deploy:
      replicas: 3
      placement:
        constraints:
          - node.role == manager
  db:
    image: postgres:latest
    environment:
      POSTGRES_USER: admin
      POSTGRES_PASSWORD: secret
    deploy:
      replicas: 2
      placement:
        constraints:
          - node.role == manager
  ```
  Развернул стек с помощью команды:
  ```bash
  docker stack deploy -c docker-compose.yml my_stack
  ```
  Проверил состояние сервисов стека:
  ```bash
  docker stack services my_stack
  ```
## 2. Установка нескольких реплик для каждого сервиса на мастер-ноде
  В файле docker-compose.yml установлены реплики и ограничение на размещение сервисов только на управляющей ноде с помощью параметра placement.
  
  Убедился, что сервисы развернуты на мастер-ноде:
  ```bash
  docker service ps my_stack_web
  docker service ps my_stack_db
  ```
## 3. Определение количества оперативной памяти на мастер- и воркер-нодах
  Проверил объем оперативной памяти на всех нодах с помощью команды:
  ```bash
  docker node inspect --format '{{ .Description.Hostname }}: {{ .Description.Resources.MemoryBytes }}' $(docker node ls -q)
  ```
  Преобразовал значение в мегабайты для удобства:
  ```bash
  echo $((<MemoryBytes>/1024/1024)) MB
  ```
## 4. Размещение нового сервиса на ноде с меньшим потреблением ресурсов
  Создал новый сервис с использованием параметра placement и ограничением по ресурсу:
  ```bash
  docker service create --name low-resource-service \
    --placement-pref "spread=node.memory" \
    alpine sleep infinity
  ```
  Проверил размещение сервиса:
  ```bash
  docker service ps low-resource-service
  ```
## 5. Настройка зарезервированного и максимального количества ресурсов
  Создал сервис с ограничением на использование ресурсов:
  ```bash
  docker service create --name resource-limited-service \
    --reserve-memory 128m --limit-memory 256m \
    --reserve-cpu 0.5 --limit-cpu 1.0 \
    nginx:latest
  ```
  Проинспектировал сервис после развертывания для проверки настроек:
  ```bash
  docker service inspect resource-limited-service --pretty
  ```
## 6. Добавление секрета к контейнеру с базой данных
  Создание секрета:
  
  Создал секрет для хранения пароля:
  ```bash
  echo "my_secure_password" | docker secret create db_password -
  ```
  Обновление стека:
  
  Обновил стек для использования секрета:
  ```yaml
  version: "3.8"
  services:
    db:
      image: postgres:latest
      deploy:
        replicas: 2
      secrets:
        - db_password
  secrets:
    db_password:
      external: true
  ```
  Применил обновленный стек:
  ```bash
  docker stack deploy -c docker-compose.yml my_stack
  ```
  Проверка секрета в контейнере:
  
  Убедился, что секрет доступен в контейнере базы данных:
  ```bash
  docker exec -it $(docker ps -q --filter name=my_stack_db) cat /run/secrets/db_password
  ```
# Вывод
  В ходе лабораторной работы был создан стек с двумя сервисами, каждый из которых имел несколько реплик. Для сервисов были настроены ограничения и резервирование ресурсов, а также использовался секрет для передачи пароля в контейнер базы данных. Работа со стеком и ресурсами выполнена успешно.
