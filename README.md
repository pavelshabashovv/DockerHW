запуск:

Сделать .env по примеру .env.example (либо вообще ниче не делать, если достаточно дефолтных настроек)

docker compose up -d --build

остановка:

docker compose down

настройки в .env:

PORT - порт
GREETING - приветствие

в compose.yaml два сервиса: app и redis. app работает с redis (STORAGE=redis), данные redis лежат в volume redis-data