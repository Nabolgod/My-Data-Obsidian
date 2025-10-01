Для корректной передачи ошибки клиенту, FastAPI предлагает встроенный класс ошибок [[`HTTPException`]]. Их нужно отправлять всякий раз, когда внутри кода возникает ошибка. Важно отправлять корректный код ответа `status_code` и сообщение `detail`.

В примере ниже, когда клиент обращается по адресу `/items/0`, нам необходимо вернуть ему ошибку с кодом 404 и подписью "Объект не найден".

```python
from fastapi import FastAPI, HTTPException

app = FastAPI()


@app.get("/items/{item_id}")
def read_item(item_id: int):
    if item_id == 0:
        raise HTTPException(status_code=404, detail="Объект не найден")
    return {"item_id": item_id}
```