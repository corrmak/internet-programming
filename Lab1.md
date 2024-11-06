# Лабораторная работа 1: Install Docker и Installing docker into a minimal alpine image

В данной лабораторной работе представлены шаги по установке Docker, созданию скрипта автоматизированной установки, а также запуск Docker внутри минимального образа `alpine`.

---

## 1. Установка Docker на операционную систему

Для установки Docker следуйте официальной инструкции: [Установка Docker](https://docs.docker.com/engine/install/).

### Шаги для установки Docker на Debian/Ubuntu:
1. Обновите индекс пакетов:
   ```sudo
    apt update
   ```
2. Установите необходимые зависимости:
   ```sudo
   apt install apt-transport-https ca-certificates curl software-properties-common
   ```
3. Добавьте GPG-ключ Docker:
   ```curl
   -fsSL https://download.docker.com/linux/ubuntu/gpg
   ```
   ```sudo
   apt-key add -
   ```
4. Добавьте репозиторий Docker:
   ```sudo
   add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
   ```
5. Установите Docker:
   ```sudo apt update
   sudo apt install docker-ce -y
   ```
6. Проверьте статус Docker:
   ```sudo
   systemctl status docker
   ```
Docker успешно установлен и готов к использованию.

## 2.  Создание скрипта автоматизированной установки Docker
Для упрощения установки Docker создам скрипт Docker_install.sh со следующим содержимым:
```
!/bin/bash
```
# Обновление системы
   ```sudo
   apt update -y
   ```
# Установка зависимостей
   ```sudo 
   apt install apt-transport-https ca-certificates curl software-properties-common -y
   ```
# Добавление ключа и репозитория Docker
   ```curl
   -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
   ```
   ```sudo
   add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
   ```
# Установка Docker
   ```sudo 
   apt update -y
   ```
   ```sudo 
   apt install docker-ce -y
   ```
# Запуск и настройка автозапуска Docker
   ```sudo 
   systemctl start docker
   ```
   ```sudo 
   systemctl enable docker
   ```
# Установка Docker Compose
   ```sudo 
   curl -L "https://github.com/docker/compose/releases/download/1.28.6/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
   ```
   ```sudo 
   chmod +x /usr/local/bin/docker-compose
   ```
# Добавление пользователя в группу docker для работы без sudo
   ```sudo 
   usermod -aG docker $USER
   ```

# Перезагрузка системы
   ```
   echo "Перезагрузите систему, чтобы изменения вступили в силу."
   ```
### Запуск системы
1. Сохраните файл Docker_install.sh.
2. Сделайте файл исполняемым:
   chmod +x Docker_install.sh
3. Запустите скрипт:
   sudo bash Docker_install.sh

После выполнения этих шагов Docker и Docker Compose будут установлены и готовы к работе.

## 3. Установка Docker внутри минимального образа alpine

Теперь установим Docker внутри контейнера на основе минимального образа alpine.

1. Запустите контейнер alpine:
   ```
   docker run -it alpine /bin/sh
   ```
3. Обновите пакетный менеджер внутри контейнера:
   ```
   apk update
   ```
5. Установите Docker CLI:
   ```
   apk add docker
   ```

Теперь Docker установлен внутри образа alpine, и вы можете работать с ним внутри контейнера.

## Заключение
Эта лабораторная работа включает базовую установку Docker,создание автоматизированного установочного скрипта и использование Docker внутри образа alpine.
Полученные знания помогут в будущих лабораторных заданиях.


    
