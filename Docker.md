# Docker #

基于Centos 7实践

#### 安装启动 ####
.....................

手动启动docker服务  
`sudo service docker start`

设置开机启动  
`sudo service docker enable`


#### 容器操作 ####
----------
1. 创建容器  
`docker create`  创建容器处于停止状态  
`docker run`   创建容器后启动容器  

2. 查看容器  
 `docker ps` 正在运行的容器  
`docker ps -a` 所有容器  
`docker ps -l` 最后创建的容器  
`docker ps -n=2` 最后创建的n个容器  

3. 启动容器  
`docker start ID/NAMES`  

4. 终止容器  
`sudo docker stop ID/NAMES`   
`sudo docker kill ID/NAMES` 强行终止  

5. 删除容器    
`docker rm ID/NAMES` 不可以删除运行中状态的容器  

6. 依附容器  
`docker attach ID/NAMES`
  
7. 查看容器日志    
`docker logs -f --tail=5 ID/NAMES`

8. 查看容器进程
`docker top ID/NAMES`
  
9. 查看容器信息
 `docker inspect ID/NAMES`
 `docker inspect --format='{{.NetworkSettings.IPAddress}}' ID/NAMES`

10. 容器内执行命令
 `docker exec -d ID/NAMES /etc/new_config_file` 后台型任务
 `docker exec -t -i ID/NAMES` 交互性任务

11. 容器的导入和导出
 `docker export inspect_import > my_container.tar`
 `cat my_container.tar | docker import -imported:container`
 `docker import url res:tag`
12. 
 
8. 






