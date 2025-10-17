Не нужно открывать на большой срок, лучше использовать только для запросов к бд, для получения или изменения данных.

Session использует подключение для работы и занимает его, поэтому чем быстрее она обработает запрос и вернёт свободное подключение, тем лучше.
```python
import asyncio  
import sqlalchemy as alh
from src.database import async_session_maker 


async with async_session_maker() as session:  
    add_hotel_stmt = alh.insert(HotelsORM).values(**hotel_data.model_dump())
    await session.execute(add_hotel_stmt)  
    await session.commit()
```

Почему важно делать <mark style="background: #FF5582A6;">session.commit</mark>() в конце транзакции, а не например внутри метода [[Репозиторий (DAO)]].

Логика простая, внутри одной сессии может быть несколько транзакций:
```python
async with async_session_maker() as session:
    # Транзакция 1
    async with session.begin():
        hotel1 = Hotel(name="Hilton")
        session.add(hotel1)
    
    # Транзакция 2  
    async with session.begin():
        hotel2 = Hotel(name="Marriott")
        session.add(hotel2)
```
Коммит говорит о завершении транзакции и соответсвенно говорит об успешном завершении работы. Если ошибка произойдёт внутри репозитория, то внутри ручки остальные транзакции могут быть не выполнены и данные в бд будут неконсистентныe.

