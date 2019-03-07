---
title: python学习第二天
date: 2019-03-07 10:49:35
tags: 学习笔记
categories: 软件开发
---
## python学习第二天
&#160; &#160; &#160; &#160;嗯，虽说是第二天，因为基础差，三天才看了两天的内容，还没看完，只是把第二天的内容学习了以后自己整理出来做了笔记！主要有判断、循环、列表、元组、copy和切片！
<!--more-->
## 判断
&#160; &#160; &#160; &#160;主要是if....else判断，例子如下：
### 单判断
```
import getpass  #密码加密模块getpass

_username = 'zhangsan'
_password = '123456'
username = input("username:")
password = getpass.getpass("password:")

if _username == username and _password == password:
    print("Welcome user {name} login...".format(name=username))
else:
    print("账号或密码错误！")
```
### 多判断
```
age = 25
guess_age = int(input("guess age:"))
if guess_age == age :
    print("yes,you got it.")
elif guess_age > age :
    print("think smaller...")
else:
    print("think bigger...")
```
## 循环
&#160; &#160; &#160; &#160;循环分为while循环和for循环，各自用法用处不同，举例说明：
### while循环
```
age = 25

count = 0                                       #计数开始！
while True:                                     #循环开始！
    if count == 3:                              #判断计数！
        break                                   #满足计数以后终止程序！
    guess_age = int(input("guess age:"))        #用户输入！
    if guess_age == age :                       #判断用户输入！
        print("yes,you got it.")
        break                                   #正确输出以后终止程序！
    elif guess_age > age :
        print("think smaller...")
    else:
        print("think bigger...")
    count +=1                                   #循环，每次计数加一！
```
### while循环进阶
```
age = 25
count = 0                                       #计数开始！
while count < 3:                                #判断count的值是否小于3，小于3则向下执行，否则终止程序！
    guess_age = int(input("guess age:"))        #用户输入！
    if guess_age == age :                       #判断用户输入！
        print("yes,you got it.")
        break                                   #正确输出以后终止程序！
    elif guess_age > age :
        print("think smaller...")
    else:
        print("think bigger...")
    count +=1                                   #循环，每次计数加一！切记这个count要与前面循环开始对齐！
    if count == 3:
        again = input("是否再来一次？请输入'yes'或者'no'")
        if again == 'yes':
            count = 0
        else:
            print("you have tried too many times..fuck off")
```
### for循环
```
age = 25
for i in range(3):                              #判断count的值是否小于3，小于3则向下执行，否则终止程序！
    guess_age = int(input("guess age:"))        #用户输入！
    if guess_age == age :                       #判断用户输入！
        print("yes,you got it.")
        break                                   #正确输出以后终止程序！
    elif guess_age > age :
        print("think smaller...")
    else:
        print("think bigger...")
else:
    print("you have tried too many times..fuck off")
```
### for循环进阶
```
for i in range(10):
    if i<5:
        print("loop",i)
    else:
        continue                 								#continue的意思是跳出本次循环，继续下一次循环，break是结束整个循环！！！
    print("hehe....")
```
## 列表
### 简单的列表应用
```
names= ["张三","李四","王五","赵六"]
names.append("朱七")                          #追加到最后
names.insert(1,"谭八")                        #插入到某个位置之后
names.insert(3,"熊九")                        #加入上一行以后计算位数
names[2] = "囚牛"                             #在原本第一个names的基础上改写
names.remove("张三")                         #以下三行均为删除第一个
del names[0]
names.pop(0)
print(names)
print(names.index("王五"))                    #打印出王五的位置
print(names[names.index("王五")])             #根据王五的位置打印出王五
print(names.count("王五"))                    #统计王五的个数
names.clear()                                 #清空
names.reverse()                               #反转
names.sort()  
```
## 元组
&#160; &#160; &#160; &#160;元组和列表的区别在于元组一旦创建，无法修改，所以元组又叫只读列表,如下例子：
```
names = ('张三','李四')
names.append('王五')
```
&#160; &#160; &#160; &#160;如上操作元组会直接报错
## copy
&#160; &#160; &#160; &#160;copy分为浅copy和深copy,具体例子如下：
### 浅copy
例子一（简单的浅copy）：
```
names = ["张三","李四","王五",["朱七","熊八"],"赵六"]
name2 = names.copy()
print(names)
print(name2)
#把names中的内容copy一份到name2，再修改names列表中的李四一项为拼音
names[1] = "lisi"
print(names)
print(name2)
```
例子二（浅copy理解）：
```
names = ["张三","李四","王五",["朱七","熊八"],"赵六"]
name2 = names.copy()
print(names)
print(name2)
#把names中的内容copy一份到name2，再修改names列表中的李四一项为拼音
names[1] = "lisi"
#下面一段话是改变names列表里面子列表中的第一个数据，所以在浅copy的时候，只是copy了第一层的数据，但是对于子列表即第二层的数据是无法copy的，也可以理解为第二层的数据其实是在names列表第三个数据这里存了一个内存地址，在修改第二层数据的时候，实际上是修改第三个数据所在地址里面的内容。所以导致了修改子列表中的内容时直接把names和name2的内容全部修改了！
names[3][0]="zhuqi"
print(names)
print(name2)
```
例子三（引用copy模块的浅copy）:
```
import copy
names = ["张三","李四","王五",["朱七","熊八"],"赵六"]
#这里的copy.copy(names)和前面的names.copy()是一样的，都是属于浅copy，所以输出一样
name2 = copy.copy(names)
print(names)
print(name2)
#把names中的内容copy一份到name2，再修改names列表中的李四一项为拼音
names[1] = "lisi"
#下面一段话是改变names列表里面子列表中的第一个数据，所以在浅copy的时候，只是copy了第一层的数据，但是对于子列表即第二层的数据是无法copy的，也可以理解为第二层的数据其实是在names列表第三个数据这里存了一个内存地址，在修改第二层数据的时候，实际上是修改第三个数据所在地址里面的内容。所以导致了修改子列表中的内容时直接把names和name2的内容全部修改了！
names[3][0]="zhuqi"
print(names)
print(name2)
```
### 深copy
&#160; &#160; &#160; &#160;深copy需要引用copy模块才能够做到，如下例子：
```
import copy
names = ["张三","李四","王五",["朱七","熊八"],"赵六"]
#这里的copy.deepcopy(names)就表示深copy，即把names中的所有内容包括第二层的数据完全copy到name2
name2 = copy.deepcopy(names)
print(names)
print(name2)
#把names中的内容copy一份到name2，再修改names列表中的李四一项为拼音
names[1] = "lisi"
#下面一段话是改变names列表里面子列表中的第一个数据，所以在浅copy的时候，只是copy了第一层的数据，但是对于子列表即第二层的数据是无法copy的，也可以理解为第二层的数据其实是在names列表第三个数据这里存了一个内存地址，在修改第二层数据的时候，实际上是修改第三个数据所在地址里面的内容。所以导致了修改子列表中的内容时直接把names和name2的内容全部修改了！
names[3][0]="zhuqi"
print(names)
print(name2)
```
## 切片
&#160; &#160; &#160; &#160;所谓的切片，其实就是把列表里面的内容按照需求切片打印出来，获得想要的结果，如下例子：
```
names = ["张三","李四","王五",["朱七","熊八"],"赵六"]
print(names[0:-1:2])    #每隔一行打印一个
print(names[::2])       #也是每隔一行打印一个
print(names[:])         #完全切片
```