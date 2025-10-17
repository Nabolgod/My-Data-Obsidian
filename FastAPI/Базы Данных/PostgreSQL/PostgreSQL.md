PostgreSQL — самая популярная СУБД. Она нужна нам, чтобы хранить данные и манипулировать ими.
### Установка PostgreSQL
- Windows: [https://selectel.ru/blog/tutorials/ustanovka-postgresql-15-windows/](https://selectel.ru/blog/tutorials/ustanovka-postgresql-15-windows)
- Ubuntu: [https://firstvds.ru/technology/ustanovka-postgresql-na-ubuntu](https://firstvds.ru/technology/ustanovka-postgresql-na-ubuntu)
- MacOS: [https://ploshadka.net/ustanovka-i-podkljuchenie-postgresql-na-mac-os/](https://ploshadka.net/ustanovka-i-podkljuchenie-postgresql-na-mac-os) или
```console_macOS
brew install postgresql
createuser -s postgres
brew services restart postgresql
```

Для работы с PostgreSQL можно выбрать среди [psycopg2](https://github.com/psycopg/psycopg2), [asyncpg](https://github.com/MagicStack/asyncpg), [aiopg](https://github.com/aio-libs/aiopg). Для синхронной работы с Postgres используется psycopg2, для асинхронной — обычно asyncpg, реже aiopg