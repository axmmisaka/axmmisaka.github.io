---
title: 假电子邮件-它是怎么产生的以及如何避免|Fake E-mails - How They'are Generated and How to Avoid
date: 2018-12-16 22:26:48
tags:
- Chinese 
- Email
---
## 引言

我们想象这样一个场景：早晨起床，你喝了一杯咖啡，打开你的电脑查收电子邮件，然后发现了这样一个新邮件：  
![邮件标题](/images/fakeemail/email_title.png)  
哇！你喜极而泣，急忙点开，在你准备感谢政府和全国各族人民的时候，你发现事情并不是这么简单：  
![邮件内容](/images/fakeemail/email_data.png)  
没错，这是一封假邮件，而且你被蒙了。不过现实中，这种事情并不会发生——因为这封邮件其实会被标记成垃圾邮件：这又是为什么呢？接着往下看吧。

## SMTP 协议

简单邮件传输协议 (Simple Mail Transfer Protocol, SMTP) 是在互联网传输电子邮件的事实标准。  
它本身**没有**身份验证机制，所以理论上你可以随意伪造发件人。现在我们都用电子邮件客户端和网页客户端来传输邮件，所以大多数人并不知道SMTP是怎么工作的。不过不要紧，现在你可以试试：  
（以下的所有命令都在 Ubuntu 18.04 LTS 上运行。OpenSSL 1.1.0g）  
首先，我们需要一个邮件服务器。我手头有一个为模联会议准备的腾讯企业邮箱，现在已经不用了。这是一个绝佳的带有SMTP支持的邮箱。  
接着，将你的邮箱和密码使用默认的base64加密。在我的zsh上，我可以这样来得到结果：  
假设我的邮箱地址是 ` obama@wh.gov ` ，那么，我将运行这个命令：
```bash
echo "obama@wh.gov` | base64
```
屏幕输出 `b2JhbWFAd2guZ292Cg==` 便是我们想要的结果。  
接着我们对密码做同样的事情：假设密码是“donald”，那么结果将是 `ZG9uYWxkCg==` 。  
然后，明确你SMTP服务器的主机名（假设是`email.wh.gov`）和端口（SMTP的默认端口都是25，不过也有其他的），以及其是否强制要求使用SSL。如果不需要，你可以直接使用这个命令连接：
```bash
telnet email.wh.gov 25
```
不过如果需要，那可能就比较麻烦了：你需要使用OpenSSL - 也就是像我这里一样。我不会解释具体的参数，你可以参阅OpenSSL的文档来了解。
```bash
openssl s_client -starttls smtp -connect email.wh.gov:25 -crlf -ign_eof
```
接下来，请看我的输入（以>开头）和服务器的回复和注释（在#后的）来了解我们如何使用我的邮件服务器（不过请假设我仍在使用上面那些认证信息）给 `J. Biden<biden@wh.gov>` 发送一个正常的邮件，标题是“Hello my friend”，内容是“Please reply”。

```
CONNECTED(00000003)
中间的信息省略
---
250 8BITMIME
 > HELO WH #WH可以换成任意的字符
250 email.wh.com
 > AUTH LOGIN
334 VXNlcm5hbWU6 # Base64加密过的Username:
 > b2JhbWFAd2guZ292Cg==
334 UGFzc3dvcmQ6# Base64加密过的Password:
 > ZG9uYWxkCg==
235 Authentication successful
 > MAIL FROM: <OBAMA@WH.GOV>#这里指明是谁发信
250 Ok
 > RCPT TO: JOE<BIDEN@WH.GOV>#这里指明是谁收信
250 Ok
 > DATA
354 End data with <CR><LF>.<CR><LF>
 > FROM: BARACK<OBAMA@WH.GOV>#这里指明邮件中显示的发信人
 > TO: JOE<BIDEN@WH.GOV>#这里指明邮件中显示的收信人
 > SUBJECT: Hello my friend
 > Please reply 
 > . #用句点表示结束
250 Ok: queued as 
 > QUIT #退出
221 Bye
closed
```
这样， ` BIDEN@WH.GOV ` 就会收到一封正常的邮件。一看注释你应该就明白了些什么：FROM:和MAIL FROM:后写什么完全取决于发信人的良心。假如改成 ` DONALD<TRUMP@WH.GOV> ` ，那收到的邮件就会显示发件人为 ` DONALD<TRUMP@WH.GOV> ` 。  
在我的腾讯邮件服务器上，这种事情不会发生：SMTP可能会出现501错误：
```
501 mail from address must be same as authorization user
```
这个服务器会认证一下你的邮件地址是否和你的登录信息一样，如果不一样则返回一个错误。然而，有些服务器并不会这样做。  
只改变FROM: 里的邮件地址也是一种办法，但现在几乎所有的邮件客户端都会显示真实发信人和显示的发信人。主要原因是这是很常见的一种外包邮件服务的办法：比如加州大学伯克利分校的邮件，如果我没记错的话，由Techsolutions的服务器代发。UCB的收件人会看到发件人是 ` admissions@berkeley.edu 通过“technolutions.net” ` 。  
不过有些服务器是不认证的，甚至有专门发假邮件的服务器，比如这个：[Emkei's Fake Mailer](https://emkei.cz/)。

## 反制

看到这里，你可能会惊呼不可战胜。你可能会问，既然SMTP协议这么容易出问题，那么我们为什么还要用呢？  
这个问题可以从多方面解释。一方面，历史遗留问题的确存在——有的服务器建于1980年代，只支持SMTP；而且在内联网（Intranet）中用SMTP收发邮件的确很方便。可能的替代，如微软Exchange ActiveSync，有它自身的局限性。  
你可能更关心另一个问题——既然假邮件这么容易造，我会不会收到假的大学录取通知书？  
答案是否定的。只要你使用正常的电子邮件提供商，一开始的那个哈佛录取通知书和其他伪造的邮件都一定会直接被扔进垃圾箱或者拒收。

### SPF

等我慢慢写吧

### DKIM

等我慢慢写吧

### DMARC

等我慢慢写吧

### 自己动手

等我慢慢写吧
