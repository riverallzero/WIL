# matplotlib 한글 깨짐 해결하기(mac, window)

## mac

```python
from matplotlib import font_manager, rc

plt.rc('font', family='AppleGothic')

```

## window

```python
from matplotlib import font_manager, rc

font_path = "C:/Windows/Fonts/NGULIM.TTF"
font = font_manager.FontProperties(fname=font_path).get_name()
rc('font', family=font)
```

## mac & window 

```python
from matplotlib import font_manager, rc
import platform

if platform.system() == "Darwin":
    plt.rc('font', family='AppleGothic')
elif platform.system() == "Windows":
    font_path = "C:/Windows/Fonts/NGULIM.TTF"
    font = font_manager.FontProperties(fname=font_path).get_name()
    rc('font', family=font)
```
