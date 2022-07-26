# Описание проекта YaMDb
Проект YaMDb собирает отзывы (Review) пользователей на произведения (Title). Произведения делятся на категории: "Книги", "Фильмы", "Музыка". Список категорий (Category) может быть расширен.
Сами произведения в YaMDb не хранятся, здесь нельзя посмотреть фильм или послушать музыку.
В каждой категории есть произведения: книги, фильмы или музыка. Например, в категории "Книги" могут быть произведения "Винни Пух и все-все-все" и "Марсианские хроники", а в категории "Музыка" — песня "Давеча" группы "Насекомые" и вторая сюита Баха. Произведению может быть присвоен жанр из списка предустановленных (например, "Сказка", "Рок" или "Артхаус"). Новые жанры может создавать только администратор.
Благодарные или возмущённые читатели оставляют к произведениям текстовые отзывы (Review) и выставляют произведению рейтинг.

# Список технологий:
Python 3.7, Django, DRF, Simple-JWT, PostgreSQL, Docker, nginx, gunicorn.

# Техническое описание проекта YaMDb
К проекту по адресу http://localhost/redoc/ подключена документация API YaMDb. В ней описаны возможные запросы к API и структура ожидаемых ответов. Для каждого запроса указаны уровни прав доступа: пользовательские роли, которым разрешён запрос.

# Пользовательские роли
**Аноним**: может просматривать описания произведений, читать отзывы и комментарии.

**Аутентифицированный пользователь (user)**: может читать всё, как и Аноним, может публиковать отзывы и ставить оценки произведениям (фильмам/книгам/песенкам), может комментировать отзывы; может редактировать и удалять свои отзывы и комментарии, редактировать свои оценки произведений. Эта роль присваивается по умолчанию каждому новому пользователю.

**Модератор (moderator)**: те же права, что и у Аутентифицированного пользователя, плюс право удалять и редактировать любые отзывы и комментарии.

**Администратор (admin)**: полные права на управление всем контентом проекта. Может создавать и удалять произведения, категории и жанры. Может назначать роли пользователям.

**Суперюзер Django**: должен всегда обладать правами администратора, пользователя с правами admin. Даже если изменить пользовательскую роль суперюзера — это не лишит его прав администратора. Суперюзер — всегда администратор, но администратор — не обязательно суперюзер.

# Самостоятельная регистрация новых пользователей
__Пользователь__ отправляет POST-запрос с параметрами email и username на эндпоинт /api/v1/auth/signup/.

__Сервис YaMDB__ отправляет письмо с кодом подтверждения (confirmation_code) на указанный адрес email.

__Пользователь__ отправляет POST-запрос с параметрами username и confirmation_code на эндпоинт /api/v1/auth/token/, в ответе на запрос ему приходит token (JWT-токен).

# Установка
## Шаг 1. Установите Docker (если он отсутствует)
Прежде чем приступать к работе, убедиться что Docker установлен, для этого ввести команду:
```bash
docker -v
```
Если команда укажет на его отсутствие скачайте [Docker Desktop](https://www.docker.com/products/docker-desktop) для Mac или Windows. [Docker Compose](https://docs.docker.com/compose) будет установлен автоматически. В Linux убедитесь, что у вас установлена последняя версия [Compose](https://docs.docker.com/compose/install/). Также вы можете воспользоваться официальной [инструкцией](https://docs.docker.com/engine/install/).

## Шаг 2. Клонируйте репозиторий себе на компьютер
Введите команду:
```bash
git clone https://github.com/DumaDim/infra_sp2.git
```

## Шаг 3. Создайте в клонированной директории файл .env используя следующий шаблон:
Пример:
```bash
DB_ENGINE=django.db.backends.postgresql
DB_NAME=postgres
POSTGRES_USER=postgres
POSTGRES_PASSWORD=postgres
DB_HOST=db
DB_PORT=5432
```

## Шаг 4. Запуск docker-compose
Для запуска необходимо выполнить из директории с проектом команду:
```bash
docker-compose up -d
```

## Шаг 5. Заполнение Базы данных
Создаем и выполняем миграции:
```bash
docker-compose exec web python manage.py makemigrations --noinput
docker-compose exec web python manage.py migrate --noinput
```

## Шаг 6. Подгружаем статику
Выполните команду:
```bash
docker-compose exec web python manage.py collectstatic --no-input 
```

## Шаг 7. Заполнение базы тестовыми данными
Для заполнения базы тестовыми данными вы можете использовать файл fixtures.json, который находится в infra_sp2. Выполните команду:
```bash
docker-compose exec web python manage.py loaddata fixtures.json
```

## Другие команды
Создание суперпользователя:
```bash
docker-compose exec web python manage.py createsuperuser
```

Остановить работу всех контейнеров можно командой:
```bash
docker-compose down
```

Для пересборки и запуска контейнеров воспользуйтесь командой:
```bash
docker-compose up -d --build 
```

Мониторинг запущенных контейнеров:
```bash
docker stats
```

Останавливаем и удаляем контейнеры, сети, тома и образы:
```bash
docker-compose down -v
```
