Есть фишка OpenAPI, которые помогают при тестировании ручек:

Способ создания примеров данных:
```python
from fastapi import FastAPI, Body

@app.post("/hotels")
def create_hotel(
	hotel_data: HotelAdd = Body(  
    openapi_examples={  
        "1": {  
            "summary": "Сочи",  
            "value": {  
                "title": "Отдых наяву, а не во сне",  
                "location": "Лучший город Сочи",  
            },  
        },
		"2": {  
            "summary": "Йошкар-ОЛа",  
            "value": {  
                "title": "Мари-Турек",  
                "location": "Йошкар-Ола",  
            },  
        },
    }  
),
):
	return {"status": "ok"}
```

Вывод в OpenAPI:
![[Pasted image 20251017204036.png]]
