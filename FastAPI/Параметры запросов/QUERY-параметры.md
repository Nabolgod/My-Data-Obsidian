Это параметры, которые используются при фильтрации данных, сортировки, для пагинации.
Они не созданы для передачи информации на [[POST]], [[PUT]], [[PATCH]] ручки, для этого, как вариант используется [[BODY-запросы]].
Пример:
```python
@app.get("/hotels")
def get_hotels(id: int, title: str):
	result = "тут результат фильтрации"
	return result
```
<mark style="background: #ABF7F7A6;">id: int, title: str</mark> - query-параметры.
В данном примере эти поля являются обязательными для заполнения.

Отображение query-параметров в URL-строке:
http//тут_айдишник/hotels?<mark style="background: #ABF7F7A6;">id=значение&title=значение</mark>

Для явного указания можно использовать объекты класса Query из FastAPI:
```python
from fastapi import FastAPI, Query

@app.get("/hotels")
def get_hotels(
	id: int = Query(), 
	title: str = Query(description="Название отеля")
):
	result = "тут результат фильтрации"
	return result
```
Теперь можно указывать дополнительные параметры, например:
	Query(<mark style="background: #D2B3FFA6;">description</mark>="названия поля ввода")
	Query(<mark style="background: #D2B3FFA6;">default</mark>="значение по умолчанию")

Для того, чтобы сделать поля необязательными для ввода можно указать это через None:
```python
from fastapi import FastAPI, Query

@app.get("/hotels")
def get_hotels(
	id: int | None = Query(default = None), 
	title: str | None = Query(default = None, description="Название отеля")
):
	result = "тут результат фильтрации"
	return result
```
