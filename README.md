# Clean_national_covid_data

```
import pandas as pd
import re
import requests
from bs4 import BeautifulSoup
```

#将数据转为list
```
data['qz'] = data['qz'].apply(lambda x:x.split('，'))
data['wzz'] = data['wzz'].apply(lambda x:x.split('，'))
```
