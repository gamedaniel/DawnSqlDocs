# 安装
## 1、环境要求

1. JDK：Oracle JDK8及以上，Open JDK8及以上，IBM JDK8及以上
2. OS：Linux（任何版本），Mac OS X（10.6及以上），Windows(XP及以上)，Windows Server（2008及以上），Oracle Solaris
3. 网络：没有限制（建议10G甚至更快的网络带宽）
4. 架构：x86，x64，SPARC，PowerPC

## 2、下载地址
### 2.1、Java 下载地址：https://www.java.com/en/download/
### 2.2、DawnSql 下载地址：https://share.weiyun.com/FVSkpqwx
### 2.3、Dbeaver 下载地址：https://dbeaver.io/download/
### 2.4、DBeaverWeb 下载地址：https://share.weiyun.com/KvjWgVmu
### 2.5、测试数据下载地址：https://share.weiyun.com/ROZEIHiI
**DBeaverWeb 是我们开源的一个 web 的 Sql 编辑器，它可以取代桌面的 Dbeaver ，同时也能够快速方便的扩展成其它的应用。**

## 3、安装并激活 DawnSql

下载: DawnSql(压缩包) 
  在命令行中转到安装文件夹的bin目录：
```shell
> cd {DawnSql}/bin/ 
```

Linux Mac 下启动：<br/>
```shell
> ./DawnSql.sh
```

Windows 下启动：<br/>
```shell
> ./DawnSql.bat
```

激活集群<br/>
Linux/Mac 下激活集群：<br/>
```shell
> ./control.sh --set-state ACTIVE
```

Windows 下激活集群：<br/>
```shell
> ./control.bat --set-state ACTIVE
```

## 4、启动 DBeaverWeb 客户端
```shell
> java -jar DBeaverWeb-1.0-SNAPSHOT.war
```

## 5、登录 DBeaverWeb
在浏览器中输入 http://localhost:8086/login
<br/>
<img src='/smart_sql_img/login_web.jpg'></img><br/>
<img src='/smart_sql_img/dawnclient.jpg'></img><br/>

**注意：在默认的配置下，DawnSql 需要端口 8091 开放，用于跟 DBeaverWeb 通讯。而 DBeaverWeb 默认的端口是 8086。**<br/>
**用户如果需要修改这些端口，或者扩展其方法，可以直接修改源代码**<br/>
**DBeaverWeb：https://gitee.com/wltz/DawnSqlPlus/tree/master/modules/DBeaverWeb**<br/>
**my-dawn-rpc-server：https://gitee.com/wltz/dawn-sql-db/tree/master/modules/my-dawn-rpc-server**<br/>

## 6、开源地址：
### 6.1、DawnSqlDb：https://gitee.com/wltz/dawn-sql-db
### 6.2、DawnSql：https://gitee.com/dawnsql/dawnsql
### 6.3、DawnSqlPlus：https://gitee.com/wltz/DawnSqlPlus


