---
layout: post
title:  "python爬虫下载Bilibili番剧弹幕"
date:   2017-04-30 16:00:00
tags: python
---

本文绍如何利用python爬虫下载bilibili番剧弹幕。  

---

## 准备：  

1. python3环境
2. 需要安装BeautifulSoup,selenium包
3. phantomjs

---

## 原理:  
 1. 通过aid下载bilibili番剧弹幕
 2. 通过aid获取cid，如: http://www.bilibili.com/widget/getPageList?aid=9654289
 3. 下载弹幕地址：http://comment.bilibili.tv/cid.xml

---  

## 代码：
```python  
# -*- coding: utf-8 -*-

import requests
import json
import urllib.request
import zlib
import os
import re
from bs4 import BeautifulSoup
from urllib.parse import quote
from selenium import webdriver
headers = { 'Accept':'*/*',
    'Accept-Encoding':'gzip, deflate, sdch, br',
    'Accept-Language':'zh-CN,zh;q=0.8',
    'Access-Control-Request-Headers':'content-encoding, content-type, x-za-batch-size, x-za-log-version, x-za-platform, x-za-product',
    'Access-Control-Request-Method':'POST',
    'Cache-Control':'no-cache',
    'Connection':'keep-alive',
    'Host':'bilibili-web-analytics.bilibili.com',
    'Origin':'https://www.bilibili.com',
    'Pragma':'no-cache',
    'User-Agent':'Mozilla/5.0 (Windows NT 10.0; Win64; x64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/54.0.2840.99 Safari/537.36'
}
for key, value in enumerate(headers):
    webdriver.DesiredCapabilities.PHANTOMJS['phantomjs.page.customHeaders.{}'.format(key)] = value
driver = webdriver.PhantomJS(executable_path='./phantomjs-2.1.1-windows/bin/phantomjs.exe')

def geAidByKeyword(Keyword):
    print('正在搜索，请等待......')
    search_url="http://search.bilibili.com/bangumi?keyword="+Keyword
    search_url=quote(search_url,safe='/:?=')
    search_html = urllib.request.urlopen(search_url)
    search_bsObj = BeautifulSoup(search_html,'html.parser')
    search_linkList = search_bsObj.findAll("a",{"class":"title"})
    count=len(search_linkList)
    print('已找到%s个' %count)
    i=0
    for item in search_linkList:
        print('%s:%s' % (i,search_linkList[i]['title']))
        i=i+1
    # 选择
    select=input('请选择你要下载的编号：')
    select=int(select)
    # print(select)
    search_link=search_linkList[select]['href']
    if search_link=='':
        print('输入无效字符')
        return False
    # 进入
    # print(search_link)
    search_link="http:"+search_link
    select_link_html = urllib.request.urlopen(search_link)
    select_link_bsObj = BeautifulSoup(select_link_html,'html.parser')
    select_link_list=select_link_bsObj.findAll("li",{"data-newest-ep-id":re.compile("[0-9]+")})
    data_season_id=select_link_list[0]['data-season-id']
    data_newest_ep_id=select_link_list[0]['data-newest-ep-id']
    new_aid_url="http://bangumi.bilibili.com/anime/%s/play#%s" % (data_season_id,data_newest_ep_id)
    getNewAid(new_aid_url)
    # print(data_season_id)


def getNewAid(url):
    driver.get(url)
    html = driver.page_source
    bsObj = BeautifulSoup(html,'html.parser')
    aid_list=bsObj.findAll("a",{"class":"v-av-link"})
    aid=aid_list[0].get_text()
    aid=aid[2:]
    checkAndDown(aid)

def checkAndDown(aid):
    title=getAnimeName(aid)
    check=input('你要下载的是%s,是否要下载(1：是；0：否)：' %title)
    print(check)
    if int(check)==1:
        getPageList(aid)
    else:
        return False


def getAnimeName(aid):
    # print(aid)
    html = urllib.request.urlopen("http://www.bilibili.com/video/av%s" %aid)
    bsObj = BeautifulSoup(html,'html.parser')
    try:
       title=bsObj.find("title").get_text()
    except Exception as e:
        print('获取失败,aid=%s' %aid)
        return False

    return title

def getPageList(aid):
    url="http://www.bilibili.com/widget/getPageList"
    params = {
        'aid':aid
    }
    re = requests.get(url,params)
    cidDic=json.loads(re.text)
    animeName=getAnimeName(aid)
    for cidItem in cidDic:
        cidItem['animeName']=animeName
        downloadDanmu(cidItem)

def downloadDanmu(cidItem):
    cid=cidItem['cid']
    animeName=cidItem['animeName']
    pagename=cidItem['pagename']
    comment_url="http://comment.bilibili.tv/%s.xml" %cid
    comment_page_zip=urllib.request.urlopen(comment_url).read()
    comment=zlib.decompressobj(-zlib.MAX_WBITS).decompress(comment_page_zip)
    if os.path.exists('%s' % animeName)==False:
        os.makedirs('%s' % animeName)
    fout=open('%s/%s.xml' % (animeName,pagename),'wb')
    fout.write(comment)
    fout.close()
    print('%s %s下载完成' % (animeName,pagename))

# aid=input('请输入aid：')
# getPageList(aid)
keyword=input('请输入番剧的关键字：')
geAidByKeyword(keyword)
```
