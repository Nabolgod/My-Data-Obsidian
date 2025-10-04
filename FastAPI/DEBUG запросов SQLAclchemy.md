Запросы можно продебажить несколькими способами:
```python
from sqlalchemy.ext.asyncio import async_sessionmaker, create_async_engine
from src.config import settings
from sqlalchemy.orm import DeclarativeBase


engine = create_async_engine(settings.DB_URL, echo=True)
async_session_maker = async_sessionmaker(bind=engine, expire_on_commit=False)

class Base(DeclarativeBase):
	pass
```
Через флаг <mark style="background: #FFF3A3A6;">echo=True</mark>, то 