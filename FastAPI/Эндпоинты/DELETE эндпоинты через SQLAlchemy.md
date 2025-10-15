[[DELETE]] - удаление записи в БД.

Пример метода в [[Репозиторий (DAO)]]:
```python
async def delete(self, **filter_by) -> None:  
    """  
    Метод для удаления данных    """    delete_data_stmt = (  
        delete(self.model)  
        .filter_by(**filter_by)  
    )  
    await self.session.execute(delete_data_stmt)
```
Реализация самой ручки (эндпоинта):
```python
@router.delete("/{hotel_id}", summary="Удалить информацию об отеле")  
async def delete_hotel(hotel_id: int):  
    async with async_session_maker() as session:  
        repository = HotelsRepository(session) 
		  
        await repository.delete(id=hotel_id)  
        await session.commit()  
  
    return {"status": "success", "message": f"Отель {hotel_id} удален"}
```