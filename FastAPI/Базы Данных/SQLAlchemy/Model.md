Находится в папке под названием model и содержит в себе модель базы данных.

Для работы с моделями нужен один необычный класс <mark style="background: #ADCCFFA6;">DeclarativeBase</mark> (указываем его в файле [[database]]):
```python
from sqlalchemy.orm import DeclarativeBase
from sqlalchemy.ext.asyncio import async_sessionmaker, create_async_engine

engine = create_async_engine("Тут мы указываем URL-базы_данных")

async_session_maker = async_sessionmaker(bind=engine, expire_on_commit=False)

class Base(DeclarativeBase):
	metadata = ... 
```

<mark style="background: #ABF7F7A6;">sqlalchemy.orm</mark> - используется для работы с питоновскими классами
<mark style="background: #ABF7F7A6;">class Base(DeclarativeBase)</mark> - создаём класс Base и наследуем от DeclarativeBase, название Base рекомендуется использовать только для этого. Используем его для того, чтобы наследовать от него все будущие модели.
<mark style="background: #ABF7F7A6;">metadata</mark> - атрибут, который мы не перезаписываем. В нём будет содержаться информация обо всех моделях (после [[Миграции Alembic]])

Создаём класс нашего объекта [[ORM]] (указываем их в отдельном файле наших сущностей):
```python
from src.database import Base
from sqalchemy.orm import Mapped, mapped_column
from sqlalchemy import String

class HotelsORM(Base):
	__tablename__ = "hotels"
	
	# указываем столбцы нашей таблицы
	id: Mapped[int] = mapped_column(primary_key=True)
	title: Mapped[str] = mapped_column(String(length=100))
	location: Mapped[str] = mapped_column()
```

<mark style="background: #ABF7F7A6;">__tablename__ = "название"</mark> - обязательно указываем название нашей модели
<mark style="background: #ABF7F7A6;">Mapped[]</mark> - специальный синтаксис, который будет переводить питоновский тип данных в SQL
<mark style="background: #ABF7F7A6;">mapped_column()</mark> - синтаксис для указания каких-то свойст поля в SQL
<mark style="background: #ABF7F7A6;">String(length=100)</mark> - импортируем тип используемый вунтри mapped_column для задания ограничения длины строки

В таблице обязательно должен быть первичный ключ:
<mark style="background: #ABF7F7A6;">mapped_column(primary_key=True)</mark> - указание того, что поле является первичным ключом

Преимущество, что мы можем создать на бекенде модель базы данных на python, а потом эту модель уже переводим в базу данных.
Порядок всегда такой.

Мы создали модель сущности, которая будет отображать реальную таблицу в базу данных.

Создадим вторую модель, которая будет связана с HotelsORM через внешний ключ:
```python
from src.database import Base
from sqalchemy.orm import Mapped, mapped_column
from sqlalchemy import String, ForeignKey

class RoomsORM(Base):
	__tablename__ = "rooms"
	
	id: Mapped[int] = mapped_column(primary_key=True)
	hotel_id: Mapped[int] = mapped_column(ForeignKey("hotel.id"))
	title: Mapped[str] = mapped_column()
	description: Mapped[str | None] = mapped_column(nullable=True) # одно и то же
	price: Mapped[int] = mapped_column()
	quantity: Mapped[int] = mapped_column()
```

<mark style="background: #ABF7F7A6;">ForeignKey("hotel.id")</mark> - тип внешнего ключа, внутри скобок указывается название_таблицы и поля, которое будет внешним ключом.

Параметры бывают опциональные, например **description**, указывается через \[str | None\] или через nullable=True.