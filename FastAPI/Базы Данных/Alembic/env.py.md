
```python
from logging.config import fileConfig  
  
from sqlalchemy import engine_from_config  
from sqlalchemy import pool  
  
from alembic import context  
  
from src.config import settings  
from src.database import Base  
from src.models.hotels import HotelsORM  
from src.models.rooms import RoomsORM  
  
# this is the Alembic Config object, which provides  
# access to the values within the .ini file in use.  
config = context.config  
config.set_main_option("sqlalchemy.url", f"{settings.DB_URL}?async_fallback=True")  
  
# Interpret the config file for Python logging.  
# This line sets up loggers basically.  
if config.config_file_name is not None:  
    fileConfig(config.config_file_name)  
  
# add your model's MetaData object here  
# for 'autogenerate' support  
# from myapp import mymodel  
# target_metadata = mymodel.Base.metadata  
target_metadata = Base.metadata  
  
  
# other values from the config, defined by the needs of env.py,  
# can be acquired:  
# my_important_option = config.get_main_option("my_important_option")  
# ... etc.  
  
  
def run_migrations_offline() -> None:  
    """Run migrations in 'offline' mode.  
  
    This configures the context with just a URL    and not an Engine, though an Engine is acceptable    here as well.  By skipping the Engine creation    we don't even need a DBAPI to be available.  
    Calls to context.execute() here emit the given string to the    script output.  
    """    url = config.get_main_option("sqlalchemy.url")  
    context.configure(  
        url=url,  
        target_metadata=target_metadata,  
        literal_binds=True,  
        dialect_opts={"paramstyle": "named"},  
    )  
  
    with context.begin_transaction():  
        context.run_migrations()  
  
  
def run_migrations_online() -> None:  
    """Run migrations in 'online' mode.  
  
    In this scenario we need to create an Engine    and associate a connection with the context.  
    """    connectable = engine_from_config(  
        config.get_section(config.config_ini_section, {}),  
        prefix="sqlalchemy.",  
        poolclass=pool.NullPool,  
    )  
  
    with connectable.connect() as connection:  
        context.configure(  
            connection=connection, target_metadata=target_metadata  
        )  
  
        with context.begin_transaction():  
            context.run_migrations()  
  
  
if context.is_offline_mode():  
    run_migrations_offline()  
else:  
    run_migrations_online()
```

<mark style="background: #ABF7F7A6;">target_metadata = Base.metadata</mark> - самая важная часть этого файла, тут мы передаём параметр metadata  Base - класса из [[database]] для наших [[Model]]. 

<mark style="background: #FF5582A6;">КРАЙНЕ ВАЖНО</mark>
Если просто импортировать
```python
from src.database import Base
```
то этого будет недосаточно, т.к. env не будет видеть модели наших сущностей и наследования от Base не будет происходить. Их мы также отдельно импортируем
```python
from src.models.hotels import HotelsORM  
from src.models.rooms import RoomsORM 
```

Необходимо переопределить [[config]], чтобы не было ошибки с [[alembic.ini]] в этом нам помогут [[Переменные окружения]]:
```python
from src.config import settings

config = context.config  
config.set_main_option("sqlalchemy.url", f"{settings.DB_URL}?async_fallback=True")
```
<mark style="background: #ABF7F7A6;">sqlalchemy.url</mark> - название параметра, который переопределяем (он отображается в [[alembic.ini#^ef44bc]]).
?async_fallback=True - параметр, чтобы избежать проблем с асинхронными движками в [[Alembic]]. Т.к. миграции процесс синхронный и занимает время, а это нам позволяет движку asyncpg работать в синхронном режиме. 