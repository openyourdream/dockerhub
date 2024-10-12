### docker安装oracle_11g文档

#### 1.安装前准备工作

提前在机器安装好docker和docker-compose

#### 2.拉取安装镜像文件

```shell
docker pull registry.cn-hangzhou.aliyuncs.com/helowin/oracle_11g
```

#### 3.编写docker-compose.yml文件并启动oracle_11g容器

docker-compose.yml文件内容如下：

```yaml
version: '2.4'
services:
  oracle:
    image: 'registry.cn-hangzhou.aliyuncs.com/helowin/oracle_11g:latest'
    restart: always
    privileged: true
    stdin_open: true
    tty: true
    environment:
      - 'TZ=Asia/Shanghai'
    volumes:
      - '/etc/localtime:/etc/localtime:ro'
    ports:
      - '1521:1521'
    networks:
      - deploy-net
    container_name: oracle_11g
networks:
  deploy-net: null
```

启动容器命令：

```shell
docker-compose up -d oracle
```

#### 4.进入容器内修改配置

进入容器内命令：

```shell
docker exec -it oracle_11g sh
```

查看配置 文件tnsnames.ora命令：

```shell
#查找配置文件位置
find / -name tnsnames.ora
```

切换root用户，密码helowin。修改/etc/profile文件，在末尾添加如下内容：

```shell
#oracle_11g env
export ORACLE_HOME=/home/oracle/app/oracle/product/11.2.0/dbhome_2
export ORACLE_SID=helowin
export PATH=$ORACLE_HOME/bin:$PATH
```

修改完之后刷新使文件生效命令（每次重新进入容器都需要执行一下此命令）：

```shell
source /etc/profile
```

创建软连接：

```shell
ln -s $ORACLE_HOME/bin/sqlplus /usr/bin
```

切换oracle用户，修改sys、system用户密码，创建用户：

```shell
//登录
sqlplus /nolog
//连接
conn /as sysdba
//修改system密码为system
alter user system identified by system;
//修改sys密码为sys
alter user sys identified by sys;
//创建用户
create user dev identified by dev;
//给用户赋予权限
grant connect,resource,dba to dev;
//修改密码规则策略为密码永不过期
ALTER PROFILE DEFAULT LIMIT PASSWORD_LIFE_TIME UNLIMITED;
//修改数据库最大连接数
alter system set processes=1000 scope=spfile;
//关闭数据库
shutdown immediate;
//启动数据库
startup;
```

执行效果如下所示：

```shell
[oracle@685fa11ea237 /]$ sqlplus /nolog
SQL*Plus: Release 11.2.0.1.0 Production on Fri Jul 29 16:43:58 2022
Copyright (c) 1982, 2009, Oracle.  All rights reserved.
SQL> conn /as sysdba
Connected.
SQL> alter user system identified by system;
User altered.
SQL> alter user sys identified by sys;
User altered.
SQL> create user dev identified by dev;
User created.
SQL> grant connect,resource,dba to dev;
Grant succeeded.
SQL> ALTER PROFILE DEFAULT LIMIT PASSWORD_LIFE_TIME UNLIMITED;
Profile altered.
SQL> alter system set processes=1000 scope=spfile;
System altered.
SQL> shutdown immediate;
Database closed.
Database dismounted.
ORACLE instance shut down.
SQL> startup;
ORACLE instance started.
Total System Global Area 1603411968 bytes
Fixed Size                  2213776 bytes
Variable Size             402655344 bytes
Database Buffers         1191182336 bytes
Redo Buffers                7360512 bytes
Database mounted.
Database opened.
SQL> SQL> 
SQL> 
```

#### 5.退出容器连接oracle

命令行输入四次exit可退出容器回到宿主机器。

宿主机防火墙开通1521端口，使用工具即可连接oracle服务。