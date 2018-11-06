---
layout: post
title:  "利用python爬虫下载页面的ed2k连接地址"
date:   2017-09-09 16:31:00
tags: python
---

利用python爬虫下载页面的ed2k连接地址。  

---

## 代码：
```python  
from urllib.request import urlopen
from bs4 import BeautifulSoup
import re
ed2ks = set()
def getPageEd2k(url):
    html = urlopen(url)
    bsObj = BeautifulSoup(html,'html.parser')
    lists = re.findall( r"(ed2k:\/\/\|file\|.+)[\"\']", str(bsObj), re.M|re.I)
    # print(lists)
    for link in lists:
        # print(link)
        if link not in ed2ks:
            print(link)
            ed2ks.add(link)
# 这里为要下载页面的连接
url='http://...'
getPageEd2k(url)
with open('ed2k.txt', 'w') as f:
    outPut=''
    for link in ed2ks:
        outPut+=link+'\r\n'
    # print(outPut)
    r=f.write(str(outPut))
    # print(r)

```
