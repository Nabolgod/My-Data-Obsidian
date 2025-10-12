Это слой приложения, который разделяет объекты внутри памяти (в нашем случае [[pydantic]]-схемы) от объектов базы данных (в нашем случае это модели [[SQLAlchemy]])

Если по-простому, то DataMapper - это python-class, который умеет из pydantic-схемы делать SQLAlchemy-модель и наоборот.

Часто находится рядом с [[Репозиторий (DAO)]] 
Пример схемы:
![[Pasted image 20251012144848.png]]
![[Pasted image 20251012144932.png]]![[Pasted image 20251012144947.png]]
## Проблематика нашего текущего проекта без DataMapper:

### Первая
Во всех методах мы возвращаем сущности базы данных (объекты HotelORM), и соответсвенно внутри ручек мы работаем с объектами базы данных ([[SQLAlchemy]]).

В базе данных из-за табличной структуры мы можем только ограниченно описать эти данные (строки, столбцы), а в Python можно описывать данные, как угодно.

Итог: то, как мы представляем данные в бизнес логике (python) могут сильно отличаться от того, как они хрянятся в бд.
```python
class BaseRepository:  
    model = None  
  
    def __init__(self, session):  
        self.session = session  
  
    async def get_all(self, *args, **kwargs):  
        query = select(self.model)  
        result = await self.session.execute(query)  
        return result.scalars().all()  
  
    async def get_on_or_none(self, **filter_by):  
		query = select(self.model).filter_by(**filter_by)  
        result = await self.session.execute(query)  
        return result.scalars().one_or_none()  
  
    async def add(self, data: BaseModel):  
		add_data_stmt = (  
            insert(self.model)  
            .values(**data.model_dump())  
            .returning(self.model)  
        )  
        result = await self.session.execute(add_data_stmt)  
        return result.scalars().one()  
```
### Вторая
Риск работы с объектами базы данных (HotelsORM) из-за возможности незапланированного изменения этих данных.

## Решение проблем - использование DataMapper

Будет настроена работа исключительно с данными, которыми можно пользоваться как угодно при этом не будет заблокировано соедение с бд и не будет случайного внесения данных в бд.

В нашем проекте это позволит сделать <mark style="background: #FFB86CA6;">Pydantic - схема</mark>.

Если коротко, то мы хотим, чтобы со стороны бизнес логики отправлялись pydantic-схемы, которы будут содеражть в себе просто данные и в конечном итоге, чтобы ответом от репозитория была та же pydantic-схема с данными.
## Перепишем проект под базовый DataMapper

### Первое
Создадим схему Hotel, которая будет соответсовать модели HotelsORM:
```python
from src.database import Base  
from sqlalchemy.orm import Mapped, mapped_column  
from sqlalchemy import String  
  
  
class HotelsORM(Base):  
    __tablename__ = "hotels"  
  
    id: Mapped[int] = mapped_column(primary_key=True)  
    title: Mapped[str] = mapped_column(String(length=100))  
    location: Mapped[str]


from pydantic import BaseModel, Field, ConfigDict
  
  
class HotelAdd(BaseModel):  
    title: str  
    location: str  
  
class Hotel(HotelAdd):  
    id: int
    
    model_config = ConfigDict(from_attributes=True) # можно указать так, чтобы каждый раз не указывать from_attributes=True в примерах ниже, но делать так не стоит.
```
### Второе
В классе репозитория создадим атрибут sheme, который будет содеражать в себе pydantic-схему:
```python
class BaseRepository:  
    model = None  
    scheme: BaseModel = None


class HotelsRepository(BaseRepository):  
    model = HotelsORM  
    scheme = Hotel
```
### Третье 
Реализуем преобразование объекта HotelsORM в pydantic-схему Hotel.
```python
class BaseRepository:  
    model = None 
    scheme: BaseModel = None
  
    def __init__(self, session):  
        self.session = session  
  
    async def get_all(self, *args, **kwargs):  
        query = select(self.model)  
        result = await self.session.execute(query)  
        return ([
	        self.scheme.model_validate(model, from_attributes=True) for model 
	        in result.scalars.all()
        ])
  
    async def get_on_or_none(self, **filter_by):  
		query = select(self.model).filter_by(**filter_by)  
        result = await self.session.execute(query) 
        model = result.scalars().one_or_none()
        if model is None:
	        return None
	    return self.scheme.model_validate(model, from_attributes=True)
  
    async def add(self, data: BaseModel):  
		add_data_stmt = (  
            insert(self.model)  
            .values(**data.model_dump())  
            .returning(self.model)  
        )  
        result = await self.session.execute(add_data_stmt)  
        model = result.scalars().one()
        return self.scheme.model_validate(model, from_attributes=True)
```
Для того, чтобы сделать из модели pydantic-схему воспользуемся методом <mark style="background: #FFB86CA6;">model_validate</mark>.

<mark style="background: #FFF3A3A6;">from_attributes=True</mark> - ключевой параметр функции model_validate, который позволит достать аттрибуты модели в виде словаря.