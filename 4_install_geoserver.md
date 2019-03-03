## 1. 安装jdk或者jre

geoserver官方给出的jdk版本说明为：
- Java 11 - GeoServer 2.15.x and above (OpenJDK tested)
- Java 8 - GeoServer 2.9.x and above (OpenJDK and Oracle JRE tested)
- Java 7 - GeoServer 2.6.x to GeoServer 2.8.x (OpenJDK and Oracle JRE tested)
- Java 6 - GeoServer 2.3.x to GeoServer 2.5.x (Oracle JRE tested)
- Java 5 - GeoServer 2.2.x and earlier (Sun JRE tested)
For best performance we recommend the use *Oracle JRE 8* (also known as JRE 1.8).

```
yum list |grep java
sudo yum install java-1.8.0-openjdk-devel
```

## 2. 配置java环境变量

修改.bashrc文件，在最后加入

```
#set java environment
JAVA_HOME=/usr/lib/jvm/java-1.8.0-openjdk-1.8.0.191.b12-1.el7_6.x86_64
JRE_HOME=$JAVA_HOME/jre
CLASS_PATH=.:$JAVA_HOME/lib/dt.jar:$JAVA_HOME/lib/tools.jar:$JRE_HOME/lib
PATH=$PATH:$JAVA_HOME/BIN:$JRE_HOME/bin
export JAVA_HOME JRE_HOME CLASS_PATH PATH
```

使环境变量即时生效
source .bachrc

应用环境变量，并验证

```
# java -version
openjdk version "1.8.0_191"
OpenJDK Runtime Environment (build 1.8.0_191-b12)
OpenJDK 64-Bit Server VM (build 25.191-b12, mixed mode)
```

## 3. 下载与安装tomcat

官网：<https://tomcat.apache.org/download-80.cgi>
```
wget http://mirrors.tuna.tsinghua.edu.cn/apache/tomcat/tomcat-8/v8.5.38/bin/apache-tomcat-8.5.38.tar.gz
```
手动安装tomcat
```
sudo mkdir /usr/local/tomcat
sudo mv apache-tomcat-8.5.38.tar.gz /usr/local/tomcat/
cd /usr/local/tomcat/
sudo tar -xzvf apache-tomcat-8.5.38.tar.gz
```
修改所属用户
```
sudo chown -R cjt:cjt apache-tomcat-8.5.38
```
在 /etc/profile 中添加环境变量
```
# Set tomcat environment
CATALINA_HOME=/usr/local/tomcat/apache-tomcat-8.5.38
CATALINA_BASE=/usr/local/tomcat/apache-tomcat-8.5.38
PATH=$PATH:$CATALINA_BASE/bin
export PATH CATALINA_BASE
```
编写启动start_tomcat.sh与停止end_tomcat.sh脚本
```
#!/bin/sh
sh /usr/local/tomcat/apache-tomcat-8.5.38/bin/startup.sh
sh /usr/local/tomcat/apache-tomcat-8.5.38/bin/shutdown.sh
```
**Tomcat启动时卡在[localhost-startStop-1] org.apache.catalina.startup.HostConfig.deployDirectory问题的解决方法**
在catalina.sh或者启动参数中加入这么一行：-Djava.security.egd=file:/dev/./urandom
或者将$JAVA_HOME/jre/lib/security/java.security内的securerandom.source参数修改为file:/dev/./urandom


## 4. 下载geoserver并进行部署
```
wget http://sourceforge.net/projects/geoserver/files/GeoServer/2.14.2/geoserver-2.14.2-war.zip
mkdir geoserver && unzip geoserver-2.14.2-war.zip -d geoserver
mv geoserver.war /usr/local/tomcat/apache-tomcat-8.5.38/webapps/
```
重启tomcat，使用 http://服务器ip:8080/geoserver 应该就能打开GeoServer了
使用默认的用户名 admin 和密码 geoserver 登录，就能看到主界面了

## 5. 修改geoserver的默认数据存储目录

创建GeoServer的数据存储目录
```
mkdir /data/geodata
```
vim $CATALINA_BASE/webapps/geoserver/WEB-INF/web.xml，添加以下内容：
```
<web-app>
  ...
  <context-param>
    <param-name>GEOSERVER_DATA_DIR</param-name>
    <param-value>/home/geoserver_data</param-value>
  </context-param>
  ...
</web-app>
```

重启tomcat，查看GeoServer服务器状态，就会发现已经修改成功了。
