# Групповой проект YaMDb собирает отзывы пользователей на произведения.

https://github.com/strekozjulia/yamdb_final/actions/workflows/yamdb_workflow.yml/badge.svg

## Описание:
Проект YaMDb собирает отзывы пользователей на произведения. Сами произведения в YaMDb не хранятся, здесь нельзя посмотреть фильм или послушать музыку.
Произведения делятся на категории, такие как «Книги», «Фильмы», «Музыка». Например, в категории «Книги» могут быть произведения «Винни-Пух и все-все-все» и «Марсианские хроники», а в категории «Музыка» — песня «Давеча» группы «Жуки» и вторая сюита Баха. Список категорий может быть расширен (например, можно добавить категорию «Изобразительное искусство» или «Ювелирка»).

Произведению может быть присвоен жанр из списка предустановленных (например, «Сказка», «Рок» или «Артхаус»).

Добавлять произведения, категории и жанры может только администратор.

Благодарные или возмущённые пользователи оставляют к произведениям текстовые отзывы и ставят произведению оценку в диапазоне от одного до десяти (целое число); из пользовательских оценок формируется усреднённая оценка произведения — рейтинг (целое число). На одно произведение пользователь может оставить только один отзыв.
Пользователи могут оставлять комментарии к отзывам.

Добавлять отзывы, комментарии и ставить оценки могут только аутентифицированные пользователи.

## Регистрация пользователей:
1. Пользователь отправляет POST-запрос с параметрами username и email на эндпоинт /api/v1/auth/signup/.
2. YaMDB отправляет письмо с кодом подтверждения (confirmation_code) на указанный email адрес.
3. Пользователь отправляет POST-запрос с параметрами email и confirmation_code на эндпоинт /api/v1/auth/token/,
после чего польватель получает JWT token. Для авторизации вы должны передавать ключ в заголовке: \
key - "Authorization", value "Bearer и ваше значение token" 

### Пользователя может создавать администратор.
1. Администратор отправляет POST-запрос с параметрами username и email на эндпоинт /api/v1/users/. \
При создании пользователя не предполагается автоматическая отправка письма пользователю с кодом подтверждения.
2. После этого пользователь должен самостоятельно отправить свой email и username на эндпоинт /api/v1/auth/signup/.
3. YaMDB отправляет письмо с кодом подтверждения (confirmation_code) на указанный email адрес.
4. Далее пользователь отправляет POST-запрос с параметрами username и confirmation_code на эндпоинт /api/v1/auth/token/, в ответе на запрос ему приходит JWT-токен, как и при самостоятельной регистрации.


## Пользовательские роли и права доступа.
- Аноним — может просматривать описания произведений, читать отзывы и комментарии.
- Аутентифицированный пользователь (user) — может читать всё, как и Аноним, может публиковать отзывы и ставить оценки произведениям (фильмам/книгам/песенкам), может комментировать отзывы; может редактировать и удалять свои отзывы и комментарии, редактировать свои оценки произведений. Эта роль присваивается по умолчанию каждому новому пользователю.
- Модератор (moderator) — те же права, что и у Аутентифицированного пользователя, плюс право удалять и редактировать любые отзывы и комментарии.
- Администратор (admin) — полные права на управление всем контентом проекта. Может создавать и удалять произведения, категории и жанры. Может назначать роли пользователям.
- Суперюзер Django обладает правами администратора, пользователя с правами admin. 

## Ресурсы API YaMDb
* Ресурс auth: аутентификация.
* Ресурс users: пользователи.
* Ресурс titles: произведения, к которым пишут отзывы (определённый фильм, книга или песенка).
* Ресурс categories: категории (типы) произведений («Фильмы», «Книги», «Музыка»). Одно произведение может быть привязано только к одной категории.
* Ресурс genres: жанры произведений.
* Ресурс reviews: отзывы на произведения.
* Ресурс comments: комментарии к отзывам.

## Технологии:
Python, Django, DRF, JWT.

## Переменные окружения прописываются в файле .env в соответствии с шаблоном:

DB_ENGINE=django.db.backends.postgresql
DB_NAME=<имя_базы_данных>
POSTGRES_USER=<логин_для_подключения_к_БД>
POSTGRES_PASSWORD=<пароль_для_подключения_к_БД>
DB_HOST=<название_сервиса_БД>
DB_PORT=<порт_для_подключения_к_БД> 



## Как запустить проект из контейнера:

Клонировать репозиторий и перейти в командной строке в папку с инфраструктурой:
```
git clone https://github.com/StrekozJulia/infra_sp2.git
cd api_yamdb/infra
```

Развернуть проект в контейнерах:
```
docker-compose up
```

Выполнить миграции:
```
docker-compose exec web python manage.py migrate
```

Наполнить базу данных из дампа:
```
docker-compose exec web python manage.py loaddata fixtures.json
```

## Документация:
Доступна по адресу /redoc/

## Доступные запросы к API:

+ http://127.0.0.1.8000/api/v1/categories/ - просмотр и создание категорий
    Тип запроса: GET (доступно без токена), POST (администратор)
    Передаваемые данные (POST): {"name": string, 
                                 "slug": string}

+ http://127.0.0.1.8000/api/v1/categories/{slug}/ - удаление категории
    Тип запроса: DELETE (администратор)

+ http://127.0.0.1.8000/api/v1/genres/ - просмотр и создание жанров
    Тип запроса: GET (доступно без токена), POST (администратор)
    Передаваемые данные (POST): {"name": string, 
                                 "slug": string}

+ http://127.0.0.1.8000/api/v1/genres/{slug}/ - удаление жанра
    Тип запроса: DELETE (администратор)

+ http://127.0.0.1.8000/api/v1/titles/ - просмотр и создание произведений
    Тип запроса: GET (доступно без токена), POST (администратор)
    Передаваемые данные (POST): {"name": string,
                                 "year": integer, 
                                 "genre": array of strings,
                                 "category": string,
                                 "description": string}

+ http://127.0.0.1.8000/api/v1/titles/{title_id} - просмотр, редактирование и удаление произведений
    Тип запроса: GET (доступно без токена), PATCH, DELETE (администратор)
    Передаваемые данные (PATCH): {"name": string,
                                 "year": integer, 
                                 "genre": array of strings,
                                 "category": string,
                                 "description": string}


## Авторы:
- [Илиан Ляпота Тимлид, Python-разработчик](https://github.com/IlianL)
- [Юлия Доманова Python-разработчик](https://github.com/StrekozJulia)
- [Александр Рассыхаев Python-разработчик](https://github.com/Leenominai)
