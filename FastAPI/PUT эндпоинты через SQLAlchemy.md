[[PUT]] - полное обновление данных в БД.

Пример метода в [[Репозиторий (DAO)]]:
```python
async def edit(self, data: BaseModel, **filter_by) -> None:  
    """  
    Метод для редактирования данных с фильтрацией    """    put_data_stmt = (  
        update(self.model)  
        .filter_by(**filter_by)  
        .values(**data.model_dump())  
    )  
    await self.session.execute(put_data_stmt)
```
Реализация самой ручки (эндпоинта):
```python
@router.put("/{hotel_id}", summary="Полное изменение информации об отеле")  
async def put_hotel(  
        hotel_id: int,  
        hotel_data: Hotel,  
):  
    async with async_session_maker() as session:  
        repository = HotelsRepository(session)  
        
        await repository.edit(hotel_data, id=hotel_id)  
        await session.commit()  
		  
    return {"status": "success", "message": f"Отель {hotel_id} обновлен"}
```

