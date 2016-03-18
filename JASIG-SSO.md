# CAS #


## Clustering CAS ##
文档引用-https://wiki.jasig.org/display/CASUM/Clustering+CAS

单点登录集群部署技术难点
1.Ticket 单机部署存于缓存中，分布式部署怎么存储
	
	Ehcache
	JDBC
	Memcache
2.Session 分布式部署怎么实现共享  

	tomcat集群实现session复制