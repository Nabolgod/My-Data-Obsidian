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
  
    async def exists(self, **filter_by):  
		query = (  
            select(self.model)  
            .filter_by(**filter_by)  
        )  
        result = await self.session.execute(query)  
        return result.scalar_one_or_none() is not None  
  
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