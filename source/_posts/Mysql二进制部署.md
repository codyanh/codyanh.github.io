---
title: Mysql二进制部署
date: 2019-07-22 18:02:07
tags: "运维"
categories: "运维"
---

### 下载mysql5.7二进制包
mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz

解压
```
tar xf mysql-5.7.24-linux-glibc2.12-x86_64.tar.gz -C /opt/mysql
mv mysql-5.7.24-linux-glibc2.12-x86_64 mysql
groupadd mysql
useradd -g mysql mysql
mkdir -p /opt/mysql/{data,log,run,etc}

echo "export PATH=$PATH:/opt/mysql/bin" >> /etc/profile
source /etc/profile

chown -R mysql:mysql /opt/mysql
chmod 750 /opt/mysql/{data,log,etc,run}
```

### 初始化
```
mysqld --initialize --user=mysql --basedir=/opt/mysql --datadir=/opt/mysql/data
```

此时会生成一个临时密码，可以在mysql_error.log文件找到
```
grep 'temporary password' /opt/mysql/log/mysql_error.log 
```

生成ssl，可以不执行此步骤
```
mysql_ssl_rsa_setup --basedir=/opt/mysql --datadir=/opt/mysql/data/
```

### systemd启动mysql服务

vim /lib/systemd/system/mysqld.service
```
[Unit]
Description=MySQL Server
Documentation=man:mysqld(8)
Documentation=http://dev.mysql.com/doc/refman/en/using-systemd.html
After=network.target
After=syslog.target

[Install]
WantedBy=multi-user.target

[Service]
User=mysql
Group=mysql
Type=forking
TimeoutSec=0
PermissionsStartOnly=true
ExecStart=/opt/mysql/bin/mysqld --defaults-file=/opt/mysql/etc/my.cnf
LimitNOFILE = 5000
Restart=on-failure
RestartPreventExitStartus=1
PrivateTmp=false
```


```
cat <<EOF >/opt/mysql/etc/my.cnf
[mysqld]
daemonize = on
user = mysql
port = 3306
basedir = /opt/mysql
datadir = /opt/mysql/data
socket = /opt/mysql/run//mysql.sock
bind-address = 0.0.0.0
pid-file = /opt/mysql/run/mysqld.pid
character-set-server = utf8
collation-server = utf8_general_ci
max_connections = 2408
log-error = /opt/mysql/log/mysqld.log
EOF
```

```
mysql -uroot -p
ALTER USER 'root'@'localhost' identified by 'nutsops';  
```
或者
```
set password=password("nutsops");
```

```
mysql> flush privileges;
mysql> exit;
```

```
SELECT User, Host, HEX(authentication_string) FROM mysql.user;
```
```
use mysql;
update user set host = '%' where user ='root';
```
或者
```
grant all privileges on *.*  to  'root'@'%'  identified by 'nutsops'  with grant option;
flush privileges;
```