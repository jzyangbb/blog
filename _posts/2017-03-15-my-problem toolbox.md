---
layout: post
title:  "我的问题排查工具箱"
date:   2017-03-15 12:00:05 +0800
categories: 开发
tags: 问题排查 Linux命令 java三板斧
author: YYQ
---

* content
{:toc} 

### 前言
平时的工作中经常碰到很多疑难问题的处理，在解决问题的同时，有一些工具起到了相当大的作用，在此书写下来，一是作为笔记，可以让自己后续忘记了可快速翻阅，二是分享，希望看到此文的同学们可以拿出自己日常觉得帮助很大的工具，大家一起进步。

闲话不多说，开搞。

### Linux命令类
#### tail
最常用的tail -f


``` bash
tail -300f shopbase.log #倒数300行并进入实时监听文件写入模式
```
#### grep
``` bash
grep forest f.txt   #文件查找
grep forest f.txt cpf.txt #多文件查找
grep 'log' /home/admin -r -n #目录下查找所有符合关键字的文件
cat f.txt | grep -i shopbase    
grep 'shopbase' /home/admin -r -n --include *.{vm,java} #指定文件后缀
grep 'shopbase' /home/admin -r -n --exclude *.{vm,java} #反匹配
seq 10 | grep 5 -A 3    #上匹配
seq 10 | grep 5 -B 3    #下匹配
seq 10 | grep 5 -C 3    #上下匹配，平时用这个就妥了
cat f.txt | grep -c 'SHOPBASE'
```

#### find
``` bash
sudo -u admin find /home/admin /tmp /usr -name \*.log(多个目录去找)
find . -iname \*.txt(大小写都匹配)
find . -type d(当前目录下的所有子目录)
find /usr -type l(当前目录下所有的符号链接)
find /usr -type l -name "z*" -ls(符号链接的详细信息 eg:inode,目录)
find /home/admin -size +250000k(超过250000k的文件，当然+改成-就是小于了)
find /home/admin f -perm 777 -exec ls -l {} \; (按照权限查询文件)
find /home/admin -atime -1  1天内访问过的文件
find /home/admin -ctime -1  1天内状态改变过的文件 
find /home/admin -mtime -1  1天内修改过的文件
find /home/admin -amin -1  1分钟内访问过的文件
find /home/admin -cmin -1  1分钟内状态改变过的文件   
find /home/admin -mmin -1  1分钟内修改过的文件
```
#### top
top除了看一些基本信息之外，剩下的就是配合来查询vm的各种问题了
``` bash
ps -ef | grep java
top -H -p pid
```
获得线程10进制转16进制后jstack去抓看这个线程到底在干啥

### java三板斧，噢不对，是七把
#### jps
我只用一条命令：
``` bash
sudo -u admin /opt/taobao/java/bin/jps -mlvV
```
![image](http://ata2-img.cn-hangzhou.img-pub.aliyun-inc.com/7b156cb53250bf5f91811900c2575a91.png) 

#### jps
我只用一条命令：
``` bash
sudo -u admin /opt/taobao/java/bin/jps -mlvV
```
![image](http://ata2-img.cn-hangzhou.img-pub.aliyun-inc.com/7b156cb53250bf5f91811900c2575a91.png) 


#### jstack
普通用法:
``` bash
sudo -u admin /opt/taobao/install/ajdk-8_1_1_fp1-b52/bin/jstack 2815
```
![image](http://ata2-img.cn-hangzhou.img-pub.aliyun-inc.com/bc501bf424b3d2884e6ec118d14554a3.png) 

#### jinfo
可看系统启动的参数，如下
``` bash
sudo -u admin /opt/taobao/install/ajdk-8_1_1_fp1-b52/bin/jinfo -flags 2815
```
![image](http://ata2-img.cn-hangzhou.img-pub.aliyun-inc.com/bc879145e2994e9a09327407ed88382c.png) 

#### jmap
两个用途

1.查看堆的情况：
``` bash
sudo -u admin /opt/taobao/install/ajdk-8_1_1_fp1-b52/bin/jmap -heap 2815
```
![image](http://ata2-img.cn-hangzhou.img-pub.aliyun-inc.com/00d9eaef08cdb51263d202d26795036b.png) 

2.dump
``` bash
sudo -u admin /opt/taobao/install/ajdk-8_1_1_fp1-b52/bin/jmap -dump:live,format=b,file=/tmp/heap2.bin 2815
```

---
