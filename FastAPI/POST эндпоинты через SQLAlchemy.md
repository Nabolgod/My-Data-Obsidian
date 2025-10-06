#эндпоинт

Пример эндпоинта:
```python
from fastapi import Query, FastAPI
import asyncio
from sqlalchemy import select
from src.database import async_session_maker
from src.models.hotels import HotelsORM


app= FastAPI()

@app.post("", summary="Добавить отель")  
async def create_hotel( ):  
    async with async_session_maker() as session:  
        add_hotel_stmt = alh.insert(HotelsORM).values(**hotel_data.model_dump())
        await session.execute(add_hotel_stmt)  
        await session.commit() 
    return {"status": "Отель успешно добавлен!"}
```
