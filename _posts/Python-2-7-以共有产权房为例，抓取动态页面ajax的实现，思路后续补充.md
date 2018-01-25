>   突然想看某个单位的信息，我不想每天都打开那个页面，看着太糟心，顺便就爬下来了，稍后整理下发出来，实现如下


```
# -*- coding:utf-8 -*-
import json
import requests
import cookielib
import urllib2
from bs4 import BeautifulSoup

cookie_accpet = ''
cookie = cookielib.CookieJar()
handler = urllib2.HTTPCookieProcessor(cookie)
opener = urllib2.build_opener(handler)
response = opener.open('http://zzfws.bjjs.gov.cn/enroll/home.jsp')
for item in cookie:
    cookie_accpet += item.name + "=" + item.value + ';'
if len(cookie_accpet) > 0:
    cookie_accpet = cookie_accpet[0:len(cookie_accpet)]
    print 'cookie=', cookie_accpet
header_dict = {
    'Host': 'zzfws.bjjs.gov.cn'
    , 'User-Agent': 'Mozilla/5.0 (Macintosh; Intel Mac OS X 10.13; rv:57.0) Gecko/20100101 Firefox/57.0'
    , 'Accept': 'application/json, text/javascript, */*; q=0.01'
    , 'Accept-Language': 'zh-CN,zh;q=0.8,zh-TW;q=0.7,zh-HK;q=0.5,en-US;q=0.3,en;q=0.2'
    , 'Accept-Encoding': 'gzip, deflate'
    , 'Referer': 'http://zzfws.bjjs.gov.cn/enroll/home.jsp'
    , 'Content-Type': 'application/json;charset=UTF-8'
    , 'X-Requested-With': 'XMLHttpRequest'
    , 'Content-Length': '40'
    , 'Cookie': cookie_accpet
    , 'Connection': 'keep-alive'
    , 'Pragma': 'no-cache'
    , 'Cache-Control': 'no-cache'
}

textmod = {"currPage": 1, "pageJSMethod": "goToPage"}
textmod = json.dumps(textmod)
url = 'http://zzfws.bjjs.gov.cn/enroll/dyn/enroll/viewEnrollHomePager.json'
req = requests.post(url=url, data=textmod, headers=header_dict)
print req.status_code
encode_json = json.loads(str(req.content))
soup = BeautifulSoup(encode_json['data'], 'html.parser')
link = soup.find('a', {'class': "F14"}, 'href')['href']
# print(soup.prettify())
pagenum = soup.find('div', {'class': 'page-num'})
pagenum.decompose()
print "详情界面：", link
s = soup.text.split(' ')
for ll in s:
    print ll
```
