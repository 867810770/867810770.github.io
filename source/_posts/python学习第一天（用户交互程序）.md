---
title: python学习第一天（用户交互程序）
date: 2019-03-04 14:29:26
tags: 学习笔记
categories: 软件开发
---
## python学习第一天（用户交互程序）
&#160; &#160; &#160; &#160;好久以前就接触到了python，但是一直没有静下心来学习，最近因为某些特殊需求吧，需要用到爬虫，就慌忙火气的写了一个不太成熟的爬虫小程序，但是写完以后自己才发现太垃圾了，所以决定还是好好的静下心来学习python，同时也准备继续完善自己写的这个爬虫，毕竟是第一次自己独立写出的一个程序！今天是学习python的第一天，就接触了一下输入输出，字符转换的一些东西，看了一下教程以后觉得自己有必要记一下，同时也能时刻提醒自己，每天保持学习的习惯！下面就先分享一下我自己写的这个爬虫代码吧，然后的话，就是记录一下自己今天的python学习情况！
<!--more-->
## 爬虫代码
&#160; &#160; &#160; &#160;自写爬虫垃圾代码如下：
```
#调用requests模块和lxml模块
import requests
from lxml import etree
#这个就是request请求的http头信息，包含cookie、referer等等信息
headers = {
    'User-Agent':'Mozilla/5.0 (Windows NT 10.0; WOW64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/58.0.3029.110 Safari/537.36 SE 2.X MetaSr 1.0',
    'Referer':'https://www.baidu.com/link?url=MRvdxSZC9aYdnFEWbd9BPJdrjx8YPiTvupfDZbcSYeZG3WMh-geRF6lOw01G2IEAEHmn1pN_hs7c-koUEM7ue_&wd=&eqid=9aad4eff00025aee000000055c7cc915',
    'cookie':'"108309"; bid=MTknVW88GDQ; douban-fav-remind=1; _vwo_uuid_v2=D3302DED1407AB93A297C5810BEFD7577|9da98df2d8a8582bf7359f380e616274; gr_user_id=168cab1c-8628-4e85-b695-611a4a68cb3b; __yadk_uid=xvOCcG6Ur5ypKFD5qkZxycUS5vKFYgHp; _pk_ref.100001.8cb4=%5B%22%22%2C%22%22%2C1551681820%2C%22https%3A%2F%2Fwww.baidu.com%2Flink%3Furl%3DMRvdxSZC9aYdnFEWbd9BPJdrjx8YPiTvupfDZbcSYeZG3WMh-geRF6lOw01G2IEAEHmn1pN_hs7c-koUEM7ue_%26wd%3D%26eqid%3D9aad4eff00025aee000000055c7cc915%22%5D; _pk_id.100001.8cb4=0255d89350d40572.1551366373.3.1551681820.1551537520.; _pk_ses.100001.8cb4=*; __utmt=1; __utma=30149280.1243716012.1551188924.1551537504.1551681825.5; __utmb=30149280.1.10.1551681825; __utmc=30149280; __utmz=30149280.1551681825.5.5.utmcsr=baidu|utmccn=(organic)|utmcmd=organic'
    }
#这下面with open这一段就是打开一个文件，保存数据用的！
with open('D:/Program Files/软件编程/Python/网页扒取/联系人.txt','a',encoding='utf-8') as f:
#这一段代码是写了循环，也就是说看你需要获取数据的页面有多少个就写几个循环,但是这里有个坑需要注意，注意下面这个url里面start={}这个参数，这个start最开始第一个页面的时候是等于0的，第二页等于25，第三页等于50每次增加25，所以我就直接赋值了一个变量a进去，这样就实现了循环的效果！
    for a in range(4):
        url = 'https://www.douban.com/doulist/45004834/?start={}&sort=time&playable=0&sub_type='.format(a*25)
        data = requests.get(url,headers=headers).text
		
#这一段开始就是使用lxml模块你面的xpath方法，开始写正则表达式来匹配我们自己所需要的内容了下面的line = html.xpath()里面的就是匹配的规则！
        html = etree.HTML(data)
#这一段就是匹配整本书名，所有整本书都是这个标签，最开始我还给干懵逼了，你说气人不！
        line= html.xpath('//*[@id="content"]/div[1]/div[1]')
#限制扒取速度
        time.sleep(3)
#这里写循环，也就是从上面匹配整本书名的标签中找取对应的书名和评分等等！然后因为我怕取到list为空值的选项，所以写了一个len判断！然后输出保存即可！
        for div in line:
            name = div.xpath('./div[5]/div/div[2]/div[3]/text()')
            number = div.xpath('./div[5]/div/div[2]/div[4]/text()')
            if len(name):
                print(name[0],number[0])
                f.write("{} {}\n".format(name[0], number[0]))

            else:
                print("未知电话姓名")
```
&#160; &#160; &#160; &#160;因为从未写过程序，以前用过的也是易语言写的，撸代码能力不咋地，大佬勿喷，以后尽量改正！
## 用户交互程序笔记
&#160; &#160; &#160; &#160;输入函数input()，举例代码(普通输出)：
```
username = input("username:")
password = input("password:")
print(username,password)
```
&#160; &#160; &#160; &#160;三种格式化输出方式，方式一（字符串拼接输出方式，不建议使用）：
```
Name = input("name:")
Age = input("age:")
Job = input("job:")
Salary = input("salary:")

#下面这些参数不是参数名，是变量名
info = '''
----------  info of'''+ Name +''' ----------
Name:''' + Name +'''
Age:''' + Age + '''
Job:''' + Job +'''
Salary:'''+ Salary +'''
'''
print(info)
```
&#160; &#160; &#160; &#160;方式二（百分号输出方式）：
```
name = input("name:")
age = input("age:")
job = input("job:")
salary = input("salary:")

info = '''
----------  info of %s----------
Name:%s
Age:%s
Job:%s
Salary:%s
'''%(name,name,age,job,salary)
print(info)
```

打印变量数据类型和强制转换数据类型例子:
```
name = input("name:")
age = int(input("age:"))	#强制转换为int类型
print(type(age))			#打印字符串类型
print(type(age),type(str(age))) #强制输出为字符型
job = input("job:")
salary = input("salary:")

info = '''
----------  info of %s----------
Name:%s
Age:%d
Job:%s
Salary:%s
'''%(name,name,age,job,salary)
print(info)
```

方式三（format输出方式）：
```
name = input("name:")
age = input("age:")
job = input("job:")
salary = input("salary:")

#下面这些参数不是参数名,也不上面的变量名，而是新定义的变量
info = '''
----------   info of  {_Name} ----------
Name:{_Name}
Age:{_Age}
Job:{_Job}
Salary:{_Salary}
'''.format(_Name=name,
           _Age=age,
           _Job=job,
           _Salary=salary)

print(info)
#或者如下这种输出方式
info2 = '''
----------   info of  {0} ----------
Name:{0}
Age:{1}
Job:{2}
Salary:{3}
'''.format(name,age,job,salary)

print(info2)
```


## 字符串说明：
```
%% 百分号标记 #就是输出一个%
%c 字符及其ASCII码
%s 字符串
%d 有符号整数(十进制)
%u 无符号整数(十进制)
%o 无符号整数(八进制)
%x 无符号整数(十六进制)
%X 无符号整数(十六进制大写字符)
%e 浮点数字(科学计数法)
%E 浮点数字(科学计数法，用E代替e)
%f 浮点数字(用小数点符号)
%g 浮点数字(根据值的大小采用%e或%f)
%G 浮点数字(类似于%g)
%p 指针(用十六进制打印值的内存地址)
%n 存储输出字符的数量放进参数列表的下一个变量中
```