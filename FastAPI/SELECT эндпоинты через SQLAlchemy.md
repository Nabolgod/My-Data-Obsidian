Пример эндпоинта:
```python
from fastapi import Query, FastAPI
import asyncio
from sqlalchemy import select
from src.database import async_session_maker
from src.models.hotels import HotelsORM


app= FastAPI()

@app.get("/hotels", summary="Вернуть информацию об отелях")  
async def get_hotels():  
    async with async_session_maker() as session:
	    query = select(HotelORM) 
	    await session.execute(query)
```

<mark style="background: #ABF7F7A6;">from sqlalchemy import select</mark> - импортируем функцию SELECT для возврата значений из базы данных.

Хороший пример явного указания полей на практике, чтобы было проще проводить [[DEBUG запросов SQLAclchemy]]
```SQL
SELECT id, title, location
FROM hotels;
```
Плохой пример, т.к. база отработет без ошибок и тяжелее будет понять ошибку со стороны бд.
```SQL
SELECT **
FROM hotels;
```

Для работы потребуется объект [[Session]]:
```python
async def get_hotels():  
    async with async_session_maker() as session:
	    query = select(HotelORM) # весь запрос, который работает с моделью данных
	    result = await session.execute(query)
```
<mark style="background: #BBFABBA6;">query</mark> = select(HotelORM) - именование query используется для работы с запросами SELECT, в иных случаях используется именование <mark style="background: #BBFABBA6;">stmt</mark> (statemate), которое обозначает изменения. Например в [[POST эндпоинты через SQLAlchemy]] 

В <mark style="background: #FFB86CA6;">result</mark> вернётся не таблица, а питоновский объект, который вернёт [[Engine]]. (итератор)
```python
print(type(result), result)

Вывода будет следующий:
<class 'sqlalchemy.engine.result.ChunkedIteratorResult'> <sqlalchemy.engine.result.ChunkedIteratorResult object at 0x00000261D6CD6250>

Из этого итератора можно достать данные:

hotels = result.all() # все отели
first_hotel = result.first() # первый отель
is_one = result.one() # вернётся только один объект, если он один, иначе ошибка
is_one_or_none = result.one_or_none() # если результата нет или вернулась одна строчка, то всё окей без ошибок, а если больше одной, то ошибка
```

Для работы с отелями, сначала посмотрим, что в них содержится:
```python
async def get_hotels():  
    async with async_session_maker() as session:
	    query = select(HotelORM) 
	    result = await session.execute(query)
	    hotels = result.all()
	    print(type(hotels), hotels)

Вывод в консоль:
<class 'list'> 
[(<src.models.hotels.HotelsORM object at 0x000002A4B1A49400>,), (<src.models.hotels.HotelsORM object at 0x000002A4B1A49430>,), ...]
		
		hotels = result.scalars().all()
	    print(type(hotels), hotels)

Вывод в консоль:
<class 'list'> 
[<src.models.hotels.HotelsORM object at 0x0000025F3E898050>, <src.models.hotels.HotelsORM object at 0x0000025F3E898590>]
		
		retrun hotels

Если вернуть отели, то в запрос отработает как следует и вернёт json-файл, который FastAPI конвертирует под капотом, что не есть хорошо.
```
Возвращается список объектов (кортеж из одного элемента).
hotels = result<mark style="background: #BBFABBA6;">.scalars()</mark>.all() - функция scalars() достанет из этих кортежей первый элемент.

При [[DEBUG запросов SQLAclchemy]] через echo=True в консоли:
```python
2025-10-06 01:39:15,557 INFO sqlalchemy.engine.Engine select pg_catalog.version()
2025-10-06 01:39:15,557 INFO sqlalchemy.engine.Engine [raw sql] ()
2025-10-06 01:39:15,558 INFO sqlalchemy.engine.Engine select current_schema()
2025-10-06 01:39:15,558 INFO sqlalchemy.engine.Engine [raw sql] ()
2025-10-06 01:39:15,558 INFO sqlalchemy.engine.Engine show standard_conforming_strings
2025-10-06 01:39:15,559 INFO sqlalchemy.engine.Engine [raw sql] ()
2025-10-06 01:39:15,559 INFO sqlalchemy.engine.Engine BEGIN (implicit)
2025-10-06 01:39:15,560 INFO sqlalchemy.engine.Engine SELECT hotels.id, hotels.title, hotels.location
FROM hotels
2025-10-06 01:39:15,560 INFO sqlalchemy.engine.Engine [generated in 0.00012s] ()
2025-10-06 01:39:15,562 INFO sqlalchemy.engine.Engine ROLLBACK
```
<mark style="background: #BBFABBA6;">ROLLBACK</mark> - означает, что транзакция откатывается и коммит при этом не требуется в SELECT запросах.