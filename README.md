
# go-chichat

# ChitChat

* 此项目用`config.json` 文件进行配置
* 程序运行出错结果记录到日志文件 `chitchat.log` 
* 神奇日期格式化,记住一个特殊的日子
> time.Format("Jan 2, 2006 at 3:04pm")
> time.Format("2006-01-02 15:04:05")

---

# Postgresql 的前期准备

## CentOS 7 安装、配置、使用 PostgreSQL 10 安装及基础配置
* 进入官方下载：[https://www.postgresql.org/download/linux/redhat/](https://www.postgresql.org/download/linux/redhat/) 
* 操作：
   1. Select version: 
    >10
   2. Select platform: 
   >CentOS 7
   3. Select architecture: 
   >x86_64
   4. Install the repository RPM:
   ```
   yum install https://download.postgresql.org/pub/repos/yum/10/redhat/rhel-7-x86_64/pgdg-centos10-10-2.noarch.rpm
   ```
   5. Install the client packages:
   ```
   yum install postgresql10
   ```
   6. Optionally install the server packages:
   ```
   yum install postgresql10-server
   ```
   7. Optionally initialize the database and enable automatic start:
   ```
    /usr/pgsql-10/bin/postgresql-10-setup initdb
	systemctl enable postgresql-10
	systemctl start postgresql-10
    ```
   8. 启动服务：
    ```
     service postgresql initdb
    chkconfig postgresql on
    ```
    > [参考](https://www.postgresql.org/docs/10/static/tutorial-createdb.html)

   9. 开放防火墙端口
   ```
    firewall-cmd --permanent --add-port=5432/tcp  
    firewall-cmd --permanent --add-port=80/tcp  
    firewall-cmd --reload 
   ``` 

   10. 访问PostgreSQL
   ```
    su - postgres 
    #出现提示符
    -bash-4.2$
    #输入命令psql将看到PostgrSQL的版本信息。
    psql (10.4)
    输入 "help" 来获取帮助信息. 
    postgres帐号密码 都为postgres
    #重启PostgreSQL数据服务
    systemctl restart postgresql-10.service
   ```

> https://blog.csdn.net/mbshqqb/article/details/78622167?locationNum=8&fps=1
> pgAdmin 连接错误一般是由于/var/lib/pgsql/10/data/pg_hba.conf的配置引起的。
> https://www.cnblogs.com/stulzq/p/7766409.html

   11. 开启远程访问
    ```
     vim /var/lib/pgsql/10/data/postgresql.conf
    修改#listen_addresses = 'localhost' 
    为  listen_addresses='*'
    当然，此处‘*’也可以改为任何你想开放的服务器IP

信任远程连接
no pg_hba.conf entry for host "192.168.33.1" ,user "postgres", database "postgrs", SSL off
编辑pg_hba.conf配置文件：
在里面添加：host    all             all             0.0.0.0/0               trust

重启服务器：systemctl restart postgresql-10.service
    vi m/var/lib/pgsql/10/data/pg_hba.conf
    修改如下内容，信任指定服务器连接
    # IPv4 local connections:
    host    all            all      127.0.0.1/32      trust
    host    all            all      192.168.157.1/32（需要连接的服务器IP）  trust
备份
pg_dump -h 192.168.3.10 -U postgres issuetracker >  /www/web/node_pro/koatopro/issuetracker.bak
```

恢复psql -h ip -U postgres -d issuetracker < issuetracker.bak

错误：
pq: Ident authentication failed for user "root" Cannot create user
解决：
vim /var/lib/pgsql/11/data/pg_hba.conf
将：
# IPv6 local connections:
host    all             all             ::1/128                 Ident
                                                                  
修改为：
# IPv6 local connections:
host    all             all             ::1/128                 trust

错误：
pq: role "root" does not exist
解决：
su postgres
# 创建root用户
postgres=#create user root with password 'password';    
CREATE ROLE

# 将数据库权限赋予root用户
postgres=# GRANT ALL PRIVILEGES ON DATABASE mydatabase to root;
GRANT

# 将用户修改为超级用户（看实际需求）
postgres=# ALTER ROLE root WITH SUPERUSER;

postgres=# \q

postgresql-10.1-3-windows-x64 安装之后,起动pgAdmin 4问题(win10)

https://www.cnblogs.com/geovindu/p/8108962.html
运行pgAdmin出现”pgAdmin 4  the application server could not be contant“ 窗口。

参考：https://stackoverflow.com/questions/40083391/postgresql-cant-connect-application-server-through-pgadmin4

解决方式：

1. c:\Users\your_name\AppData\Roaming\pgAdmin 之内的删除所有文件和文件夹

2.C:\Program Files\PostgreSQL\10\pgAdmin 4\web 找到config_distro.py文件

添加：

MINIFY_HTML=False
DATA_DIR = "C:/Data/pgAdmin" # set non-ascii path here

则为：

SERVER_MODE = False
HELP_PATH = '../../../docs/en_US/html/'
MINIFY_HTML=False
DATA_DIR = "C:/Data/pgAdmin" # set non-ascii path here
DEFAULT_BINARY_PATHS = {
"pg":   "C:\\Program Files\\PostgreSQL\\10\\bin",
"ppas": ""
}

