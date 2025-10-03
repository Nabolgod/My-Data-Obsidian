Двумерная структура структура данных, представляющая собой таблицу, каждый столбец которой является структурой [[Series]].

```python
import pandas as pd

months = [m1, m2, m3, m4]
sales = {
	k1: [p1, p2, p3],
	k2: [p1, p2, p3]
}
df = pd.DataFrame(data = sales, index = months)
```
