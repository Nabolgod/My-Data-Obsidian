Именование query используется для работы с запросами [[SELECT]] ([[GET]]).
```python
async with async_session_maker() as session:
	    query = select(HotelORM)
```
