---
title: python学习第三天
date: 2019-03-08 15:03:45
tags: 学习笔记
categories: 软件开发
---
## python学习第二天
&#160; &#160; &#160; &#160;今天主要写了一下购物车程序，然后就是字符串、字典、多字典嵌套等操作！
<!--more-->
## 购物车程序代码
```
#定义商品列表
product_list =[
    ('手机',4999),
    ('电脑',7499),
    ('《武林外传》',89),
    ('冰红茶',3),
    ('1T移动硬盘',599),
    ('雷柏蓝牙无线鼠标',79),
    ('高级保温茶杯',89)
]
#定义一个空的购物车
shoping_list = []
#用户输入资金，并且判断输入的是否是数字，如果是，则把数字转换成int类型！
money = input("请输入你的资金：")
if money.isdigit():
    money = int(money)
    #进入死循环
    while True:
        #输出商品列表，这里如果不适用xx.index(ss),ss方法，直接使用print(ss)的话，输出的商品会没有前面的下标排序！
        for item in product_list:
            print(product_list.index(item),item)
            #另一种下标输出写法,个人喜欢上面一种，易懂！
            #for index,item in enumerate(product_list):
            #    print(index,item)
        user_choose = input("请输入你要选择的商品：")
        #判断输入的是否是数字
        if user_choose.isdigit():
            #如果是数字转换为int类型
            user_choose = int(user_choose)
            #判断输入的数字是否在0-6之间，下面的len()函数是计算长度的
            if user_choose< len(product_list) and user_choose>=0:
                #这里根据输入的数字，取对应的商品
                p_item = product_list[user_choose]
                #根据商品价格和用户的资金判断是否买的起此商品！
                if p_item[1] <= money:
                    #把商品添加到购物车里面去
                    shoping_list.append(p_item)
                    #添加到购物车以后，用用户输入的资金减去商品的价格！
                    money -=p_item[1]
                    #输出购买商品后的余额
                    print("您此次购买%s成功，剩下\033[31;1m%s\033[0m元钱！" %(p_item,money))
                #如果资金不足以购买商品，提示购买其他商品
                else:
                    print("\033[41;1m抱歉，你的资金只有%s,无法购买此类商品，请选择其他商品！\033[0m"%(money))
        #判断用户输入的是不是"q"
        elif user_choose == "q":
            #如果用户输入的是q，则打印输出购物车里面的内容，显示余额并退出！
            print("------------shoping list------------")
            for index,p in enumerate(shoping_list):
                print(index,p)
            print("您当前的余额为：\033[31;1m%s\033[0m"%(money))
            exit()
        #判断用户是否输入的其他什么内容，如果是直接输出错误选项！
        else:
            print("错误选项！")
#判断用户是不是输入的数字，不是直接提示输入数字！
else:
    print("请输入数字！")
```
## 字符串操作
```
name = "my \tname is zhangsan"
print(name.capitalize())            #首字母大写
print(name.count("n"))              #统计指定数据数量
print(name.center(50,"-"))          #打印多少个字符，不够用指定数据补上
print(name.encode())                #字符串转换成为二进制
print(name.endswith("n"))           #判断是不是以什么结尾，这里指定以N结尾
print(name.expandtabs(tabsize=20))  #把table键转为指定的空格
print(name.find("name"))            #从下标0开始找，找指定的字符，输出字符位置
```
## 字典
```
#key-value
info = {
    'stu0001':"zhangsan",
    'stu0002':"lisi",
    'stu0003':"wangwu"
}
#查数据（注意，字典是根据key来查找，字符串或者列表根据下标来）
print(info["stu0001"])      #基本用法
print(info.get("stu0004"))  #此方法是获取，没有输出None,上一种没有数据直接报错！
print('stu0004' in info)    #此方法是判断，输出True/False
#改数据
info["stu0001"] = "zhaoliu"
#增加数据
info["stu0004"] = "zhuqi"
print(info)
#删除数据
del info["stu0001"]     #指定删除
info.pop("stu0002")     #指定删除
info.popitem()          #随机删除
print(info)
```
## 多字典嵌套
```
info = {
    "重庆":{
        "巴南":["地大物薄","房价相对不算太贵"],
        "城口":["重庆最远的地方之一","坐车大半天"],
        "渝中":["重庆繁华地段","房价特别贵"]
    },
    "上海":{
        "黄浦":["123","321"],
        "长宁":["123","321"],
        "虹口":["123","321"]

    },
    "北京":{
        "不知道":["234","432"]

    }

}
info["上海"]["黄浦"][0]="000"
print(info)
```4-