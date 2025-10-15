Параметры пути, которые используются внутри URL-строки.

Разберём на примере [[DELETE эндпоинты через SQLAlchemy]]:
```python
@app.delete("/hotels/{hotel_id}")
def delete_hotel(
	hotel_id: int
):
	"тут логика удаления отеля"
	return {"status": "ok"}
```

Также можно указать явно, что это параметр пути:
```python
from fastapi import FastAPI, Path

@app.delete("/hotels/{hotel_id}")
def delete_hotel(
	hotel_id: int = Path()
):
	"тут логика удаления отеля"
	return {"status": "ok"}
```

Также у объектов Path() есть параметры, например:
	Path(description="информация по полю")