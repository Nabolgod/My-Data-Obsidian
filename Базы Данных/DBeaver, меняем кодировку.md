#кодировка

Для того, чтобы изменить кодировку в базе данных для работы с русской кириллицей используем команды:
```SQL
SET client_encoding = 'UTF8';
UPDATE pg_database SET datcollate='ru_RU.UTF-8', datctype='ru_RU' WHERE datname='название бд';
UPDATE pg_database set encoding = pg_char_to_encoding('UTF8') where datname = 'название бд';
```
