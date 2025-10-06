#функции #бд

Это обёртка, которая работает с движком ([[Engine]]) для "похода" в базу данных. Движки бывают разные, синхронные, асинхронные.

Сама алхимия работет с результатами того, что вернул движок.

Для работы с функциями базы данных можно использовать модуль <mark style="background: #ADCCFFA6;">func</mark>.
Пример:

```python
from sqlalchemy import func


async def get_hotels(
	hotel_id: int | None = Query(default=None, description="ID-номер"),  
):  
	async with async_session_maker() as session:  
	    query = alh.select(HotelsORM)  
	    
	    query = (
		    query
		    .filter(func.lower(HotelsORM.location)
		    .like(f"%{location.lower()}%"))
	    )
	    
	    result = await session.execute(query)  
	    return result.scalars().all()

```
Здесь мы используем <mark style="background: #FFB86CA6;">func.lower</mark> по отношению к атрибуту (полю) модели данных (таблицы), чтобы привести его в нижнему регистру, а потом воспользоваться фильтрацией без учёта регистра.

