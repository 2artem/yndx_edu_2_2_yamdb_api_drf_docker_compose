# Проект YaMDb (Контейнеризация проекта, docker-compose)

(infra_sp2 - спринт 15)

## Ссылка на начальный проект (с описанием):

https://github.com/2artem/yndx_edu_2_1_yamdb_api_drf_command_project

## Установка docker-compose, можно посмотреть в  разделе "Запуск приложения", пункт 2.

https://github.com/2artem/yndx_edu_5_foodgram_api_backend_docker_cl_cd_gitactions_react

## Описание
Произведена упаковка в контейнер проекта YaMDb.

Файлы для развёртывания инфраструктуры находятся в папке infra/.

 * Dockerfile инструкция по созданию образа для контейнера.
 * docker-compose.yaml инструкция для запуска нескольких контейнеров.
 * settings.py указаны дефолтные значения для переменных из env-файла, ведь в env-файле хранится секретная информация, а ей не место в репозитории. Дефолтные значения нужны для успешного прохождения тестов на платформе.

## Контейнеризация проекта:

### Переменные окружения для работы с базой данных
Создайте файл ".env" в каталоге "infra_sp2/infra/.env"

Шаблон наполнения env-файла:
```
DB_ENGINE=django.db.backends.postgresql # указываем, что работаем с postgresql
DB_NAME=postgres # имя базы данных
POSTGRES_USER=postgres # логин для подключения к базе данных
POSTGRES_PASSWORD=postgres # пароль для подключения к БД (установите свой)
DB_HOST=db # название сервиса (контейнера)
DB_PORT=5432 # порт для подключения к БД 
```

### Команды для запуска приложения в контейнерах
В терминале, перейдите в каталог, в который будет загружаться приложение:
```
cd 
```
Клонируйте репозиторий:
```
git clone git@github.com:2artem/infra_sp2.git
```
*На данном этапе создайте env-файл по шаблону из раздела выше*

Далее перейдите в каталог приложения, папку инфраструктуры:
```
cd infra_sp2/infra/
```
Запустите docker-compose командой:
```
sudo docker-compose up -d
```
Выпоните миграции:
```
sudo docker-compose exec web python manage.py migrate
```
**Заполнить базу данных начальными данными (из резервной копии) можно по инструкции раздела ниже.**

Создайте суперюзера (логин\почта\пароль):
```
sudo docker-compose exec web python manage.py createsuperuser
```
Соберите статические файлы:
```
sudo docker-compose exec web python manage.py collectstatic --no-input 
```
*Теперь проект доступен по адресу http://localhost/.*


Остановить и удалить контейнеры, оставив образы:
```
sudo docker-compose down -v
```
### Команды для заполнения базы данных
Создать дамп (резервную копию) базы данных "fixtures.json" можно следующей командой:
```
sudo docker-compose exec web python manage.py dumpdata > fixtures.json
```
**Далее команды по востановлению базы данных из резервной копии.**
Узнаем CONTAINER ID для контейнера с джанго - "infra-web-1":
```
sudo docker container ls -a
```
Копируем файл "fixtures.json" с фикстурами в контейнер::
```
sudo docker cp fixtures.json <CONTAINER ID>:/app
```
Применяем фикстуры::
```
sudo docker-compose exec web python manage.py loaddata fixtures.json
```
Удаляем файл "fixtures.json" из контейнера:
```
sudo docker exec -it <CONTAINER ID> bash
rm fixtures.json
exit
```

