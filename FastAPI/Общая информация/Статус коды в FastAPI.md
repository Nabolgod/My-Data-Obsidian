## Основные сценарии и автоматические коды

### 1. **Успешные запросы (200 OK)**
```python
from fastapi import FastAPI
app = FastAPI()

# Автоматически 200 для успешных GET-запросов
@app.get("/items/")
def read_items():
    return {"message": "Все ок"}

# Автоматически 200 для успешных операций
@app.post("/items/")
def create_item(item: dict):
    return {"id": 1, **item}
```
### 2. **Создание ресурсов (201 Created)**
```python
from fastapi import status

@app.post("/items/", status_code=status.HTTP_201_CREATED)
def create_item(item: dict):
    return {"id": 1, **item}
```
### 3. **Ошибки валидации Pydantic (422 Unprocessable Entity)**
```python
from pydantic import BaseModel

class Item(BaseModel):
    name: str
    price: float

# Если передать невалидные данные, автоматически вернется 422
@app.post("/items/")
def create_item(item: Item):
    return item
```
## Явное указание статус-кодов

### Использование `status` из FastAPI
```python
from fastapi import FastAPI, status

app = FastAPI()

@app.post("/items/", status_code=status.HTTP_201_CREATED)
def create_item(item: dict):
    return {"id": 1, **item}

@app.delete("/items/{item_id}", status_code=status.HTTP_204_NO_CONTENT)
def delete_item(item_id: int):
    # Для 204 не возвращаем тело ответа
    return None
```
## Обработка ошибок и кастомные статус-коды

### Использование `HTTPException`
```python
from fastapi import FastAPI, HTTPException, status

app = FastAPI()

items = {1: "apple", 2: "banana"}

@app.get("/items/{item_id}")
def read_item(item_id: int):
    if item_id not in items:
        # Явно указываем статус-код ошибки
        raise HTTPException(
            status_code=status.HTTP_404_NOT_FOUND,
            detail="Item not found"
        )
    return {"item": items[item_id]}
```
### Кастомные обработчики ошибок
```python
from fastapi import FastAPI, Request
from fastapi.responses import JSONResponse

app = FastAPI()

@app.exception_handler(ValueError)
async def value_error_handler(request: Request, exc: ValueError):
    return JSONResponse(
        status_code=status.HTTP_400_BAD_REQUEST,
        content={"message": str(exc)}
    )
```
## Часто используемые статус-коды в FastAPI
```python
from fastapi import status

# Часто используемые коды
common_status_codes = {
    "OK": status.HTTP_200_OK,
    "CREATED": status.HTTP_201_CREATED,
    "ACCEPTED": status.HTTP_202_ACCEPTED,
    "NO_CONTENT": status.HTTP_204_NO_CONTENT,
    
    "BAD_REQUEST": status.HTTP_400_BAD_REQUEST,
    "UNAUTHORIZED": status.HTTP_401_UNAUTHORIZED,
    "FORBIDDEN": status.HTTP_403_FORBIDDEN,
    "NOT_FOUND": status.HTTP_404_NOT_FOUND,
    "METHOD_NOT_ALLOWED": status.HTTP_405_METHOD_NOT_ALLOWED,
    "CONFLICT": status.HTTP_409_CONFLICT,
    "UNPROCESSABLE_ENTITY": status.HTTP_422_UNPROCESSABLE_ENTITY,
    "TOO_MANY_REQUESTS": status.HTTP_429_TOO_MANY_REQUESTS,
    
    "INTERNAL_SERVER_ERROR": status.HTTP_500_INTERNAL_SERVER_ERROR,
    "SERVICE_UNAVAILABLE": status.HTTP_503_SERVICE_UNAVAILABLE,
}
```
