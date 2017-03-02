title: MySQL5.7.13 源码非编译安装
date: 2016-07-22 10:00
categories: TECH
tags: [MySQL]
---

###[MySQL5.7下载地址](http://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.13-linux-glibc2.5-x86_64.tar.gz)
##### 前提：阿里云服务器是CentOS6.5版本，下载的时候看清楚Select Platform，新的MySQL有600M，比之前多出一倍。

- 下载MySQL，完成解压，解压后将文件夹移到/usr/local/下
#####有人推荐axel下载多线程，支持断点，但是centos下没有，需要下载成yum插件，debian系统可以直接安装。尝试下载，没有反应，可能国外服务器不稳定吧，以后再试。
```bash
wget http://dev.mysql.com/get/Downloads/MySQL-5.7/mysql-5.7.13-linux-glibc2.5-x86_64.tar.gz
tar -zxvf mysql-5.7.13-linux-glibc2.5-x86_64.tar.gz
mv mysql-5.7.13-linux-glibc2.5-x86_64 /usr/local/
```
#####Tips:如果下载速度很慢，我是本地迅雷下载完成后通过scp方式上传到阿里云的，上传了20分钟。

- 在/usr/local/下新建一个软连接，这样以后可以安装多个MySQL实例
```bash
ln -s mysql-5.7.13-linux-glibc2.5-x86_64 mysql
```

- 新增MySQL用户和组，一般都存在，除非服务器是新的,之前没装过MySQL 设置mysql文件夹的权限组是root
```
groupadd mysql
useradd mysql -g mysql

cd /usr/local/mysql
chowm -R root:mysql mysql .
```

- 下面是关键的一步，设置my.cnf，删除原先的配置文件
```bash
unlink /etc/my.cnf
```

新的my.cnf 放进/etc/下
```bash
[client]
user=test
password=abcd1234_
 
[mysqld]
########basic settings########
server-id = 11
port = 3306
user = mysql
bind_address = 10.0.0.1
autocommit = 0
character_set_server=utf8mb4
skip_name_resolve = 1
max_connections = 800
max_connect_errors = 1000
datadir = /data/mysql_data
transaction_isolation = READ-COMMITTED
explicit_defaults_for_timestamp = 1
join_buffer_size = 134217728
tmp_table_size = 67108864
tmpdir = /tmp
max_allowed_packet = 16777216
sql_mode = "STRICT_TRANS_TABLES,NO_ENGINE_SUBSTITUTION,NO_ZERO_DATE,NO_ZERO_IN_DATE,ERROR_FOR_DIVISION_BY_ZERO,NO_AUTO_CREATE_USER"
interactive_timeout = 1800
wait_timeout = 1800
read_buffer_size = 16777216
read_rnd_buffer_size = 33554432
sort_buffer_size = 33554432
########log settings########
log_error = error.log
slow_query_log = 1
slow_query_log_file = slow.log
log_queries_not_using_indexes = 1
log_slow_admin_statements = 1
log_slow_slave_statements = 1
log_throttle_queries_not_using_indexes = 10
expire_logs_days = 90
long_query_time = 2
min_examined_row_limit = 100
########replication settings########
master_info_repository = TABLE
relay_log_info_repository = TABLE
log_bin = bin.log
sync_binlog = 1
gtid_mode = on
enforce_gtid_consistency = 1
log_slave_updates
binlog_format = row
relay_log = relay.log
relay_log_recovery = 1
binlog_gtid_simple_recovery = 1
slave_skip_errors = ddl_exist_errors
########innodb settings########
innodb_page_size = 8192
innodb_buffer_pool_size = 512M
innodb_buffer_pool_instances = 8
innodb_buffer_pool_load_at_startup = 1
innodb_buffer_pool_dump_at_shutdown = 1
innodb_lru_scan_depth = 2000
innodb_lock_wait_timeout = 5
innodb_io_capacity = 4000
innodb_io_capacity_max = 8000
innodb_flush_method = O_DIRECT
innodb_file_format = Barracuda
innodb_file_format_max = Barracuda
#innodb_log_group_home_dir = /redolog/
#innodb_undo_directory = /undolog/
innodb_undo_logs = 128
innodb_undo_tablespaces = 3
innodb_flush_neighbors = 0
innodb_log_file_size = 1G
innodb_log_buffer_size = 16777216
innodb_purge_threads = 4
innodb_large_prefix = 1
innodb_thread_concurrency = 64
innodb_print_all_deadlocks = 1
innodb_strict_mode = 1
innodb_sort_buffer_size = 67108864
########semi sync replication settings########
plugin_dir=/usr/local/mysql/lib/plugin
plugin_load = "rpl_semi_sync_master=semisync_master.so;rpl_semi_sync_slave=semisync_slave.so"
loose_rpl_semi_sync_master_enabled = 1
loose_rpl_semi_sync_slave_enabled = 1
loose_rpl_semi_sync_master_timeout = 5000
 
[mysqld-5.7]
innodb_buffer_pool_dump_pct = 40
innodb_page_cleaners = 4
innodb_undo_log_truncate = 1
innodb_max_undo_log_size = 2G
innodb_purge_rseg_truncate_frequency = 128
binlog_gtid_simple_recovery=1
log_timestamps=system
transaction_write_set_extraction=MURMUR32
show_compatibility_56=on
```
#####有几个注意的参数
```bash
bind_address = 10.0.0.1 	自己服务器的ip地址
datadir = /data/mysql_data   MySQL数据存放文件夹 在root下新建一个data文件夹 里面什么都不要
#innodb_log_group_home_dir = /redolog/  这两行注释掉，不需要，如果没这两目录 会报错
#innodb_undo_directory = /undolog/
```

- 初始化Mysql，完成后在datadir下有MySQL的库和相关文件(重要的有slow.log，error.log)
```bash
cd /usr/local/mysql
bin/mysql --initialize
```
- 如果初始化成功，会看到如下文件
![](http://7xo7qs.com1.z0.glb.clouddn.com/mysql_data.png)

- 如果不是以上文件，则初始化失败，查看error.log，具体分析，虽然有时候网上的回答也解决不了，只能具体问题具体分析
##### 我当时也是报错了，试了很久都不行，直接把整个阿里云恢复了，还是不行，最后还是配置文件出了问题。

- 最后一步了，把启动项放到/etc/init.d下 这样就能直接启动了(可以命名相同 也可以不同 启动名称而已)
```bash
cd /usr/local/mysql
mv support-files/mysql.server /etc/init.d/mysql.server
```

- 尝试启动MySQL，启动成功。可以连接了 回车输入初始密码
#####MySQL5.7初始化成功后是有默认密码的，这和之前的版本都不一样，初始密码在error.log中，就是最后那一串字符
![](http://7xo7qs.com1.z0.glb.clouddn.com/initpwd.png)
```bash
/etc/init.d/mysql.server start
Starting MySQL..                                           [  OK  ]

/usr/local/mysql/bin/mysql -uroot -p
```







