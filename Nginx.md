# Nginx #

 **Nginx** ("engine x") 是一个高性能的HTTP和 反向代理 服务器，也是一个 IMAP/POP3/SMTP 服务器。 Nginx 是由 Igor Sysoev 为俄罗斯访问量第二的 Rambler.ru 站点开发的，第一个公开版本0.1.0发布于2004年10月4日。其将源代码以类BSD许可证的形式发布，因它的稳定性、丰富的功能集、示例配置文件和低系统资源的消耗而闻名。2011年6月1日，nginx 1.0.4发布。

**Nginx**作为**负载均衡**服务器：Nginx 既可以在内部直接支持 Rails 和 PHP 程序对外进行服务，也可以支持作为 HTTP代理服务器对外进行服务。Nginx采用C进行编写，不论是系统资源开销还是CPU使用效率都比 Perlbal 要好很多。


## Windows环境 负载均衡##
1.Ngnix下载  

http://nginx.org/en/docs/windows.html

2.解压启动

cd c:\  
unzip nginx-1.9.9.zip  
cd nginx-1.9.9  
start nginx

    nginx -s stop 	fast shutdown
    nginx -s quit 	graceful shutdown
    nginx -s reload 	changing configuration, starting new worker processes with a new configuration, graceful shutdown of old worker processes
    nginx -s reopen 	re-opening log files

3.查看logs

nginx-1.9.9\logs\access.log  
nginx-1.9.9\logs\error.log  
nginx-1.9.9\logs\nginx.pid

4.负载均衡配置

edit nginx-1.9.9\conf\nginx.conf
	
	http {
	    upstream myapp1 {
	        server srv1.example.com;
	        server srv2.example.com;
	        server srv3.example.com;
	    }

	    server {
	        listen 8080;
			server_name localhost;
	
	        location / {
	            proxy_pass http://myapp1;
	        }
	    }
	}

5.open http://localhost:8080

可以看到页面随机分配到配置服务器ser1、serv2、serv3



