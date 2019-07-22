---
title: pmm监控平台的安装与使用
date: 2019-07-22 18:02:07
tags: "监控"
categories: "监控"
---


PMM的使用操作手册

### 安装docker，创建data数据，启动pmm-server容器
```
docker pull percona/pmm-server:latest
```

```
docker create \
   -v /opt/prometheus/data \
   -v /opt/consul-data \
   -v /var/lib/mysql \
   -v /var/lib/grafana \
   --name pmm-data \
   percona/pmm-server:latest /bin/true
```

```
docker run -d \
   -p 8081:80 \
   --volumes-from pmm-data \
   --name pmm-server \
   --restart always \
   percona/pmm-server:latest
```
   

### 从官方下载rpm包进行client安装

https://www.percona.com/downloads/pmm/   选择centos7  最新版本的pmm版本
```
rpm -ivh pmm-client-1.17.1-1.el7.x86_64.rpm
```

```
[root@Prometheus0002 tmp]# pmm-admin config --server 172.16.0.193
OK, PMM server is alive.

PMM Server      | 172.16.0.193 
Client Name     | Prometheus0002
Client Address  | 172.16.0.15 
```

```
pmm-admin add linux:metrics 
```

```
pmm-admin add mysql
```


### 配置告警








### 邮箱报警添加
进入docker
```
docker exec -it pmm-server /bin/bash
```

编缉grafana.ini
vim /etc/grafana/grafana.ini
```
[smtp]
enabled = true
host = smtp.qq.com:25
user = huyanghf@qq.com
password = hufangyun260
from_address = liuqian@healthmall.cn
from_name = Grafana
```
 
重启pmm-server容器
```
docker restart pmm-server
```

### 添加 redis_exporter和 nginx监控指标
由于PMM监控平台没有集成redis和nginx，需要在prometheus.yml文件scrape_configs下添加项目:
```
scrape_configs:

- job_name: redis_exporter
  static_configs:
  - targets: ['localhost:9121']
```
  
  
### 设置grafana登录用户7.1、进入容器





#### 修改grafana.ini，禁止匿名登录
```
[root@c74f5be8ed88 opt]# vi /etc/grafana/grafana.ini 
#################################### Anonymous Auth 
##########################
[auth.anonymous]
# enable anonymous access
#enabled = True
```

把enabled = Ture注释掉,这样既禁止匿名用户登陆了现在如果重启容器（systemctl restart docker）,再打开页面,你会发现自己进不去了

#### 修改登录账号admin的密码

登录数据库：sqlite3 /var/lib/grafana/grafana.db
修改user表,把admin密码改成admin：
```
update user set password='59acf18b94d7eb0694c61e60ce44c110c7a683ac6a8f09580d626f90f4a242000746579358d77dd9e570e83fa24faa88a8a6', salt ='F3FAxVm33R' where login ='admin'；
```
安全起见,也可以把admin密码改成TdPXP4sg：
```
update user set password='11cf3a1ee21b046b939b5f0cdc9d92ab70ba66e4e53f301fb2456ee7b6a665d8abf0d5b387ae0ec53f5f5fc8e477bfbe073e',salt='AHxOW2Fn34',name='admin',is_admin=1 where login='admin';
```

#### 开启用户注册
我们可以通过开启用户注册,自己创建用户,然后再查看user表的数据来自己定义密码(不要忘记salt列也要更新) 
```
####################################Users####################################
[users]
# disable user signup / registration
allow_sign_up =true
```
取消allow_sign_up =true注释


Prometheus，Grafana， Consul部署手册
将二进制包进行解压








