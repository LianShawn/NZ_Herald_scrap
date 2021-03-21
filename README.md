# NZ_Herald_scrap
scrap about the news website of New Zealand, both English one and Chinese one 

##Chinese one

#首先import相关的package
```
import requests
from bs4 import BeautifulSoup
import pandas as pd
import re
```
#定义这个模型，是抓取links和标题的
```
def get_date(url):
    news_links=[]
    news_titles=[]
    result=requests.get(url)
    soup = BeautifulSoup(result.text, 'html.parser')
    for link in soup.find_all('a',class_='search-result'):
        links = link.get('href')
        news_links.append(links)
        texts=link.text.strip()
        news_titles.append(texts)
        
    data_news = {
        'news_title':news_titles,
        'news_links':news_links
    }
    
    data_news = pd.DataFrame(data_news)
    
    return data_news
```
#看自己需要的内容是什么，一共有几页
```
for i in range(20,320,20):
  url_1 = 'https://www.chinesenzherald.co.nz/home/SearchForm?url=%2Fpublic%2Fhome%2FSearchForm&Search=%E5%9F%BA%E7%9D%A3%E5%9F%8E%E6%9E%AA%E5%87%BB%E6%A1%88&start={}'.format(i)
  ddd=get_date(url_1)
  data_fin = data_fin.append(ddd)
  print(i)
```
#因为抓下来的网页不全，需要补全
    
```
lins=[]
for i in data_fin['news_links']:
  ii = "https://www.chinesenzherald.co.nz"+i
  lins.append(ii)
data_fin['news_links']=lins
```
    
#因为中文的时间无法在目录页面直接抓取，所以需要点击进去
```
a=0
times = []
contents = []
titles = []
for i in data_fin['news_links']:
    
    result=requests.get(i)
    soup = BeautifulSoup(result.text, 'html.parser')
    
    time = soup.find(class_='article-page__header__date').text
    content = soup.find(class_='article-page__content').text.strip()
    title = soup.find(class_='article-page__header').text.strip()

    times.append(time)
    contents.append(content)
    titles.append(title)

    print(a)
    a+=1
``` 
#最后将抓好的信息合并
``` 
data_fin['time'] = times
data_fin['content'] = contents
``` 

#保存信息
``` 
data_fin.to_csv("Chinese_news_2021.csv",index=False,sep=',',mode='a')

``` 
##英文版的
#英文的比较方便，因为时间就在列表页面

#也是先import相关的packages

```
import requests
from bs4 import BeautifulSoup
import pandas as pd
import re
```
#然后也是抓取标题、links和时间
```
def get_date(url):
    news_links=[]
    news_titles=[]
    times = []
    result=requests.get(url)
    soup = BeautifulSoup(result.text, 'html.parser')
    
    for link in soup.find_all(class_='story-card__heading'):
        links= link.find('a').get('href')
        news_links.append(links)
        texts=link.text.strip()
        news_titles.append(texts)
    
    for factor in soup.find_all(class_='meta-data__time-stamp'):
        time = factor.text.strip()
        
        times.append(time)
    
    data_news = {
        'news_title':news_titles,
        'time':times,
        'news_links':news_links
    }
    
    data_news = pd.DataFrame(data_news)
    
    return data_news
```

#抓取过程
```
for i in range(2,19):
    url='https://www.nzherald.co.nz/search/Christchurch%20mosque%20massacre/{}/'.format(i)
    dddd = get_date(url)
    data_fin = data_fin.append(dddd)
    print(i)
```
#补充链接的完整性
```
lins=[]
for i in data_fin['news_links']:
    ii = "https://www.nzherald.co.nz"+i
    lins.append(ii)
data_fin['news_links']=lins
```
#最后保存吧～
```
data_fin.to_csv("English_news_massacre.csv",index=False,sep=',',mode='a')
```






    
    
    
    
    
