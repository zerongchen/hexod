---
title: spring-tomcat-apr
date: 2021-03-08 17:39:21
tags:
---

# spring-tomcat-apr

* 要求实现安装 JDK 1.8 ; gcc 版本 >4.5，该安装暂时忽略ssl协议

以下安装包目前存在OSS oss://kanban-bj/tool/apr/ 

### 1,apr-1.7.0 



进入该目录 执行 ./configure --prefix=${apr_path} & make & make install
```shell
wget http://mirrors.hust.edu.cn/apache//apr/apr-1.7.0.tar.gz
tar xvf apr-1.7.0.tar.gz 
./configure --prefix=${apr_path} & make & make install
```
其中路径  ${apr_path} 是APR安装目录，JNI执行文件模目录所在地，默认 /usr/local/apr

### 2,apr-util-1.6.1
进入该目录 执行 ./configure --with-apr=${apr_path} & make & mak install
```shell
wget https://mirrors.tuna.tsinghua.edu.cn/apache//apr/apr-util-1.6.1.tar.gz
tar xvf apr-util-1.6.1.tar.gz
./configure --prefix=${apr_path} & make & make install
```
如果执行过程中报 xml/apr_xml.c expat.h 找不到的情况
* 能上网的情况下执行 yum install expat-devel 后执行 ./configure --with-apr=${apr_path} & make & mak install
* 不能上网的情况下解压包 expac-2.2.10.tar.bz2 
 ``` shell
 wget https://github.com/libexpat/libexpat/releases/download/R_2_2_10/expat-2.2.10.tar.bz2
 
 tar xvf expact-2.2.10.tar.bz2 -C ./expat-dir    
 cd ./expat-dir     
 ./cofigure --prefix=${expat_path} & make & make install
 # ${expact_path} expat 的安装路径     
 ./configure --with-apr=${apr-pat} --with-expat={expat_path} & make & make install

 ``` 
 ### 3,apr-iconv-1.2.2

 ```shell 
wget https://mirrors.tuna.tsinghua.edu.cn/apache//apr/apr-iconv-1.2.2.tar.gz
tar xvf apr-iconv-1.2.2.tar.gz
./configure --with-apr={apr_path} & make & make install


 ```

 ### 4,tomcat-native
 根据自身tomcat 于 http://tomcat.apache.org/native-doc/ 下载
 ```shell

 cd native
./configure --with-apr={apr_path} --with-java-home=${JAVA_HOME}  --without-ssl & make & make install 
# 因为没有装openssl 所以这里直接加上 without-ssl 即可
 ```

### 5,服务启动加上参数

修改配置项 spring.tomcat.protocol=org.apache.coyote.http11.Http11AprProtocol(如果是appolo启动可从appolo 全局变量修改)


-Djava.library.path=/usr/local/apr/lib
```shell 

nohup java -Xmx5G -Xms5G -Djava.library.path=/usr/local/apr/lib -jar view.jar --spring.config.location=shared.properties,view.properties > /dev/null 2>&1 &

```
启动成功的话会打印如下log
```log
cn.****.valuekanban.TomcatConfig      : protocol:org.apache.coyote.http11.Http11AprProtocol@421bba99

2021-03-08 17:16:12.685+0800 INFO [service-view,,,] 12998 --- [apr-8882-exec-2] c.a.valuekanban.view.core.ViewDelegate   : 过滤视图[0ac34610c3d343f288e022087e20b13e-部门事务按来源状态分布]
2021-03-08 17:16:12.685+0800 INFO [service-view,,,] 12998 --- [apr-8882-exec-1] c.a.valuekanban.view.core.ViewDelegate   : 过滤视图[92a7ad88155443f184a563dccb91790e-事务(重点项目)状态分布]
2021-03-08 17:16:12.724+0800 INFO [service-view,,,] 12998 --- [apr-8882-exec-1] c.a.v.view.core.support.StatService      : batchVu size: 4

```

### 6,mark

目前已经已经生成了一份 8C/16G 的执行文件，oss://kanban-bj/tool/apr/8c/tomcat-apr-bin.tar.gz
```shell
ossutil cp oss://kanban-bj/tool/apr/8c/tomcat-apr-bin.tar.gz ./
tar xvf tomcat-apr-bin.tar.gz -C /usr/local/

```
目前解压目标文件是在 /usr/local ，其他目前文件测试好像是不生效，另外这些执行文件是基于8C的linux操作系统下生成，如果遇到机器是非8C最好重新安装（内存是64位的就不影响）--暂时无测试8c环境生产的文件在4c下是否可以执行，欢迎指正

执行上述命令之后进行 步骤 5
