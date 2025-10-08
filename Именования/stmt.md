Именование используется для обозначения изменения ([[POST]], [[DELETE]], [[PATCH]]).
```python
async with async_session_maker() as session:  
    add_hotel_stmt = alh.insert(HotelsORM).values(**hotel_data.model_dump()) 
    await session.execute(add_hotel_stmt)  
    await session.commit()
```
