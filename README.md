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

#clean data
```
def clean(data):
    shuzi = []
    place = []
    num = len(data)
    pattern = r'\d+'
    pattern2 = r'[\u4e00-\u9fa5]+'
    
    for i in range(0,num):
        ii = re.findall(pattern,data[i])
        shuzi.append(ii[0])
        
        iii = re.findall(pattern2,data[i])
        place.append(iii[0])
    
    result = {
        'place':place,
        'num':shuzi
    }
    
    result = pd.DataFrame(result)
    return result
    
 ```
 
 ```
 def gather(data):
    
    num2 = len(data)
    linshi = clean(data['qz'][0])
    linshi2 = clean(data['qz'][1])
    result = pd.merge(linshi,linshi2,on='place',how = 'outer')
    for i in range(2,num2):
        linshi3 = clean(data['qz'][i])
        result = pd.merge(result,linshi3,on='place',how = 'outer')
    
    result =result.fillna(0)
    
    return result
 ```
 
 ```
 def gather2(data):
    
    num2 = len(data)
    linshi4 = clean(data['wzz'][0])
    linshi5 = clean(data['wzz'][1])
    result2 = pd.merge(linshi4,linshi5,on='place',how = 'outer')
    for i in range(2,num2):
        linshi6 = clean(data['wzz'][i])
        result2 = pd.merge(result2,linshi6,on='place',how = 'outer')
    
    result2 =result2.fillna(0)
    
    return result2
```

```
qz_result = gather(data)
wzz_result = gather2(data)
```

#read again
```
sta1 = pd.read_excel('qz.xlsx')
sta2 = pd.read_excel('wzz.xlsx')
```
#计算感染值，就是无症状+确诊
```
sta3 = pd.concat([sta1,sta2])
sta3 = sta3.groupby(sta3.place).sum().reset_index()
sta3.to_excel('all.xlsx')
```
 
 
 
 
    
    
