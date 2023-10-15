# 그래프 축을 날짜로 설정하기

<img src="../.asset/graph-dateform.png" width="500">

```python
from matplotlib.dates import DateFormatter
import matplotlib.pyplot as plt

date_form = DateFormatter('%m-%d')
plt.gca().yaxis.set_major_formatter(date_form)
```
