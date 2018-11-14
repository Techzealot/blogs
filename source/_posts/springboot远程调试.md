---
title: springboot远程调试
date: 2018-11-15 00:09:50
tags: ["springboot","调试"]
categories: ["随笔"]
---
## springboot应用远程调试  
Java程序远程调试的原理是基于JDWP(Java Debug Wire Protocol),具体可参考[JDWP 协议及实现](https://www.ibm.com/developerworks/cn/java/j-lo-jpda3/);  
具体步骤如下： 
1. 远程代码必须和本地一致，否则可能出现无法预料的错误！
2. 在启动命令加上如下参数：
-Xdebug -Xrunjdwp:transport=dt_socket,server=y,suspend=n,address=0.0.0.0:10009  
> * -Xdebug是通知JVM工作在DEBUG模式下  
> * -Xrunjdwp是通知JVM使用(java debug wire protocol)来运行调试环境
> * transport指定了调试数据的传送方式，dt_socket是指用SOCKET模式，另有dt_shmem指用共享内存方式，其中，dt_shmem只适用于Windows平台  
> * suspend指明，是否在调试客户端建立起来后，再执行JVM  
> * adress 表示远程调试提供访问的地址和端口，0.0.0.0代表本机所有ip，避免绑定到localhost，不能远程访问到
3. 在ide(eclipse,idea)中打开debug configurations，选择remote application，添加要调试的项目，选择attach模式，输入ip和调试端口，即命令中adress设置的内容，添加断点，debug操作与本地一样。
> 注意：
> 1. attach模式下，远程应用必须先启动，否则会报connection refused异常，无法调试
> 2. 要确定远程调试的端口能被访问到，阿里云必须在安全组中配置才能访问。
