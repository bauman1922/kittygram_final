### Проект «Kittygram»

#### Описание проекта:
- Kittygram - это платформа, которая позволяет пользователям делиться своими милыми пушистыми созданиями и рассказывать об их радостных достижениях!
#### Стек технологий:
- Django REST
- Python 3.9
- Gunicorn
- Nginx
- JS
- Node.js
- PostgreSQL
- Docker
#### Как запустить проект(Linux):
- Склонируйте репозиторий на свой компьютер:
```
https://github.com/bauman1922/kittygram_final
```
- Создать и заполнить .env по аналогии с .env.example из корневой директории проекта.
- Создание и загрузка Docker-образов:
```
cd frontend
docker build -t USERNAME/kittygram_frontend .
cd ../backend
docker build -t USERNAME/kittygram_backend .
cd ../nginx
docker build -t USERNAME/kittygram_gateway .
```
```
docker push USERNAME/kittygram_frontend
docker push USERNAME/kittygram_backend
docker push USERNAME/kittygram_gateway
```
- Подключитесь к удаленному серверу:
```
ssh -i путь_до_файла_с_SSH_ключом/название_файла_с_SSH_ключом_без_расширения username@ip 
```
- Создать на удаленном сервере папку под развертывание проекта:
```
mkdir kittygram
```
- Установить Docker Compose на удаленный сервер:
```
sudo apt update
sudo apt install curl
curl -fsSL https://get.docker.com -o get-docker.sh
sudo sh get-docker.sh
sudo apt install docker-compose
```
- Скопируйте файлы docker-compose.production.yml и .env в директорию kittygram/ на сервере.
- Запустите Docker Compose в режиме демона:
```
sudo docker-compose -f /home/YOUR_USERNAME/kittygram/docker-compose.production.yml up -d
```
- Выполните миграции, соберите статические файлы бэкенда и скопируйте их в /backend_static/static/:
```
sudo docker-compose -f /home/YOUR_USERNAME/kittygram/docker-compose.production.yml exec backend python manage.py migrate
sudo docker-compose -f /home/YOUR_USERNAME/kittygram/docker-compose.production.yml exec backend python manage.py collectstatic
sudo docker-compose -f /home/YOUR_USERNAME/kittygram/docker-compose.production.yml exec backend cp -r /app/collected_static/. /backend_static/static/
```
- Изменить файл конфигурации Nginx:
```
sudo nano /etc/nginx/sites-enabled/default
````
Пример: 
```
server {
    listen 80;
    server_name example.com;
    
    location / {
        proxy_set_header HOST $host;
        proxy_pass http://127.0.0.1:9000;

    }
}
```
- Проверить корректность настроек Nginx:
```
sudo nginx -t
```
- Перезапустить Nginx:
```
sudo service nginx reload
```
- Для адаптации проекта к Github Actions, добавить секреты в соответствующий раздел репозитория: 
```
DOCKER_USERNAME                # имя пользователя в DockerHub
DOCKER_PASSWORD                # пароль пользователя в DockerHub
HOST                           # IP-адрес сервера
USER                           # имя пользователя
SSH_KEY                        # содержимое приватного SSH-ключа (cat ~/.ssh/id_rsa)
SSH_PASSPHRASE                 # пароль для SSH-ключа

TELEGRAM_TO                    # ID вашего телеграм-аккаунта (можно узнать у @userinfobot, команда /start)
TELEGRAM_TOKEN                 # токен вашего бота (получить токен можно у @BotFather, команда /token, имя бота)
```
##### Автор
* [Sergey Samorukov](https://github.com/bauman1922)