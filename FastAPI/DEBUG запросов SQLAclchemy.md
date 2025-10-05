Запросы можно продебажить несколькими способами:

### Первый способ
```python
from sqlalchemy.ext.asyncio import async_sessionmaker, create_async_engine
from src.config import settings
from sqlalchemy.orm import DeclarativeBase


engine = create_async_engine(settings.DB_URL, echo=True)
async_session_maker = async_sessionmaker(bind=engine, expire_on_commit=False)

class Base(DeclarativeBase):
	pass
```
Через флаг <mark style="background: #FFF3A3A6;">echo=True</mark>, то:
![[Снимок экрана 2025-10-04 в 22.48.53.png]]
Алхимия в консоли выводит информацию работы с БД. Тут можно увидеть строку сырого запроса, который вызывается в [[БД]].

## Второй способ
```python
async with async_session_maker() as session:
	add_hotel_stmt = alh.insert(HotelsORM).values(**hotel_data.model_dump())
	print(add_hotel_stmt) # 1 вариант
	print(add_hotel_stmt.compile(compile_kwargs={"literal_binds"=True})) # 2
	print(add_hotel_stmt.compile(bind= engine, compile_kwargs={"literal_binds"=True})) # 3
	await session.execute(add_hotel_stmt)
	await session.commit()
```
Через <mark style="background: #FFF3A3A6;">print(add_hotel_stmt)</mark>, где переменная является командой для бд.
Результат такого вывода:
![[Снимок экрана 2025-10-04 в 23.01.10.png]]

Через <mark style="background: #FFF3A3A6;">print(add_hotel_stmt.compile(compile_kwargs={"literal_binds"=True}))</mark>:
![[Снимок экрана 2025-10-04 в 23.09.11.png]]

Через <mark style="background: #FFF3A3A6;">print(add_hotel_stmt.compile(bind= engine, compile_kwargs={"literal_binds"=True}))</mark>:
![[Снимок экрана 2025-10-04 в 23.14.42.png]]