#sql

Это, когда ввод данных пользователем содержит конструкции, которые могут незапланированно "внести изменения в базу данных".

Например пользователь в строке поиска вводит:
```sql
';DROP TABLE hotels; --'
```

Это проблема, которая возникает не на всех [[engine]], например на "**postgresql+asyncpg**":
```python
from sqlalchemy.ext.asyncio import async_sessionmaker, create_async_engine  
from src.config import settings  
from sqlalchemy.orm import DeclarativeBase  
from pydantic_settings import BaseSettings, SettingsConfigDict  


class Settings(BaseSettings):  
    DB_NAME: str  
    DB_PORT: int  
    DB_HOST: str  
    DB_USER: str  
    DB_PASS: str  
  
    @property  
    def DB_URL(self):  
        return f"postgresql+asyncpg://{self.DB_USER}:{self.DB_PASS}@{self.DB_HOST}:{self.DB_PORT}/{self.DB_NAME}"  
  
    model_config = SettingsConfigDict(env_file=".env")  
  
  
settings = Settings()
engine = create_async_engine(settings.DB_URL, echo=True)  
  
async_session_maker = async_sessionmaker(bind=engine, expire_on_commit=False)
```
<mark style="background: #FFF3A3A6;">asyncpg</mark> умный и не даёт реализовать SQL-инъекцию.

Проблема возникает на более старых или не таких прокаченных. Они начинают пропускать SQL-инъекции.

Для решения проблемы необходимо валидировать вводимые пользователем данные.