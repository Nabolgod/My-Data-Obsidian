#эндпоинт

[[POST]] - это добавление новой записи в БД.

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

Пример ручки через [[Репозиторий (DAO)]]:
```python
from sqlalchemy import insert  
from pydantic import BaseModel


class BaseRepository:  
    model = None  
  
    def __init__(self, session):  
        self.session = session  
        
        
    async def add(self, data: BaseModel):  
        """  
		Метод добавляет данные об сущности в базу данных
	    """        
	    add_data_stmt = (  
            insert(self.model)  
            .values(**data.model_dump())  
            .returning(self.model)  
        )  
        result = await self.session.execute(add_data_stmt)  
        return result.one()

@router.post("", summary="Добавить отель")  
async def create_hotel(hotel_data: Hotel):  
    async with async_session_maker() as session:  
        hotel = await HotelsRepository(session).add(hotel_data)  
        await session.commit()  
  
    return {"status": "Отель успешно добавлен!",  
            "data": hotel}
```

<mark style="background: #FF5582A6;">insert</mark>(self.model) - метод для работы с моделью данных (таблицей) для добавления новой записи.
<mark style="background: #FF5582A6;">values</mark>(**data.model_dump()) - преобразует Pydantic модель в словарь (поле: значение)
```python
from pydantic import BaseModel

class HotelCreate(BaseModel):
    name: str
    location: str
    stars: int

# Создаем объект Pydantic модели
hotel_data = HotelCreate(name="Hilton", location="Moscow", stars=5)

# model_dump() преобразует в словарь
dict_data = hotel_data.model_dump()
print(dict_data)
# Результат: {'name': 'Hilton', 'location': 'Moscow', 'stars': 5}
```
<mark style="background: #FF5582A6;">returning</mark>(self.model) - эта строчка говорит PostgreSQL: **"Верни мне данные вставленной записи"**. 
Данная запись возвращает ВСЕ колонки модели:
```SQL
-- SQL запрос который генерируется:
INSERT INTO hotels (name, location, stars) 
VALUES ('Hilton', 'Moscow', 5)
RETURNING hotels.id, hotels.name, hotels.location, hotels.stars;
```
Можно вернуть только определённые поля:
```python
.returning(self.model.id, self.model.name)  # Только ID и имя
```

Аналогия: представьте что вы заполнили анкету и отдали ее:
- **Без `returning()`** - вам говорят "спасибо", но не отдают копию
- **С `returning()`** - вам возвращают заполненную анкету с вашими данными

result.<mark style="background: #FF5582A6;">one</mark>() - возвращает нам одно значение.
