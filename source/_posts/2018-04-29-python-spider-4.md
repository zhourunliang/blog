---
layout: post
title:  "利用python爬虫获取json数据并保存"
date:   2018-04-29 16:15:00
tags: python
---

利用python爬虫获取json数据并保存  

---

## 代码：
```python  
import requests
import json
import pymysql

conn = pymysql.connect(host='127.0.0.1',user=
                       'root', passwd='', db='mysql', charset='utf8')
cur = conn.cursor()
cur.execute("USE spider")

url = 'http://urllink.com/getdata/'

def getData(url, group, type):
    params = {
        'group':group,
        'type':type,
    }
    headers = {
        'Accept': 'application/json, text/plain, */*',
        'Accept-Encoding': 'gzip, deflate',
        'Accept-Language': 'zh-CN,zh;q=0.9,en;q=0.8',
        'Cache-Control': 'no-cache',
        'Connection': 'keep-alive',
        'Cookie': 'xxx',
        'Host': 'urllink.com',
        'Pragma': 'no-cache',
        'Referer': 'http://urllink.com/',
        'User-Agent': 'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36',
        'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/65.0.3325.181 Safari/537.36',
    }
    re = requests.get(url,params, headers=headers)
    result = json.loads(re.text)
    result = result['results']
    return result

def  saveData(data):
    cur.execute("SELECT * FROM spdier_data WHERE id = %s",int(data['id']))
    if cur.rowcount == 0:
        print('插入数据库...id='+str(data['id']))
        cur.execute("INSERT INTO spdier_data (id, group, type,content) VALUES (%s, %s, %s, %s)",(int(data['id']), int(data['group']),int(data['type']), str(data['content'])))
        conn.commit()

def main():
    for group in range(1,7):
        for type in range(1,7):
            result = getData(url,group,type)
            for data in result:
                saveData(data)
main()
cur.close()
conn.close()

```
