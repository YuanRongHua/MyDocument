# Eclipse Luna 4.4.2 #

----------

## java code  ##
	set Tab
    open window->Prefences->General->Editors->Text Editors
	
![](http://i.imgur.com/pQLWS2W.png)


# Spring boot 热部署 #


[https://github.com/spring-projects/spring-loaded/tree/1.2.1.RELEASE/springloaded/src/main/java](https://github.com/spring-projects/spring-loaded/tree/1.2.1.RELEASE/springloaded/src/main/java "https://github.com/spring-projects/spring-loaded/tree/1.2.1.RELEASE/springloaded/src/main/java")


	springloaded-1.2.0.RELEASE.jar  资源已上传至附件	
![http://i.imgur.com/KAR14cL.png](http://i.imgur.com/KAR14cL.png)

**VM arguments** 新增如下：

		-javaagent:D:\springloaded-1.2.0.RELEASE.jar -noverify

最后重新启动即可

# Maven 依赖包冲突 #

**Eclipse 解决**

open pom.xml
select Dependency Hierarchy

![http://i.imgur.com/WIeFOyL.png](http://i.imgur.com/WIeFOyL.png)

# BatchShell #

A SSH2 based tool which can transfer files to multiple remote servers and execute commands simultaneously on multiple remote servers

http://www.batchshell.com/