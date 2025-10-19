Две важные консольные команды, чтобы накатить изменения в БД.

После создания модели данных:

```python
from src.database import Base  
from sqlalchemy.orm import Mapped, mapped_column  
from sqlalchemy import ForeignKey, String  
  
  
class RoomsORM(Base):  
    __tablename__ = "rooms"  
  
    id: Mapped[int] = mapped_column(primary_key=True)  
    hotel_id: Mapped[int] = mapped_column(ForeignKey("hotels.id"))  
    title: Mapped[str] = mapped_column(String(length=100))  
    description: Mapped[str | None]  
    price: Mapped[int]  
    quantity: Mapped[int]

```
С помощью консольной команды:
	*alembic revision --autogenerate -m "Текст коммита"*

<mark style="background: #FFF3A3A6;">revision --autogenerate</mark> - этот флаг позволяет [[Alembic]] сравить состояние базы данных и кодовой базы.
Мы таким образом генерируем новую [[Миграции]]

Вид миграции:
```python
"""initial migration rooms  
  
Revision ID: 8fc1a940c8a7  
Revises: 7f85d342381e  
Create Date: 2025-10-03 10:16:18.148627  
  
"""  
from typing import Sequence, Union  
  
from alembic import op  
import sqlalchemy as sa  
  
revision: str = '8fc1a940c8a7'  
down_revision: Union[str, Sequence[str], None] = '7f85d342381e'  
branch_labels: Union[str, Sequence[str], None] = None  
depends_on: Union[str, Sequence[str], None] = None  
  
  
def upgrade() -> None:  
    op.create_table('rooms',  
                    sa.Column('id', sa.Integer(), nullable=False),  
                    sa.Column('hotel_id', sa.Integer(), nullable=False),  
                    sa.Column('title', sa.String(length=100), nullable=False),  
                    sa.Column('description', sa.String(), nullable=True),  
                    sa.Column('price', sa.Integer(), nullable=False),  
                    sa.Column('quantity', sa.Integer(), nullable=False),  
                    sa.ForeignKeyConstraint(['hotel_id'], ['hotels.id'], ),  
                    sa.PrimaryKeyConstraint('id')  
                    )  
  
  
def downgrade() -> None:  
    op.drop_table('rooms')
```
Чтобы внести эту таблицу в базу данных мы используем консольную команду:
	*alembic upgrade head* # для накатывания самой последней миграции
	*alembic upgrade номер_ревизии* # для конкретной миграции по её номеру '8fc1a940c8a7'

Чтобы откатиться на версию назад, используем консольную команду:
	*alembic downgrade* *номер_ревизии* 
	*alembic downgrade -1* # можно откатиться на предыдущую миграцию

Для полного отката БД следующие шаги:

1. Откатить все миграции:
   *alembic downgrade base*
2. Удалить папку с миграциями (опционально)
   *rm -r src/migrations/versions/*
3. Переинициализировать Alembic (если нужно)
   *alembic stamp head*
4. Создать начальную миграцию заново:
   *alembic revision --autogenerate -m "initial"*
   *alembic upgrade head*
