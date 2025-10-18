В SQLAlchemy транзакции обрабатываются с использованием объектов сессии [[Session]]. Новая транзакция начинается автоматически при первом обращении к объекту сессии. После вызова команды `session.commit()` происходит отправка всей транзакции в базу данных. 

Если во время транзакции происходит ошибка, используется `session.rollback()` для отката всех изменений, внесенных в текущую транзакцию. Это обеспечивает целостность данных, предотвращая частичные обновления. 

Например:
```python
from sqlalchemy import create_engine
from sqlalchemy.orm import sessionmaker

engine = create_engine('sqlite:///example.db')
Session = sessionmaker(bind=engine)
session = Session()

try:
    # выполняем любые операции с сессией...
    session.add(some_object)
    session.execute(delete(Users).where(Users.id == 1))
    session.commit()  # <-- изменения отправляются в БД
except:
    session.rollback()  # <-- в случае ошибки изменения откатываются
finally:
    session.close()  # <-- сессию всегда нужно закрывать
```

Этот код инициирует транзакцию с помощью `session.add()` и пытается зафиксировать её. Если происходит исключение, транзакция откатывается, и никакие изменения не сохраняются в базе данных.