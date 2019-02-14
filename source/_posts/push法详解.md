---
title: push法详解
date: 2018-09-17 10:35:21
tags: 学习笔记
categories: 逆向破解
---
## push法概述
&#160; &#160; &#160; &#160;所谓的push法，就是使用push命令，查找易语言窗体或者按钮。主要是针对易语言，同样是针对市面上常见的游戏外挂，现在市面的游戏外挂，普遍是易语言写的。本次要讲的就是push 25命令，查找易语言窗体事件！
<!--more-->
## push法具体例子一
&#160; &#160; &#160; &#160;如下图所示自写小程序：
[![iZmwvj.png](https://s1.ax1x.com/2018/09/17/iZmwvj.png)](https://imgchr.com/i/iZmwvj)
&#160; &#160; &#160; &#160;载入OD分析一波，如图所示：
[![iZmDrn.png](https://s1.ax1x.com/2018/09/17/iZmDrn.png)](https://imgchr.com/i/iZmDrn)
&#160; &#160; &#160; &#160;直接Ctrl+B，搜索二进制字符串FF 25窗体事件，来到窗体，如图所示：
[![iZneLn.png](https://s1.ax1x.com/2018/09/17/iZneLn.png)](https://imgchr.com/i/iZneLn)
[![iZnnZq.png](https://s1.ax1x.com/2018/09/17/iZnnZq.png)](https://imgchr.com/i/iZnnZq)
&#160; &#160; &#160; &#160;如上第二幅图所示，程序来到窗体事件，因为所有的易语言窗体，都需要通过一个jmp来实现跳转，所以，程序直接来到了jmp处！往上拉一点可以看见主窗体和窗口1窗体，如图下图所示（注意：并不是所有的窗体代码都能够直接这样看见，比如加壳的程序，或者很多窗口的程序，有可能jmp和窗口1的代码就相距很远，这里仅仅是个小程序，要根据实际情况来分析！）：
[![iZnsQH.png](https://s1.ax1x.com/2018/09/17/iZnsQH.png)](https://imgchr.com/i/iZnsQH)
&#160; &#160; &#160; &#160;如上图所示，选中的部分就是窗口1的代码，下面箭头指的就是主窗口的push，上面箭头指的就是窗口1的push值！这里可以直接改主窗口的push值，让程序打开就是窗口1，具体操作详见《世恒破解教程一期》。通过上面截图代码可以看见窗口1最后有一个call，其实这个call就是把前面的代码压栈到里面，然后弹出窗口1，在这里下断点，运行程序，点击“登录”，如图所示：
[![iZKzL9.png](https://s1.ax1x.com/2018/09/17/iZKzL9.png)](https://imgchr.com/i/iZKzL9)
&#160; &#160; &#160; &#160;如上图所示，程序点击“登录”后在这个call被断了下来，查看反汇编窗口中的代码和堆栈窗口（右下角为堆栈窗口）代码，发现一模一样，但是堆栈窗口代码顺序和反汇编窗口的代码顺序刚好相反，这是因为栈空间需要满足先进后出，先出后进的原则，也就是说，最先入栈的代码在最下面，所以刚好相反。关于汇编知识，个人推荐小甲鱼的汇编，他就详细的把王爽的汇编解释了一下！不过这不是这里的重点，继续分析！
&#160; &#160; &#160; &#160;到了这里以后，F7进call看一下，发现程序直接就到了jmp这里，如图所示：
[![iZMudI.png](https://s1.ax1x.com/2018/09/17/iZMudI.png)](https://imgchr.com/i/iZMudI)
&#160; &#160; &#160; &#160;如上图所示，这是因为易语言程序，不管前面代码怎么调用，都会经过这几个jmp，所以，易语言的程序，关键点就在这些jmp上面，如果前面的代码被V（VMP加壳）了，那就可以从这里下手，下面就演示一下给这个程序加壳！把窗口1的代码给V掉，如图所示：
[![iZMNes.png](https://s1.ax1x.com/2018/09/17/iZMNes.png)](https://imgchr.com/i/iZMNes)
[![iZMaoq.png](https://s1.ax1x.com/2018/09/17/iZMaoq.png)](https://imgchr.com/i/iZMaoq)
[![iZMgm9.png](https://s1.ax1x.com/2018/09/17/iZMgm9.png)](https://imgchr.com/i/iZMgm9)
[![iZM2wR.png](https://s1.ax1x.com/2018/09/17/iZM2wR.png)](https://imgchr.com/i/iZM2wR)
[![iZMRT1.png](https://s1.ax1x.com/2018/09/17/iZMRT1.png)](https://imgchr.com/i/iZMRT1)
&#160; &#160; &#160; &#160;如上几幅图所示，就是给窗口1代码加壳，因为这里不需要特别麻烦，仅仅只是个演示，所以仅仅只是把窗口1虚拟化掉了，其他比如变异这些功能都没有加上，详细加壳怎么加，可以执行百度，加好壳以后，载入OD，看看程序变化，如图：
[![iZMISO.png](https://s1.ax1x.com/2018/09/17/iZMISO.png)](https://imgchr.com/i/iZMISO)
&#160; &#160; &#160; &#160;如图所示，可以看见窗口1的代码被整个虚拟化了，根本看不出来原本的样子！但是下面的主窗口部分还是没有变化，前面说了，易语言的关键点在于跳转jmp，所以，直接到jmp哪儿去下断，但是这里有四个jmp，在不知道具体是哪个jmp的情况下，就都下断，看看哪个jmp在点击登录的时候断下来了那么它就是关键的jmp，也称为关键跳！如图所示：
[![iZM7OH.png](https://s1.ax1x.com/2018/09/17/iZM7OH.png)](https://imgchr.com/i/iZM7OH)
[![iZMq0A.png](https://s1.ax1x.com/2018/09/17/iZMq0A.png)](https://imgchr.com/i/iZMq0A)
&#160; &#160; &#160; &#160;如上几幅图所示，第四个jmp停止了断了下来，然后看右下角堆栈窗口可以看出来，堆栈里面的代码，还是原本没有被V的代码，那就可以尝试着直接修改主窗体的push值，直接打开程序就来到窗口1，如图所示：
[![iZQp6g.png](https://s1.ax1x.com/2018/09/17/iZQp6g.png)](https://imgchr.com/i/iZQp6g)
[![iZQ9XQ.png](https://s1.ax1x.com/2018/09/17/iZQ9XQ.png)](https://imgchr.com/i/iZQ9XQ)
&#160; &#160; &#160; &#160;如图所示，当把主窗体的push值改为窗口1的值以后，程序直接进入窗口1了！！
## push法具体例子二——还原被V代码
&#160; &#160; &#160; &#160;上面例子一讲述了push法找到关键jmp，然后可以通过修改主窗体的push值，直接进入程序，下面来看一下，程序被V以后还原，如图所示自写小软件：
[![iZ3H8P.png](https://s1.ax1x.com/2018/09/17/iZ3H8P.png)](https://imgchr.com/i/iZ3H8P)
&#160; &#160; &#160; &#160;如图所示，这个弹窗软件由一个变为两个窗口后，V掉一个窗口，把窗口2V掉，然后还原，先V掉窗口2代码，载入OD（具体V掉过程前面已经大概演示，就不做截图演示了），如图所示：
[![iZ8ZVJ.png](https://s1.ax1x.com/2018/09/17/iZ8ZVJ.png)](https://imgchr.com/i/iZ8ZVJ)
&#160; &#160; &#160; &#160;如图所示，窗口2代码被V以后完全看不出来原本的代码，按照案例一的办法直接在jmp下断，然后运行，点击登录2，断下来以后看堆栈窗口，如图所示：
[![iZ8K8x.png](https://s1.ax1x.com/2018/09/17/iZ8K8x.png)](https://imgchr.com/i/iZ8K8x)
&#160; &#160; &#160; &#160;如图所示，堆栈窗口的代码就是原本没有被V掉的正常代码，刚好窗口1的代码也能够正常显示，就可以尝试根据窗口1的代码把窗口2的代码还原，代码如下：
```
push ebp
mov ebp,esp
push 0x80000002
push 0x0
push 0x1
push 0x0
push 0x0
push 0x0
push 0x10001
push 0x601000A
push 0x5201000B
push 0x3
mov ebx,2个窗口_.004010D0
call 2个窗口_.004010C5
add esp,0x28
mov esp,ebp
pop ebp
retn                  
```
&#160; &#160; &#160; &#160;如上代码中，`push ebp` `mov ebp,esp`为创建子程序代码，也是窗口2的开始代码，最后的`mov esp,ebp` `pop ebp` `retn`为创建子程序结束代码，也是窗口2的结束代码。窗口2和窗口1不同之处就在于代码第十行和第十一行，窗口1为`push 0x6010005` `push 0x52010006`所以这里根据堆栈窗口的值，改一下，其他全部一样的，包括`call`里面的值，直接复制粘贴就行，然后把多余的代码直接`Nop`填充掉即可，最后如下图所示：
[![iZ8TZ4.png](https://s1.ax1x.com/2018/09/17/iZ8TZ4.png)](https://imgchr.com/i/iZ8TZ4)
&#160; &#160; &#160; &#160;最后保存修改的代码，执行一下，看看会不会报错，如图：
[![iZ8Ho9.png](https://s1.ax1x.com/2018/09/17/iZ8Ho9.png)](https://imgchr.com/i/iZ8Ho9)
## 总结
&#160; &#160; &#160; &#160;针对易语言而言，不管代码怎么改变，jmp关键跳转不会变，除非把jmp给V掉，不然可以利用这一点，对登录进行破解。同时，jmp上面一点肯定是主窗体的`push`这一点是不会改变的！也算是一个小的特征。同时，也可以利用关键jmp对已经被V的部分代码进行还原。这需要根据实际情况来看，演示的代码不大，就几句话，要是多了情况另说，程序大了，jmp也可能会很多！实际情况实际轮！
>谢谢大家关注