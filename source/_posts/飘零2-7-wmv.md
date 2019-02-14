---
title: 飘零2.7.wmv
date: 2018-09-20 09:17:54
tags: 学习笔记
categories: 逆向破解
---
## 飘零2.7.wmv网络认证概述
&#160; &#160; &#160; &#160;飘零的网络认证，是一种加了时钟判断和算法特征的网络认证，通过发送本地输入的账号密码发送到服务器端进行算法认证，确实存在该账号才能够进行登录操作！
<!--more-->
## 飘零2.7.wmv网络认证案例
&#160; &#160; &#160; &#160;本次学习中的测试软件因为服务器端已经下架了，所以只能做原理分析，不能够做实际测试。如下图所示的无壳飘零2.7网络认证软件：
[![im3oCR.png](https://s1.ax1x.com/2018/09/20/im3oCR.png)](https://imgchr.com/i/im3oCR)
&#160; &#160; &#160; &#160;如上图所示，一个简单的飘零无壳软件，但是这个软件是加了时钟的，所以需要用内存钩子工具把时钟干掉，如图所示：
[![im8iKf.png](https://s1.ax1x.com/2018/09/20/im8iKf.png)](https://imgchr.com/i/im8iKf)
[![im8Fr8.png](https://s1.ax1x.com/2018/09/20/im8Fr8.png)](https://imgchr.com/i/im8Fr8)
&#160; &#160; &#160; &#160;干掉时钟以后，再到OD中附加，如图所示：
[![im8VaQ.png](https://s1.ax1x.com/2018/09/20/im8VaQ.png)](https://imgchr.com/i/im8VaQ)
&#160; &#160; &#160; &#160;来到易语言处，下好易语言按钮事件断点后让软件运行起来，随便输入账号密码，点击登录，程序在按钮事件登录处断下来，如图所示：
[![im8hsf.png](https://s1.ax1x.com/2018/09/20/im8hsf.png)](https://imgchr.com/i/im8hsf)
[![im84L8.png](https://s1.ax1x.com/2018/09/20/im84L8.png)](https://imgchr.com/i/im84L8)
&#160; &#160; &#160; &#160;如上图所示，程序断下来以后，按F7跟进分析，可以看见提示账号和密码不能为空的提示，返回到源码中查看此处怎么处理的，如图所示：
[![im8Lzq.png](https://s1.ax1x.com/2018/09/20/im8Lzq.png)](https://imgchr.com/i/im8Lzq)
[![im8XQ0.png](https://s1.ax1x.com/2018/09/20/im8XQ0.png)](https://imgchr.com/i/im8XQ0)
&#160; &#160; &#160; &#160;如上图所示，可以从源码中看到，当按钮登录被单击以后，程序开始判断账号和密码是否为空，如果为空则输出提示“用户名不能为空！”和“密码不能为空！”，如果不为空则判断模式，模式判断完成以后，执行代码`登录.禁止=假`下面的子程序。这里到OD中看一下程序判断为什么模式，这里因为服务器端已经下架，不能够做实际测试分析，所以只能够根据教程做原理解释。到了这里判断模式的时候，程序会断在如下图所示的call里面：
[![imWX11.png](https://s1.ax1x.com/2018/09/20/imWX11.png)](https://imgchr.com/i/imWX11)
&#160; &#160; &#160; &#160;如上图所示这个call就是判断模式的。程序从登陆call断下来以后，F7跟进，一路F8单步向下来到这里停下来，然后在下一行这个`mov`这里下断，点击`t`选择激活所有线程后，程序会断在这个`mov`这里，在寄存器`eax`里面右键选择“数据窗口”跟随就可以看见这个模式选择的是模式1，如下图所示：
[![imf4Cd.png](https://s1.ax1x.com/2018/09/20/imf4Cd.png)](https://imgchr.com/i/imf4Cd)
[![imfOUg.png](https://s1.ax1x.com/2018/09/20/imfOUg.png)](https://imgchr.com/i/imfOUg)
&#160; &#160; &#160; &#160;如上图所示，再来看源代码，分析一下程序怎么运行的，当账号密码都不会空时，程序把状态信息发送到服务器判断，当服务器端判断出模式以后，返回到程序，得出结论为模式1，也就是收费模式，然后就执行`登录.禁止=假`里面的收费模式的子程序，如下图所示：
[![imhSvq.png](https://s1.ax1x.com/2018/09/20/imhSvq.png)](https://imgchr.com/i/imhSvq)
&#160; &#160; &#160; &#160;再看看OD中是否也是这样，如图所示：
[![imhUMt.png](https://s1.ax1x.com/2018/09/20/imhUMt.png)](https://imgchr.com/i/imhUMt)
&#160; &#160; &#160; &#160;到了这里以后，进入第一个jnz条件判断下的call里面，这就是收费模式1的call,里面有许许多多的判断，如下图所示：
[![im4MSs.png](https://s1.ax1x.com/2018/09/20/im4MSs.png)](https://imgchr.com/i/im4MSs)
[![im5PNF.png](https://s1.ax1x.com/2018/09/20/im5PNF.png)](https://imgchr.com/i/im5PNF)
&#160; &#160; &#160; &#160;到了这里就不用多说了，可以根据源码来看，直接jmp把所有错误信息全部跳过即可，如图下图所示：
[![im5aE8.png](https://s1.ax1x.com/2018/09/20/im5aE8.png)](https://imgchr.com/i/im5aE8)
[![im54C4.png](https://s1.ax1x.com/2018/09/20/im54C4.png)](https://imgchr.com/i/im54C4)
&#160; &#160; &#160; &#160;如上图所示，直接jmp掉所有错误信息以后，程序就可以登录进去了！