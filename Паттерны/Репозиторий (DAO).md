#api

Наглядная схема:
![[Pasted image 20251009012257.png]]

Паттерн позволяет работать с данными, как будто они рядом с нами и мы работаем с ними будто они в оперативной памяти. Для этого создаётся сущность, которую описывает сам программист. Позволяет гибко работать с тем, что может предоставлять данные (бд).

Допустим у нас есть бизнес логика вернуть что-то (отель, номер отеля, бронирование и т.д.), репозиторий позволяет реализовать грубо говоря полиморфизм по отношению к бизнес логике.
## Пример репозитория

Можно начать с создания папки /repositories относительно папки [[src]] и создания в ней файла <mark style="background: #ADCCFFA6;">base.py</mark>, который будет содержать базовый класс от которого все будут наследоваться.
```python
class BaseRepository:
	_model = None
	
	def __init__(self, session:
		self.session = session
		
	async def get_all(self):
		''' Здесь прописана логика возврата всех данных '''
		
		query = select(self._model) 
		result = await self.session.execute(query)
		return result.scallars.all()
	
	async def get_one_or_none(self, **filter_by):
		''' 
		Здесь прописана логика возврата одной еденицы данных с
		определёнными фильтрами.
		'''
		
		query = select(self._model).filter_by(**filter_by)
		result = await self.session.execute(query)
		return result.scallars.one_or_none()
		
class HotelsRepository(BaseRepository):
	model = HotelsORM

class RoomsRepository(BaseRepository):
	model = RoomsORM
	
```
В классе мы принимаем [[Session]] на вход, чтобы все наши методы (запросы в бд например) работали внутри одной сессии и не блокировали соединение.

Также у родительского и в последующем дочерних классов будет атрибут класса [[Model]] для работы с их моделями бд.