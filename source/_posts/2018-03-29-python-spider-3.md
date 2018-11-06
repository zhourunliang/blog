---
layout: post
title:  "利用python爬虫下载文件"
date:   2018-03-29 15:24:00
tags: python
---

利用python爬虫下载文件。  

---

## 代码：
```python  
import urllib.request
import os

print ("ready")
url = 'http://xxxx.com/'   
savePath = "C:/savePath/"
fileName = "xxx"
getCount = 0
lastCount = 0
def downFile(url,fileName,savePath):
    print('-----------------')
    global getCount
    global lastCount
    print('getCount=%s' %getCount)
    getCount = getCount+1
    if lastCount>=getCount:
        print ("jump")
        return False
    print ("geting"+fileName)
    getUrl=url+fileName
    print ("getUrl="+getUrl)
    try:
        f = urllib.request.urlopen(getUrl)
    except Exception as e:
        print ("url not exit")
        return False
    if f.getcode()==200:
        print("getUrl success")
        if os.path.exists(savePath+fileName):
            print("file exits")
        else:
            data = f.read() 
            with open(savePath+fileName, "wb") as code:     
                code.write(data)
                print('success down '+fileName)

def main():
    fileName='a.xxx'
    downFile(url,fileName,savePath)
main()

```
