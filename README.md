#  Как работать с репозиторием финального задания

## Что нужно сделать

Настроить запуск проекта Kittygram в контейнерах и CI/CD с помощью GitHub Actions

## Как проверить работу с помощью автотестов

В корне репозитория создайте файл tests.yml со следующим содержимым:
```yaml
repo_owner: ваш_логин_на_гитхабе
dockerhub_username: ваш_логин_на_докерхабе
```

Скопируйте содержимое файла `.github/workflows/main.yml` в файл `kittygram_workflow.yml` в корневой директории проекта.

Для локального запуска тестов создайте виртуальное окружение, установите в него зависимости из backend/requirements.txt и запустите в корневой директории проекта `pytest`.

## Чек-лист для проверки перед отправкой задания

- Пуш в ветку main запускает тестирование Kittygram, а после вам приходит сообщение в телеграм.
- В корне проекта есть файл `kittygram_workflow.yml`.

1. Создай файл .env в корне проекта
Скопируй из примера и заполни:
bashcp .env.example .env
Минимальное содержимое .env:
POSTGRES_DB=kittygram
POSTGRES_USER=kittygram_user
POSTGRES_PASSWORD=kittygram_password
DB_HOST=db
DB_PORT=5432
SECRET_KEY=any-random-string-here
DEBUG=True
ALLOWED_HOSTS=localhost 127.0.0.1
2. Собери и запусти контейнеры
bashdocker compose up --build
Первый раз это займёт несколько минут — будут собираться образы. Следи за логами в терминале. Когда увидишь что-то вроде gateway | ... start worker processes — всё готово.
3. Проверь в браузере

Фронтенд: http://localhost:9000
Админка Django: http://localhost:9000/admin/
API: http://localhost:9000/api/

Чтобы войти в админку, нужно создать суперпользователя. В отдельном терминале:
bashdocker compose exec backend python manage.py createsuperuser
4. Быстрая проверка через curl
bash# API должен отвечать (401 — норма, значит бэкенд работает)
curl -i http://localhost:9000/api/cats/

# Фронтенд — должен вернуть HTML
curl -s http://localhost:9000 | head -5
5. Посмотреть логи конкретного контейнера, если что-то не работает
bashdocker compose logs backend
docker compose logs gateway
docker compose logs db
6. Остановить проект
bash# Остановить (данные сохранятся в volumes)
docker compose down

# Остановить и удалить все данные (чистый старт)
docker compose down -v
Частые проблемы:

Если бэкенд падает с ошибкой подключения к БД — подожди 5–10 секунд, PostgreSQL стартует медленнее, чем Django пытается подключиться. Можно перезапустить: docker compose restart backend
Если порт 9000 занят — поменяй в docker-compose.yml строку 9000:80 на, например, 8080:80

fff