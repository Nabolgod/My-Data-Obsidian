Используются при [[POST эндпоинты через SQLAlchemy]] для обозначения какого-то тела.

Пример:
```python
from fastapi import FastAPI, Body

@app.post("/hotels")
def create_hitel(
	title: str, # ошибка, т.к. это воспринимается как query-параметры
	title: str = Body(embed=True), # теперь правильно
	location: str | None = Body(default=None, emded=True)
):
	"логика добавления отеля"
	return {"status": "ok"}
```

^80fae4

У объектов Body() есть параметры, например:
	Body(<mark style="background: #D2B3FFA6;">embed=True</mark>), который переводит вывод в json-виде
	Body(<mark style="background: #D2B3FFA6;">default="значение по умолчанию"</mark>)