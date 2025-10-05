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
