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

#单纯看每天的数据
```
import pandas as pd
import re
data = pd.read_excel('covid.xlsx')
data['qz'] = data['qz'].apply(lambda x:x.split('，'))
data['wzz'] = data['wzz'].apply(lambda x:x.split('，'))

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
    
qz2 = clean(data['qz'][0])
wzz2 = clean(data['wzz'][0])
result_qz = pd.merge(qz2,wzz2,on='place',how = 'outer')
result_qz = result_qz.fillna(0)
```
#上海各区的数据
```
def count(shuzi):
    #提取出区
    num =[]
    qu=[]
    shuzi=shuzi.dropna()
    for i in shuzi:
        ii = i.split('，')
        num.append(ii[0])
        iii = ii[1].replace('居住于','')
        qu.append(iii)
    data_a={
    'qu':qu,
    'num':num
    }
    
    data_a=pd.DataFrame(data_a)
    
    #提取出数字
    shuzi = []
    for i in data_a['num']:
        age = re.findall('\d+',i)
        if len(age)==2:
            bb = int(age[1])-int(age[0])+1
            shuzi.append(bb)
        else:
            shuzi.append(1)
    
    data_a['shuzi']=shuzi
    
    data_b = {
    'qu':data_a['qu'],
    '4/28':data_a['shuzi']
    }
    
    data_b=pd.DataFrame(data_b)
    
    data_c=data_b.groupby('qu')['4/28'].sum()
    
    data_c=pd.DataFrame(data_c)
    return data_c

que_1 = count(data3['q1'])
wu_1 = count(data3['w1'])
data_result = pd.merge(que_1,wu_1,on='qu',how='outer')
data_result = data_result.fillna(0)
```
