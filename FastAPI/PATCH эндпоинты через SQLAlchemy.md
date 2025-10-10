[[PATCH]] - частичное обновление данных в БД.

Пример метода через [[Репозиторий (DAO)]]
```python
async def edit(
	self, 
	data: BaseModel, 
	exclude_unset: bool = False, 
	**filter_by
) -> None:  
    put_data_stmt = (  
        update(self.model)  
        .filter_by(**filter_by)  
        .values(**data.model_dump(exclude_unset=exclude_unset))  
    )  
    await self.session.execute(put_data_stmt)
```
Пример эндпоинта:
```python
@router.patch("/{hotel_id}", summary="Изменение определённой информации об отеле")
async def patch_hotel(  
        hotel_id: int,  
        hotel_data: HotelPATCH,  
):  
    async with async_session_maker() as session:  
        repository = HotelsRepository(session)  
        
        await repository.edit(hotel_data, exclude_unset=True, id=hotel_id)  
        await session.commit()  
		  
    return {"status": "success", "message": f"Отель {hotel_id} частично обновлен"}
```

При работе с PATCH методами нужно понимать работу с [[pydantic]].
```python
from pydantic import BaseModel, Field  
  
  
class Hotel(BaseModel):  
    title: str  
    location: str  
  
  
class HotelPATCH(BaseModel):  
    title: str | None = Field(default=None)  
    location: str | None = Field(default=None)
```

<mark style="background: #ABF7F7A6;">title: str | None = Field(default=None)</mark>  - данная запись говорит о том, что поле может быть заполнено или по умолчанию имеет значение None.

Если отправить данные через ручку и не указывать одно из полей, то из pydantic придёт следующий формат данных:
```python
{"title": "новое название", "location": None} # без exclude_unset = False
{"title": "новое название"} # с exclude_unset = True
```
В таком случае location имеет значение None и все поля в таблице примут это значение. Чтобы этого избежать и игнорировать такие поля достаточно использовать атрибут  <mark style="background: #FFB86CA6;">exclude_unset = True</mark> метода <mark style="background: #ABF7F7A6;">model_dump</mark> , который говорит о том, что не нужно обновлять записи, которые пользователь не вводил.

