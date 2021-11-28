[Tomcat 8权威指南](http://wiki.jikexueyuan.com/project/tomcat/)
[RUNNING.txt](http://tomcat.apache.org/tomcat-8.0-doc/RUNNING.txt)



### UNIX 指令路径

| UNIX 指令 | 注释    |
| ------- | ----- |
| /       | 根路径   |
| ./      | 当前路径  |
| ../     | 上一级路径 |



### Tomcat 目录结构及作用


| 目录             | 注释                                       |
| -------------- | ---------------------------------------- |
| $CATALINA_HOME | Tomcat 安装的根目录                            |
| /bin           | 存放用于启动及关闭的文件，以及其他一些脚本。其中，UNIX 系统专用的 `*.sh` 文件在功能上等同于 Windows 系统专用的 `*.bat` 文件。因为 Win32 的命令行缺乏某些功能，所以又额外地加入了一些文件。 |
| /conf          | 配置文件及相关的 DTD。其中最重要的文件是 server.xml，这是容器的主配置文件。 |
| /lib           | 支持tomcat软件运行的jar包。其中还有技术支持包，如servlet，jsp |
| /logs          | 日志文件的默认目录。                               |
| /webapps       | 存放 Web 应用的相关文件。                          |
| /work          | Web应用程序的临时工作目录。                          |
| /temp          | JVM用于临时文件的目录（java.io.tmpdir）             |





## Mac 系统下 Tomcat 安装及配置

### 一、下载

下载页：https://tomcat.apache.org/download-80.cgi

下载文件包后，解压拷贝到：`/Library`（Macintosh HD - 资源库） 目录下，重命名为 `Tomcat8524`

切换到 Tomcat 的 bin 目录下：`cd /Library/Tomcat8524/bin`

修改权限：`sudo chmod 755 ./*.sh`

启动 Tomcat：`./startup.sh`

控制台输出日志如下：

```
Using CATALINA_BASE:   /Library/Tomcat8524
Using CATALINA_HOME:   /Library/Tomcat8524
Using CATALINA_TMPDIR: /Library/Tomcat8524/temp
Using JRE_HOME:        /Library/Java/JavaVirtualMachines/jdk1.8.0_152.jdk/Contents/Home
Using CLASSPATH:       /Library/Tomcat8524/bin/bootstrap.jar:/Library/Tomcat8524/bin/tomcat-juli.jar
Tomcat started.
```

打开浏览器输入网址验证：`http://localhost:8080`

关闭 Tomcat：`./shutdown.sh`



### 参考

* [Mac环境下配置tomcat的步骤详解](http://www.jb51.net/article/94201.htm)
