Перед началом, можно создать дополнительную базу данных в DBeaver под любым названием, которая будет использоваться конкретно в проекте.

Для начала создаём основной файл для работы с подключением в БД.

Внутри [[src]] мы создаём файл [[database]].py:
```python
from sqlalchemy.ext.asyncio import async_sessionmaker, create_async_engine

engine = create_async_engine("Тут мы указываем URL-базы_данных")
```

<mark style="background: #ABF7F7A6;">from sqlalchemy.ext.asyncio</mark> - для работы с асинхронный sqlalchemy (ext от слова extension-расширение)
<mark style="background: #ABF7F7A6;">async_sessionmaker</mark> - для создания [[Session]]
<mark style="background: #ABF7F7A6;">create_async_engine</mark> - для создания асинхронного движка [[Engine]], в него мы помещаем URL из [[Переменные окружения#^2bb4c4]]

Функция для проверки работы движка с использованием сырых SQL-запросов:
```python
from sqlalchemy import text

async def func():
	async with engine.begin() as conn:
		res = await conn.execute(text("SELECT version()"))
		print(res.fetchone())

asyncio.run(func())
```

<mark style="background: #ADCCFFA6;">engine.begin()</mark> - пожалуйста начни транзакцию
<mark style="background: #ADCCFFA6;">text("SELECT version()")</mark> - показать текущую версию, оборачиваем в text из sqlalchemy
<mark style="background: #ADCCFFA6;">res</mark> - будет содержать итератор, который вернётся от запроса
<mark style="background: #ADCCFFA6;">res.fetchone()</mark> - просим показать одну строчку того, что вернули
### async_sessionmaker 
Это фабрика для создания [[Session]], каждый объект сессии будет по сути являться [[Транзакция]] в базу данных.