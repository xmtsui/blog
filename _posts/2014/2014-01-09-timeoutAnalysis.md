---
layout: post
title: JDBC Timeout分析
categories:
- java
---

## 问题描述
queryTimeOut设置为60s，但发生了超时后，实际的timeout等待时间超过了60s，WHY?  
并确认和oracle-test的timeout具体差异.

## 分析
常见的JDBC相关的Timeout类型从高到低级别有：
>Transaction Timeout
>>Statement Timeout
>>>Socket Timeout

高级别的timeout依赖于低级别的timeout，只有当低级别的timeout无误时，高级别的timeout才能确保正常。例如，当socket timeout出现问题时，高级别的statement timeout和transaction timeout都将失效。[*点击查看引用文章*](http://www.importnew.com/2466.html "深入理解JDBC的超时设置")

了解了上面的背景，再通过log来分析问题：

## mysql log

{% highlight java %}
Last packet sent to the server was 180536 ms ago.; nested exception is com.mysql.jdbc.exceptions.jdbc4.CommunicationsException : Communications link failure
...
Caused by: java.net.SocketTimeoutException: Read timed out
{% endhighlight %}

这是在一个正常的mysql数据库，针对某一topic的消息执行select count(\*)的操作时抛出的。测试时刚好这个topic下的消息量特别大，根据desc，asc排序算出的max与min id差值有6000多w，但由于id是不是连续的，所以实际值应该比这个小很多，不过也应该超过了1000w。
对于Socket Timeout的判断，实际是通过是否有数据返回来判定超时[*点击查看引用ppt 28-29页*](http://vdisk.weibo.com/s/Ey4_gkMI0fLp "JDBC优化的意外之旅---樊振华 28-29页")。  
因为此时数据量刚好特别大，select count(\*) 会一直计算，直到得出结果才返回给客户端。在我们的环境中，QueryTimeout设定为60s，SocketTimeout设定为120s。返回结果的时间就超过了db设定的SocketTimeout值，然后就抛出SocketTimeoutException: Read timed out。

* **那么为什么queryTimeOut时间短却没有抛出，反而抛出的是时间长的120s呢？**

要分析清楚这个问题，就要结合上面的背景知识，来看下jdbc实现的源代码[*点击查看引用博文*](http://iwinit.iteye.com/blog/1933399 "query timeout实现分析")。

根据前面的背景知识，高级别的timeout依赖于低级别的timeout，只有当低级别的timeout无误时，高级别的timeout才能确保正常。
发送了select count(\*)后，db驱动会创建一个新的timeout-execution线程用于超时处理，实现类是CancelTask。CancelTask如果能成功关闭Query操作，那么QueryTimeout成功，否则，关闭Query操作失败，并抛出异常给主线程。
此时db查询结果集太大，在执行期间不会有数据传给客户端，所以客户端在等待最终结果返回时候，被当做了DB宕机或是发生网络错误。QueryTimeout到了60s触发后，结果还是没有返回完成，在CancelTask中关闭Query操作失败。主线程还会等待server端返回，直到Socket Read Timeout抛出异常。

再来看下log里显示的时间`Last packet sent to the server was 180536 ms ago`  
这里的超时时间为180536（不是精确的），约等于3分钟，刚好是我们的queryTimeout+SocketTimeout。

## oracle log

{% highlight java %}
ORA-01013: user requested cancel of current operation
{% endhighlight %}

Oracle在实现超时处理上与Mysql不太一样，没用通过子线程复制一个新的connection出来，再去向DBMS发命令，而是通过OracleTimeoutPollingThread调用OracleStatement的cancel()方法。根据目前的测试情况来看不会出现SocketTimeOut的情况。Oracle的超时参数设置可以参考这里[*点击查看引用文章*](http://www.dbafree.net/?p=957 "Oracle querytimeout的说明")
关于Oracle还没有深入分析，不过可以接着参考这个DBA大牛的博客[*点击查看引用文章*](http://www.dbafree.net/?p=1030 "Oracle 超时参数设置")

[1]:http://www.importnew.com/2466.html "深入理解JDBC的超时设置"
[2]:http://vdisk.weibo.com/s/Ey4_gkMI0fLp "JDBC优化的意外之旅---樊振华 28-29页"
[3]:http://iwinit.iteye.com/blog/1933399 "query timeout实现分析"
[4]:http://www.dbafree.net/?p=957 "Oracle querytimeout的说明"
[5]:http://www.dbafree.net/?p=1030 "Oracle 超时参数设置"