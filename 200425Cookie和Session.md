---
title: 200425Cookie和Session
comments: true
tags:
  - http
  - 网络
categories: Web
date: 2020-04-25 21:28:32
---
## Cookie
> 其实是一份小数据,是服务器给客户端的,并存储在客户端的一份小数据
> 自动登录,浏览记录,购物车
### 为什么要有这个cookie
> http的协议是无状态的,客户端与服务器在通讯的时候是无状态的,其实就是客户端第二次来访的时候服务端根本就不知道这个客户端的记录.为了更好的用户体验,更好的交互,使用cookie.其实从公司层面想,就是需要更好的收集用户习惯
### cookie有什么用
### 简单使用
1. 在响应的时候,添加cookie
2. 客户端收到的信息里面,响应头的字段多了一个 cookie
3. 当浏览器再请求该网站时，浏览器把请求的网址连同该Cookie一同提交给服务器。服务器检查该Cookie，以此来辨认用户状态。服务器还可以根据需要修改Cookie的内容。


### Cookie具有不可跨域名性
>浏览器访问Google只会携带Google的Cookie，而不会携带Baidu的Cookie。Google也只能操作Google的Cookie，而不能操作Baidu的Cookie。
###  Cookie的修改、删除
> Cookie并不提供修改、删除操作。如果要修改某个Cookie，只需要新建一个同名的Cookie，添加到response中覆盖原来的Cookie。
## Session
>除了使用Cookie，Web应用程序中还经常使用Session来记录客户端状态。Session是服务器端使用的一种记录客户端状态的机制，使用上比Cookie简单一些，相应的也增加了服务器的存储压力。
1. 如果说Cookie机制是通过检查客户身上的“通行证”来确定客户身份的话，那么Session机制就是通过检查服务器上的“客户明细表”来确认客户身份。Session相当于程序在服务器上建立的一份客户档案，客户来访的时候只需要查询客户档案表就可以了。
2. Session机制决定了当前客户只会获取到自己的Session，而不会获取到别人的Session。各客户的Session也彼此独立，互不可见。
3. Session的使用比Cookie方便，但是过多的Session存储在服务器内存中，会对服务器造成压力。

### Session的生命周期
1. Session保存在服务器端。为了获得更高的存取速度，服务器一般把Session放在内存里。每个用户都会有一个独立的Session。如果Session内容过于复杂，当大量客户访问服务器时可能会导致内存溢出。因此，Session里的信息应该尽量精简。
2. Session在用户第一次访问服务器的时候自动创建。。需要注意只有访问JSP、Servlet等程序时才会创建Session，只访问HTML、IMAGE等静态资源并不会创建Session。如果尚未生成Session，也可以使用request.getSession(true)强制生成Session。
3. Session生成后，只要用户继续访问，服务器就会更新Session的最后访问时间，并维护该Session。用户每访问服务器一次，无论是否读写Session，服务器都认为该用户的Session“活跃（active）”了一次。

### Session的有效期
由于会有越来越多的用户访问服务器，因此Session也会越来越多。**为防止内存溢出，服务器会把长时间内没有活跃的Session从内存删除。这个时间就是Session的超时时间。如果超过了超时时间没访问过服务器，Session就自动失效了。**

## 两者的区别
1. cookie数据存放在客户的浏览器上，session数据放在服务器上.客户端每次请求服务器的时候会发送 当前会话的session_id，服务器根据当前session_id判断相应的用户数据标志，以确定用户是否登录，或具有某种权限。
2. cookie不是很安全，别人可以分析存放在本地的COOKIE并进行COOKIE欺骗考虑到安全应当使用session。
3. 设置cookie时间可以使cookie过期。但是使用session-destory（），我们将会销毁会话。
4. session会在一定时间内保存在服务器上。当访问增多，会比较占用你服务器的性能考虑到减轻服务器性能方面，应当使用cookie。
5. 单个cookie保存的数据不能超过4K，很多浏览器都限制一个站点最多保存20个cookie。(Session对象没有对存储的数据量的限制，其中可以保存更为复杂的数据类型)
6.  两者最大的区别在于生存周期，一个是IE启动到IE关闭.(浏览器页面一关 ,session就消失了)，一个是预先设置的生存周期，或永久的保存于本地的文件。(cookie)
7.  Session信息是存放在server端，但session id是存放在client cookie的，当然php的session存放方法是多样化的，这样就算禁用cookie一样可以跟踪.Cookie是完全保持在客户端.


## 值得注意的地方
1. cookie一定要设置失效时间,要不然浏览器关闭就消失了;
2. 浏览器 有禁止cookie功能 ,但一般用户都不会设置;
3. cookie也分为永久和暂时存在的;
4. 虽然cookie不安全,但是可以加密 ;
5. session很容易失效,用户体验很差;