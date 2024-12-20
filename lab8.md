# Отчет по выполнению лабораторной работы №8: Управление контейнерами и тестирование производительности
# Цель
  Научиться работать с контейнерами Docker, проводить тестирование производительности, замораживать и размораживать контейнеры, анализировать логи и статистику, а также использовать дополнительные параметры для тестирования веб-приложений.

# Выполненные шаги
## 1. Получение информации о состоянии Docker
  С помощью команды docker info собрал данные о состоянии Docker на хосте и типе операционной системы. Информацию сохранил в файл:
  ```bash
  docker info > docker_info.txt
  ```
## 2. Запуск произвольного контейнера и тестирование производительности
  Запустил контейнер:
  ```bash
  docker run -d --name test-container alpine sleep 300
  ```
  Выполнил тестирование производительности системы в течение 3 минут, используя утилиты для мониторинга ресурсов, такие как top или htop.

## 3. Замораживание и размораживание контейнера
  Заморозил контейнер с помощью команды:
  ```bash
  docker pause test-container
  ```
  Затем разморозил:
  ```bash
  docker unpause test-container
  ```
## 4. Анализ логов веб-сервера Apache во время тестирования
  Запустил контейнер Apache:
  ```bash
  docker run -d --name apache-server httpd:latest
  ```
  Провел тестирование производительности веб-сервера, сохранил логи контейнера в файл:
  ```bash
  docker logs apache-server > apache_logs.txt
  ```
## 5. Запуск контейнера MongoDB и просмотр статистики контейнеров
  Запустил контейнер MongoDB:
  ```bash
  docker run -d --name mongodb-container mongo
  ```
  Посмотрел статистику всех контейнеров, исключив столбец PIDS, и сохранил результат в файл:
  ```bash
  docker stats --format "table {{.Container}}\t{{.Name}}\t{{.CPUPerc}}\t{{.MemUsage}}" > container_stats.txt
  ```
## 6. Дополнительные параметры тестирования производительности веб-приложений
  Изучил параметры для тестирования производительности веб-приложений, такие как настройки нагрузки, количество одновременных подключений и задержки. Для тестирования использовал утилиты, например, `ab` (Apache Benchmark) или `wrk`.

## 7. Подсчет запросов к веб-серверу Nginx и анализ статистики
  Запустил контейнер Nginx:
  ```bash
  docker run -d --name nginx-server -p 80:80 nginx
  ```
  Выполнил несколько тестов с использованием разных IP-адресов. Для подсчета запросов за день и анализа статистики по странам использовал инструменты, такие как nginx log analyzer или скрипты на Python. Результаты сохранил в файл nginx_statistics.txt.

# Вывод
В ходе лабораторной работы освоены базовые и продвинутые операции с контейнерами Docker, тестирование производительности и анализ данных, а также изучены инструменты мониторинга и анализа логов веб-приложений.
