# Отчет по выполнению лабораторной работы №11: Создание и управление кластером Docker Swarm
# Цель
  Настроить кластер Docker Swarm, управлять нодами, создать и обновить Docker-образ, а также развернуть сервис с репликами.

# Шаги выполнения
## 1. Установка кластера с одной управляющей и двумя рабочими нодами
  Инициализация управляющей ноды:
  Инициализировал кластер Docker Swarm:
  ```bash
  docker swarm init --advertise-addr <MANAGER_IP>
  ```
  Получил токен для добавления рабочих нод:
  ```bash
  docker swarm join-token worker
  ```
  Добавление рабочих нод:
  
  На рабочих нодах выполнил команду для присоединения к кластеру:
  ```bash
  docker swarm join --token <TOKEN> <MANAGER_IP>:2377
  ```
  Проверка статуса кластера:
  
  На управляющей ноде проверил состояние кластера:
  ```bash
  docker node ls
  ```
## 2. Удаление и повторное добавление рабочей ноды
  Удалил рабочую ноду:
  ```bash
  docker node rm <NODE_ID>
  ```
  Повторно добавил ноду, используя токен:
  ```bash
  docker swarm join --token <TOKEN> <MANAGER_IP>:2377
  ```
  Убедился, что нода снова добавлена:
  ```bash
  docker node ls
  ```
## 3. Создание Docker-образа для отображения метаданных узла
  Dockerfile:
  ```dockerfile
  FROM python:3.9-slim
  WORKDIR /app
  COPY app.py .
  RUN pip install flask
  EXPOSE 5000
  CMD ["python", "app.py"]
  ```
  Код приложения app.py:
  ```python
  from flask import Flask, jsonify
  import socket
  
  app = Flask(__name__)
  
  @app.route('/')
  def metadata():
      return jsonify({
          "Hostname": socket.gethostname(),
          "IP Address": socket.gethostbyname(socket.gethostname())
      })

  if __name__ == "__main__":
      app.run(host="0.0.0.0", port=5000)
  ```
  Сборка образа:
  
  Собрал образ с метаданными узла:
  ```bash
  docker build -t node-metadata:v1 .
  ```
## 4. Развертывание сервиса с 3 репликами
  Развернул сервис:
  ```bash
  docker service create --name metadata-service --replicas 3 -p 5000:5000 node-metadata:v1
  ```
  Проверил статус реплик:
  ```bash
  docker service ps metadata-service
  ```
  Проверил, на какой IP-адрес попадает запрос:
  ```bash
  curl <MANAGER_IP>:5000
  ```
## 5. Пересборка образа и обновление приложения
  Обновление Dockerfile с добавлением информации о разработчике:
  ```dockerfile
  FROM python:3.9-slim
  LABEL maintainer="Your Name <your.email@example.com>"
  WORKDIR /app
  COPY app.py .
  RUN pip install flask
  EXPOSE 5000
  CMD ["python", "app.py"]
  ```
  Пересборка образа:
  ```bash
  docker build -t node-metadata:v2 .
  ```
  Обновление приложения в сервисе:
  ```bash
  docker service update --image node-metadata:v2 metadata-service
  ```
  Проверил обновление приложения:
  ```bash
  curl <MANAGER_IP>:5000
  ```
# Вывод
  В ходе лабораторной работы был настроен кластер Docker Swarm с одной управляющей и двумя рабочими нодами. Изучены операции удаления и повторного добавления нод, создан Docker-образ для отображения метаданных узлов, а также выполнено обновление сервиса с использованием команды docker service update.
