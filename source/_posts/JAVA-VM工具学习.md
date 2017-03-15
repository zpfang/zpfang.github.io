---
title: JAVA VM工具学习
date: 2017-03-15 17:17:54
tags:
+ description: "在学习和工作中使用的有关JAVA的工具记录"
---
<Contents>
## JPS
**显示当前所有Java进程pid的命令**  
**相关命令**  
1. jps -q  
只显示pid，不显示class名称,jar文件名和传递给main 方法的参数
1. jps -l  
输出应用程序main class的完整package名 或者 应用程序的jar文件完整路径名
2. jps -v
 输出传递给JVM的参数


## Btrace  
**远程调试代码，无需重启，性能影响小**

    btrace [-p <port>] [-cp <classpath>] <pid> <btrace-script>  

port指定BTrace agent的服务端监听端口号，用来监听clients，默认为2020，可选。  
classpath用来指定类加载路径，比如你的BTrace代码里用到了netty等第三方jar包。  
pid表示进程号，可通过jps命令获取。  
btrace-script即为BTrace脚本。
