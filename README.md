# API_YAMDB
REST API проект для сервиса YaMDb — сбор отзывов о фильмах, книгах или музыке.

(![workflow](https://github.com/dnker/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg?branch=master&event=push))

## Описание.

Проект YaMDb собирает отзывы пользователей на произведения. Сами произведения в YaMDb не хранятся, здесь нельзя посмотреть фильм или послушать музыку.
Произведения делятся на категории, такие как «Книги», «Фильмы», «Музыка». Например, в категории «Книги» могут быть произведения «Винни-Пух и все-все-все» и «Марсианские хроники», а в категории «Музыка» — песня «Давеча» группы «Жуки» и вторая сюита Баха. Список категорий может быть расширен (например, можно добавить категорию «Изобразительное искусство» или «Ювелирка»). 
Произведению может быть присвоен жанр из списка предустановленных (например, «Сказка», «Рок» или «Артхаус»). 
Список категорий  может быть расширен (например, можно добавить категорию «Изобразительное искусство» или «Ювелирка»).

## Технологии.
[![Python](https://img.shields.io/badge/-Python-464646?style=plastic&logo=Python&logoColor=56C0C0&color=008080)](https://www.python.org/)
[![Django](https://img.shields.io/badge/-Django-464646?style=plastic&logo=Django&logoColor=56C0C0&color=008080)](https://www.djangoproject.com/)
[![Django REST Framework](https://img.shields.io/badge/-Django%20REST%20Framework-464646?style=plastic&logo=Django%20REST%20Framework&logoColor=56C0C0&color=008080)](https://www.django-rest-framework.org/)
[![PostgreSQL](https://img.shields.io/badge/-PostgreSQL-464646?style=plastic&logo=PostgreSQL&logoColor=56C0C0&color=008080)](https://www.postgresql.org/)
[![JWT](https://img.shields.io/badge/-JWT-464646?style=plastic&color=008080)](https://jwt.io/)
[![Nginx](https://img.shields.io/badge/-NGINX-464646?style=plastic&logo=NGINX&logoColor=56C0C0&color=008080)](https://nginx.org/ru/)
[![gunicorn](https://img.shields.io/badge/-gunicorn-464646?style=plastic&logo=gunicorn&logoColor=56C0C0&color=008080)](https://gunicorn.org/)
[![Docker](https://img.shields.io/badge/-Docker-464646?style=plastic&logo=Docker&logoColor=56C0C0&color=008080)](https://www.docker.com/)
[![Docker-compose](https://img.shields.io/badge/-Docker%20compose-464646?style=plastic&logo=Docker&logoColor=56C0C0&color=008080)](https://www.docker.com/)
[![Docker Hub](https://img.shields.io/badge/-Docker%20Hub-464646?style=plastic&logo=Docker&logoColor=56C0C0&color=008080)](https://www.docker.com/products/docker-hub)
[![GitHub%20Actions](https://img.shields.io/badge/-GitHub%20Actions-464646?style=plastic&logo=GitHub%20actions&logoColor=56C0C0&color=008080)](https://github.com/features/actions)

### Как запустить проект:

> приводятся команды для `Linux`.

Клонировать репозиторий и перейти в него:
```bash
git clone https://github.com/DNKer/yamdb_final
cd /yamdb_final
```
Создать и активируть виртуальное окружение. Обновить установщик пакетов:
```bash
python3 -m venv venv
source venv/bin/activate
python3 -m pip install --upgrade pip
```

Установить зависимости из файла requirements.txt:
```bash
pip install -r api_yamdb/requirements.txt
```

Перейти в папку с файлом docker-compose.yaml:
```bash
cd infra
```
### Установить Docker (операционная система Linux)
Синхронизировать списки пакетов и обновить их в системе:
```bash
sudo apt update && apt upgrade -y
```
Установить Docker:
```bash
sudo apt install docker.io
```
Активировать Docker с указанием запуска при старте системы:
```bash
sudo systemctl enable docker
```
Запустить Docker:
```bash
sudo systemctl start docker
```
Запросить статус процесса:
```bash
sudo systemctl status docker
```
Установить контейнеры (infra_db_1, infra_web_1, infra_nginx_1):
```bash
docker-compose up -d --build 
```
Выполнить миграции:
```bash
docker-compose exec web python manage.py migrate
```
Создать суперпользователя:
```bash
docker-compose exec web python manage.py createsuperuser
```
Собрать статику:
```bash
docker-compose exec web python manage.py collectstatic --no-input
```
Запольнить базу из копии:
```bash
docker-compose exec web python manage.py loaddata ../infra/fixtures.json 
```
Остановить контейнеры:
```bash
docker-compose down -v
```

### Шаблон наполнения .env (нет в репозитории) создаем в директории infra/.env
```python
DB_ENGINE=django.db.backends.postgresql
DB_NAME=postgres
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
DB_HOST=db
DB_PORT=5432
```

## Восстановление данных из резервной копии

Узнаем ** CONTAINER ID ** для контейнера с проектом Django:
```bash
sudo docker container ls -a
```

Копируем файл "fixtures.json" с фикстурами в контейнер:
```bash
sudo docker cp fixtures.json <CONTAINER ID>:/app
```

Применяем фикстуры:
```bash
sudo docker-compose exec web python manage.py loaddata fixtures.json
```

Удаляем файл "fixtures.json" из контейнера:

```bash
sudo docker exec -it <CONTAINER ID> bash
rm fixtures.json
exit
```

## Регистрация нового пользователя.

Получить код подтверждения на переданный **e-mail**. Права доступа: Доступно без токена. Использовать имя **'me'** в качестве **username** запрещено. Поля **email** и **username** должны быть **уникальными**.

### &#128221; _Регистрация нового пользователя:_
```http
POST /api/v1/auth/signup/

{
  "email": "string",
  "username": "string"
}
```
### &#128273; _Получение JWT-токена:_
```http
POST /api/v1/auth/token/

{
  "username": "string",
  "confirmation_code": "string"
}
```

## Примеры работы с API для авторизованных пользователей.
### &#10133; _Добавление категории:_
### добавлять имеет право только **Администратор**. ###
```http
POST /api/v1/categories/

{
  "name": "string",
  "slug": "string"
}
```

### &#10060 _Удаление категории:_
### удалять имеет право только **Администратор**. ###
```http
DELETE /api/v1/categories/{slug}/
```

### &#10133; _Добавление жанра:_
### добавлять имеет право только **Администратор**. ###
```http
POST /api/v1/genres/

{
  "name": "string",
  "slug": "string"
}
```

### &#10060 _Удаление жанра:_
### удалять имеет право только **Администратор**. ###
```http
DELETE /api/v1/genres/{slug}/
```

### &#128257; _Обновление публикации:_
```http
PUT /api/v1/posts/{id}/

{
"text": "string",
"image": "string",
"group": 0
}
```

### &#10133; _Добавление произведения:_
### с правами **Администратор**. **Нельзя** добавлять произведения, которые еще не вышли (год выпуска не может быть больше текущего). ###

```http
POST /api/v1/titles/

{
  "name": "string",
  "year": 0,
  "description": "string",
  "genre": "string",
  "category": "string"
}
```

### &#10133; _Добавление произведения:_
### доступно **без tokken** ###

```http
GET /api/v1/titles/{titles_id}/

{
  "id": 0,
  "name": "string",
  "year": 0,
  "rating": 0,
  "description": "string",
  "genre": [
    {
      "name": "string",
      "slug": "string"
    }
  ],
  "category": {
    "name": "string",
    "slug": "string"
  }
}
```

### &#128257; _Частичное обновление информации о произведении:_
### Имеет право **Администратор**. ###
```http
PATCH /api/v1/titles/{titles_id}/

{
  "name": "string",
  "year": 0,
  "description": "string",
  "genre": [
    "string"
  ],
  "category": "string"
}
```


## Примеры работы c пользователями.

### &#128209; _Получение списка всех пользователей._ ###
### Имеет право только **Администратор**. ###
```http
GET /api/v1/users/
```

### &#10133; _Добавление пользователя:_ ###
### Имеет право только **Администратор**. ###
### Поля email и username должны быть уникальными. ###

```http
POST /api/v1/users/

{
"username": "string",
"email": "user@example.com",
"first_name": "string",
"last_name": "string",
"bio": "string",
"role": "user"
}
```

### &#128209; _Получение данных о пользователе по username:_ ###
### Имеет право только **Администратор**. ###
```http
GET /api/v1/users/{username}/
```

### &#128221; _Изменение данных о пользователе по username:_ ###
### Имеет право только **Администратор**. ###
```http
PATCH /api/v1/users/{username}/

{
  "username": "string",
  "email": "user@example.com",
  "first_name": "string",
  "last_name": "string",
  "bio": "string",
  "role": "user"
}
```

### &#10060 _Удаление пользователя по username:_ ###
### Имеет право только **Администратор**. ###
```http
GET /api/v1/users/{username}/
```

### &#128526; _Получение данных своей учетной записи:_ ###
### Имеет право **любой авторизованный пользователь**. ###
```http
GET /api/v1/users/me/
```

### __Более подробная информация доступна__ по адресу: /redoc/ ###
