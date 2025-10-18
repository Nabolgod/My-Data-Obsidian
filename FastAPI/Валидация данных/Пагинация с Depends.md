В больших проектах используется пагинается чуть ли не в каждой ручке. Логику работы пагинации можно вынести в отдельную логику (файл).

Для этого создадим файл <mark style="background: #FF5582A6;">dependencies.py</mark> (зависимости):
```python
from fastapi import Depends, Query
from pydantic import BaseModel

class PaginationParams(BaseModel):
	page: int | None = Query(None, gt=1)
	per_page: int | None = Query(None, gt=1, lt=30)
```

Мы хотели использовть Qyery-объект модуля fastapi в pydantic-схеме, но мы так не можем сделать, будет ошибка, поэтому изменим типизацию:
```python
from typing import Annotated

class PaginationParams(BaseModel):
	page: Annotated[int | None, Query(None, ge=1)]
	per_page: Annotated[int | None, Query(None, ge=1, le=30)]
```

В данном случае мы имеем просто тип и FastAPI умеет считывать из типа и сам тип (int | None) и его реализацию (Query).

Как теперь передать эти параметры внутри ручки?
```python
from fastapi import Query, APIRouter, Body

from dependencies import PaginationDep
from schemas.hotels import Hotel, HotelPATCH

router = APIRouter(prefix="/hotels", tags=["Отели"])

@router.get("")
def get_hotels(
        pagination: PaginationParams,
        id: int | None = Query(None, description="Айдишник"),
        title: str | None = Query(None, description="Название отеля"),
```

В данном случае pagination будет распозноваться, как [[BODY-запросы]], но в [[GET]] такого быть не может.
Попробуем присовоить явно [[QUERY-параметры]]:
```python
@router.get("")
def get_hotels(
        pagination: PaginationParams = Query(),
        id: int | None = Query(None, description="Айдишник"),
        title: str | None = Query(None, description="Название отеля"),
```

В данном случае будет ошибка с указанием, что pagination может быть только телом запроса, но такого снова не может быть при нашей логике.

Что делать? Использовать [[Что такое Depends в FastAPI]].

Используем синтаксис Annotated, в которую заложим логику, а FastAPI её от туда вычленит.
```python
@router.get("")
def get_hotels(
        pagination: Annotated[PaginationParams, Depends()],
        id: int | None = Query(None, description="Айдишник"),
        title: str | None = Query(None, description="Название отеля"),
```

Параметр Depends() в данном случа говорит "Пожалуйста FastAPI прокинь в зависимости параметры, которые мы указывали в файле dependencies.py".

Упростим запись, изменив dependencies.py:
```python
from typing import Annotated
from fastapi import Query, APIRouter, Body
from pydantic import BaseModel

class PaginationParams(BaseModel):
	page: Annotated[int | None, Query(None, ge=1)]
	per_page: Annotated[int | None, Query(None, ge=1, le=30)]
	
PaginationDep = Annotated[PaginationParams, Depends()]
```

Теперь:
```python
@router.get("")
def get_hotels(
        pagination: PaginationDep,
        id: int | None = Query(None, description="Айдишник"),
        title: str | None = Query(None, description="Название отеля"),
```