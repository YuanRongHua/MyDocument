# Tomcat #
Tomcat是Apache 软件基金会（Apache Software Foundation）的Jakarta 项目中的一个核心项目，由Apache、Sun 和其他一些公司及个人共同开发而成。由于有了Sun 的参与和支持，最新的Servlet 和JSP 规范总是能在Tomcat 中得到体现，Tomcat 5支持最新的Servlet 2.4 和JSP 2.0 规范。因为Tomcat 技术先进、性能稳定，而且免费，因而深受Java 爱好者的喜爱并得到了部分软件开发商的认可，成为目前比较流行的Web 应用服务器。  
  
Tomcat 服务器是一个免费的开放源代码的Web 应用服务器，属于轻量级应用服务器，在中小型系统和并发访问用户不是很多的场合下被普遍使用，是开发和调试JSP 程序的首选。对于一个初学者来说，可以这样认为，当在一台机器上配置好Apache 服务器，可利用它响应HTML（标准通用标记语言下的一个应用）页面的访问请求。实际上Tomcat 部分是Apache 服务器的扩展，但它是独立运行的，所以当你运行tomcat 时，它实际上作为一个与Apache 独立的进程单独运行的。
  
诀窍是，当配置正确时，Apache 为HTML页面服务，而Tomcat 实际上运行JSP 页面和Servlet。另外，Tomcat和IIS等Web服务器一样，具有处理HTML页面的功能，另外它还是一个Servlet和JSP容器，独立的Servlet容器是Tomcat的默认模式。不过，Tomcat处理静态HTML的能力不如Apache服务器。

## tomcat https配置 ##

1.为服务器生成证书   
jdk生成证书
 
打开cmd输入  
	keytool -genkey -v -alias cas -keyalg RSA -keystore D:\home\cas.keystore -validity 36500 

  	参数简要说明
	D:\home\cas.keystore	证书文件的保存路径	  
	cas.keystore	证书文件名称  
	-validity 36500	证书有效期，36500表示100年，默认值是90天
	cas	自定义证书名称
	
	注意：2次输入密码必须一致
2.修改tomcat server.xml


	<Connector port="8443" protocol="org.apache.coyote.http11.Http11NioProtocol"
    maxThreads="150" SSLEnabled="true" scheme="https" secure="true"
    clientAuth="false" sslProtocol="TLS"   keystoreFile="C:\Users\wb-yuanronghua\cas.keystore" keystorePass="casserver" />
	
	修改说明：
	SSLEnabled="true"  启用SSL
	keystoreFile	服务器证书存放路径
	keystorePass	证书口令密码

## tomcat+Redis 实现session共享 ##

解决集群session共享问题,扩展tomcat
jar下载:已上传Git@OCS tomcat redis session.7z
https://github.com/jcoleman/tomcat-redis-session-manager

	apache-tomcat-7.0.64    
	tomcat-redis-session-manager-1.2-tomcat-7.jar    
	jedis-2.1.0.jar    
	commons-pool-1.6.jar

将所需jar包加入apache-tomcat-7.0.64\lib目录下，修改apache-tomcat-7.0.64\conf\context.xml 新增如下配置：

	<Valve className="com.radiadesign.catalina.session.RedisSessionHandlerValve" />
	<Manager className="com.radiadesign.catalina.session.RedisSessionManager" host="100.69.199.135" port="6379" database="0" maxInactiveInterval="60" />

重启apache-tomcat-7.0.64
对比访问集群的JSESSIONID 发现一致

## Tomcat集群部署 ##

1.修改配置文件 /conf/server.xml
   
取消以下注释  

	<!-- <Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster" channelSendOptions="8" /> -->

2.编辑应用代码内的web.xml配置文件，新增<distributed/>标签说明应用为分布式部署



    Cluster标签默认配置如下  
	<!-- 
	    Cluster(集群,族) 节点,如果你要配置tomcat集群,则需要使用此节点.
	    className 表示tomcat集群时,之间相互传递信息使用那个类来实现信息之间的传递.
	    channelSendOptions可以设置为2、4、8、10，每个数字代表一种方式
	    2 = Channel.SEND_OPTIONS_USE_ACK(确认发送)
	    4 = Channel.SEND_OPTIONS_SYNCHRONIZED_ACK(同步发送) 
	    8 = Channel.SEND_OPTIONS_ASYNCHRONOUS(异步发送)
	    在异步模式下，可以通过加上确认发送(Acknowledge)来提高可靠性，此时channelSendOptions设为10
	-->
	<Cluster className="org.apache.catalina.ha.tcp.SimpleTcpCluster" channelSendOptions="8">
    <!--
        Manager决定如何管理集群的Session信息。Tomcat提供了两种Manager：BackupManager和DeltaManager
        BackupManager－集群下的所有Session，将放到一个备份节点。集群下的所有节点都可以访问此备份节点
        DeltaManager－集群下某一节点生成、改动的Session，将复制到其他节点。
        DeltaManager是Tomcat默认的集群Manager，能满足一般的开发需求
        使用DeltaManager，每个节点部署的应用要一样；使用BackupManager，每个节点部署的应用可以不一样.


        className－指定实现org.apache.catalina.ha.ClusterManager接口的类,信息之间的管理.
        expireSessionsOnShutdown－设置为true时，一个节点关闭，将导致集群下的所有Session失效
        notifyListenersOnReplication－集群下节点间的Session复制、删除操作，是否通知session listeners
        maxInactiveInterval－集群下Session的有效时间(单位:s)。
        maxInactiveInterval内未活动的Session，将被Tomcat回收。默认值为1800(30min)
    -->
    <Manager className="org.apache.catalina.ha.session.DeltaManager"
             expireSessionsOnShutdown="false"
             notifyListenersOnReplication="true"/>

    <!--
        Channel是Tomcat节点之间进行通讯的工具。
        Channel包括5个组件：Membership、Receiver、Sender、Transport、Interceptor
    -->
    <Channel className="org.apache.catalina.tribes.group.GroupChannel">
         <!--
            Membership维护集群的可用节点列表。它可以检查到新增的节点，也可以检查到没有心跳的节点
            className－指定Membership使用的类
            address－组播地址
            port－组播端口
            frequency－发送心跳(向组播地址发送UDP数据包)的时间间隔(单位:ms)。默认值为500
            dropTime－Membership在dropTime(单位:ms)内未收到某一节点的心跳，则将该节点从可用节点列表删除。默认值为3000

            注: 组播（Multicast）：一个发送者和多个接收者之间实现一对多的网络连接。
                一个发送者同时给多个接收者传输相同的数据，只需复制一份相同的数据包。
                它提高了数据传送效率，减少了骨干网络出现拥塞的可能性
                相同组播地址、端口的Tomcat节点，可以组成集群下的子集群
         -->
        <Membership className="org.apache.catalina.tribes.membership.McastService"
                    address="228.0.0.4"
                    port="45564"
                    frequency="500"
                    dropTime="3000"/>

        <!--
            Receiver : 接收器，负责接收消息
            接收器分为两种：BioReceiver(阻塞式)、NioReceiver(非阻塞式)

            className－指定Receiver使用的类
            address－接收消息的地址
            port－接收消息的端口
            autoBind－端口的变化区间
            如果port为4000，autoBind为100，接收器将在4000-4099间取一个端口，进行监听
            selectorTimeout－NioReceiver内轮询的超时时间
            maxThreads－线程池的最大线程数
        -->
        <Receiver className="org.apache.catalina.tribes.transport.nio.NioReceiver"
                  address="auto"
                  port="4000"
                  autoBind="100"
                  selectorTimeout="5000"
                  maxThreads="6"/>

        <!--
            Sender : 发送器，负责发送消息
            Sender内嵌了Transport组件，Transport真正负责发送消息
        -->
        <Sender className="org.apache.catalina.tribes.transport.ReplicationTransmitter">
            <!--
                Transport分为两种：bio.PooledMultiSender(阻塞式)、nio.PooledParallelSender(非阻塞式) 
            -->
            <Transport className="org.apache.catalina.tribes.transport.nio.PooledParallelSender"/>
        </Sender>

        <!--
            Interceptor : Cluster的拦截器
            TcpFailureDetector－网络、系统比较繁忙时，Membership可能无法及时更新可用节点列表，
            此时TcpFailureDetector可以拦截到某个节点关闭的信息，
            并尝试通过TCP连接到此节点，以确保此节点真正关闭，从而更新集群可以用节点列表                 
        -->
        <Interceptor className="org.apache.catalina.tribes.group.interceptors.TcpFailureDetector"/>

        <!--
            MessageDispatch15Interceptor－查看Cluster组件发送消息的方式是否设置为
            Channel.SEND_OPTIONS_ASYNCHRONOUS(Cluster标签下的channelSendOptions为8时)。
            设置为Channel.SEND_OPTIONS_ASYNCHRONOUS时，
            MessageDispatch15Interceptor先将等待发送的消息进行排队，然后将排好队的消息转给Sender
        -->
        <Interceptor className="org.apache.catalina.tribes.group.interceptors.MessageDispatch15Interceptor"/>
    </Channel>

    <!--
        Valve : 可以理解为Tomcat的拦截器
        ReplicationValve－在处理请求前后打日志；过滤不涉及Session变化的请求                   
        vmRouteBinderValve－Apache的mod_jk发生错误时，保证同一客户端的请求发送到集群的同一个节点
    -->
    <Valve className="org.apache.catalina.ha.tcp.ReplicationValve" filter=""/>
    <Valve className="org.apache.catalina.ha.session.JvmRouteBinderValve"/>

    <!--
        Deployer : 同步集群下所有节点的一致性。Deployer没试验成功过。。。
     -->
     <Deployer className="org.apache.catalina.ha.deploy.FarmWarDeployer"
                   tempDir="/tmp/war-temp/"
                   deployDir="/tmp/war-deploy/"
                   watchDir="/tmp/war-listen/"
                   watchEnabled="false"/>
    <!--
        ClusterListener : 监听器，监听Cluster组件接收的消息
        使用DeltaManager时，Cluster接收的信息通过ClusterSessionListener传递给DeltaManager
    -->
    <ClusterListener className="org.apache.catalina.ha.session.ClusterSessionListener"/>
	</Cluster>
