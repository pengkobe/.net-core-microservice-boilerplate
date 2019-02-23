
# TARS 使用记录

## TODO

- 对服务器扩容进行实践
- 基于 IDC 分组案例
- 对应用配置、Set配置、服务配置和节点配置搭建案例
- 框架服务分机器、多机器搭建实践


## 安装

```bash
# 1.1
yum install glibc-devel
# 1.2

wget https://cmake.org/files/v2.8/cmake-2.8.8.tar.gz
tar zxvf cmake-2.8.8.tar.gz
cd cmake-2.8.8
./bootstrap
# error: Cannot find appropriate C++ compiler on this system. Please specify one using environment variable CXX.
yum install gcc-c++
./bootstrap
make
make install

# 1.3
yum install ncurses-devel
yum install zlib-devel
cd /usr/local

cd /usr/local
wget http://mirrors.sohu.com/mysql/MySQL-5.6/mysql-5.6.40.tar.gz
# 源码安装参考: https://blog.csdn.net/hwh1231/article/details/71426844
groupadd -g 701 mysql
tar -xvf mysql-5.6.40.tar.gz
# mkdir mysql-5.6.26
# chown ${普通用户}:${普通用户} ./mysql-5.6.26
ln -s /usr/local/mysql-5.6.40 /usr/local/mysql
cmake . -DCMAKE_INSTALL_PREFIX=/usr/local/mysql-5.6.40 -DWITH_INNOBASE_STORAGE_ENGINE=1 -DMYSQL_USER=mysql -DDEFAULT_CHARSET=utf8 -DDEFAULT_COLLATION=utf8_general_ci
make & make install

# 搭建运行环境
yum install perl
cd /usr/local/mysql
useradd mysql
# 已经存在名称为 mysql 的用户组
groupdel mysql
useradd mysql
rm -rf /usr/local/mysql/data
mkdir -p /data/mysql-data
ln -s /data/mysql-data /usr/local/mysql/data
chown -R mysql:mysql /data/mysql-data /usr/local/mysql/data
cp support-files/mysql.server /etc/init.d/mysql
rm -f /etc/my.cnf # **如果/etc/目录下有my.cnf存在，需要把这个配置删除了**
yum install -y perl-Module-Install.noarch
perl scripts/mysql_install_db --user=mysql
vim /usr/local/mysql/my.cnf # 修改内容参考 附录中的 `my.cnf` 实例
service mysql start
# error: permission deny
# chmod a+wrx /etc/init.d/mysql
# service mysql start
# # error: log-error set to '/var/log/mariadb/mariadb.log', however file don't exists. Create writable for user 'mysql
# mkdir /var/log/mariadb 
# touch /var/log/mariadb/mariadb.log 
# chown -R mysql:mysql  /var/log/mariadb/
# service mysql start
# # error: mysqld_safe Directory '/temp' for UNIX socket file don't exists
# mkdir /temp
# chown -R mysql:mysql  /temp
# service mysql start
# # error: Starting MySQL.The server quit without updating PID file 
chkconfig mysql on
service mysql stop
vim /etc/profile
    PATH=$PATH:/usr/local/mysql/bin
    export PATH
source /etc/profile
# ./bin/mysqladmin -u root password 'root@passwd' # 默认基于 localhost
./bin/mysqladmin -u root -h 172.19.244.31 password 'root@passwd'
vim /etc/ld.so.conf
    /usr/local/mysql/lib/
ldconfig # 添加新的动态链接库时需要运行该命令
# TODO: MYSQL 主从配置

yum install -y flex bison
```

## 数据库环境初始化
```bash

mysql -uroot -proot@passwd -h 172.19.244.31 -e "grant all on *.* to 'tars'@'172.19.244.31' identified by 'tars2015' with grant option;"
mysql -uroot -proot@passwd -h 172.19.244.31 -e "flush privileges;"
cd /mnt/framework/sql/
sed -i "s/192.168.2.131/172.19.244.31/g" `grep 192.168.2.131 -rl ./*`
sed -i "s/db.tars.com/172.19.244.31/g" `grep db.tars.com -rl ./*`
chmod u+x exec-sql.sh
./exec-sql.sh
# 提示权限不足，对脚本进行了稍许修改,以下为修改内容
# mysql -uroot -proot@passwd -h 172.19.244.31 -e "create database db_tars"
# mysql -uroot -proot@passwd -h 172.19.244.31 -e "create database tars_stat"
# mysql -uroot -proot@passwd -h 172.19.244.31 -e "create database tars_property"
# mysql -uroot -proot@passwd -h 172.19.244.31 -e "create database db_tars_web"
# mysql -uroot -proot@passwd -h 172.19.244.31 db_tars < db_tars.sql
```


## Tars框架运行环境搭建
```bash
# 这些步骤其实没有必要
# cd /mnt
# wget https://github.com/TarsCloud/Tars/archive/master.zip
cd /usr/local
mkdir app
cd app
mkdir tars
# # TODO: chown ${普通用户}:${普通用户} ./tars/
# cd /mnt
# yum install unzip
# unzip master.zip
# rm -f master.zip
# cd Tars-master/build
# # ERROR: make: *** No rule to make target `framework-tar'.  Stop
# wget https://github.com/TarsCloud/TarsFramework/archive/master.zip
# unzip master.zip
# cd TarsFramework-master

cd /mnt/framework/build
make framework-tar
cp framework.tgz /usr/local/app/tars/
cd /usr/local/app/tars/
tar xzfv framework.tgz

sed -i "s/192.168.2.131/172.19.244.31/g" `grep 192.168.2.131 -rl ./*`
sed -i "s/db.tars.com/172.19.244.31/g" `grep db.tars.com -rl ./*`
sed -i "s/registry.tars.com/172.19.244.31/g" `grep registry.tars.com -rl ./*`
sed -i "s/web.tars.com/172.19.244.31/g" `grep web.tars.com -rl ./*`

chmod u+x tars_install.sh

./tars_install.sh # 这个会开启 tars 各个服务
./tarspatch/util/init.sh # 部署管理平台并启动web管理平台

# 开始部署管理平台
wget -qO- https://raw.githubusercontent.com/creationix/nvm/v0.33.11/install.sh | bash
source ~/.bashrc
nvm install v8.11.3
cd /mnt/
git clone https://github.com/TarsCloud/TarsWeb.git
cd TarsWeb
sed -i 's/db.tars.com/172.19.244.31/g' config/webConf.js
sed -i 's/registry.tars.com/172.19.244.31/g' config/tars.conf
npm install -g pm2 --registry=https://registry.npm.taobao.org
npm install --registry=https://registry.npm.taobao.org
npm run prd
mkdir -p /data/log/tars/
```

## 搭建开发环境

```bash
## 下载 C++ 基础服务框架（TarsFramework）
yum install -y git
git clone https://github.com/TarsCloud/TarsFramework.git
mv TarsFramework framework
cd framework
git submodule update --init --recursive
## 安装c++语言框架
cd build/
chmod u+x build.sh
./build.sh all
./build.sh install

```

## 附录

my.cnf 实例

```bash
[mysqld]

# Remove leading # and set to the amount of RAM for the most important data
# cache in MySQL. Start at 70% of total RAM for dedicated server, else 10%.
innodb_buffer_pool_size = 128M

# Remove leading # to turn on a very important data integrity option: logging
# changes to the binary log between backups.
log_bin

# These are commonly set, remove the # and set as required.
basedir = /usr/local/mysql
datadir = /usr/local/mysql/data
# port = .....
# server_id = .....
socket = /tmp/mysql.sock

bind-address={$your machine ip}

# Remove leading # to set options mainly useful for reporting servers.
# The server defaults are faster for transactions and fast SELECTs.
# Adjust sizes as needed, experiment to find the optimal values.
join_buffer_size = 128M
sort_buffer_size = 2M
read_rnd_buffer_size = 2M

sql_mode=NO_ENGINE_SUBSTITUTION,STRICT_TRANS_TABLES

```

## 疑问

- rapidjson版本:	1.0.2版本（c++语言框架依赖）

## 参考
- 简介: https://github.com/TarsCloud/Tars/blob/master/Introduction.md 
- https://github.com/TarsCloud/Tars/blob/master/Install.md 
  - 一键部署安装脚本: https://github.com/TarsCloud/Tars/blob/master/build/install.sh
- 网友写的安装教程
  - https://cloud.tencent.com/developer/article/1372998  
  - https://github.com/maq128/temp/blob/master/kb/tars%E5%B0%8F%E7%99%BD%E5%AE%89%E8%A3%85%E5%BF%85%E6%88%90%E6%89%8B%E5%86%8C.md
- TarsDocker https://github.com/TarsCloud/TarsDocker