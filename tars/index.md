

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
# useradd -M -g mysql -u 1101 -s /sbin/nologin  mysql 
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
useradd mysq
useradd mysql
rm -rf /usr/local/mysql/data
mkdir -p /data/mysql-data
ln -s /data/mysql-data /usr/local/mysql/data
chown -R mysql:mysql /data/mysql-data /usr/local/mysql/data
cp support-files/mysql.server /etc/init.d/mysql
# **如果/etc/目录下有my.cnf存在，需要把这个配置删除了**
yum install -y perl-Module-Install.noarch
perl scripts/mysql_install_db --user=mysql
vim /usr/local/mysql/my.cnf # 修改内容参考 附录中的 `my.cnf` 实例
service mysql start
# error: service mysql start

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

## 参考

- https://github.com/TarsCloud/Tars/blob/master/Install.md 
  - 一键部署安装脚本: https://github.com/TarsCloud/Tars/tree/master/deploy 