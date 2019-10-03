## HOW TO SETUP MYSQL MASTER/SLAVE REPLICATION ?


## Mysql Master side Configuration

sudo vim /etc/my.cnf

`
[client]
port		= 3306
socket		= /var/run/mysqld/mysqld.sock


[mysqld_safe]
socket		= /var/run/mysqld/mysqld.sock
nice		= 0

[mysqld]
user		= mysql
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
port		= 3306
basedir		= /usr
datadir		= /opt/mount_point/database/mysql/mysql
tmpdir		= /tmp
lc-messages-dir	= /usr/share/mysql
open_files_limit=32768
skip-external-locking
group_concat_max_len=1000000
bind-address           = 0.0.0.0
key_buffer		= 16M
max_allowed_packet	= 16M
thread_stack		= 192K
thread_cache_size       = 8
myisam-recover         = BACKUP
max_connections        = 255
query_cache_limit	= 1M
query_cache_size        = 16M
log_error = /var/log/mysql/error.log

server-id               = 101
binlog-format   = mixed
log_bin                 = /var/log/mysql/mysql-bin.log
innodb_flush_log_at_trx_commit=1
sync_binlog=1
expire_logs_days        = 3
max_binlog_size         = 100M


log_slave_updates=1
replicate-do-db=financialForms
replicate-do-db=encoredb
replicate-do-db=bi
replicate-do-db=ams
replicate-do-db=ams_dump
replicate-do-db=psychometric
replicate-do-db=forms_management
relay-log= mysql-relay-bin




log_output = TABLE
slow_query_log = 1
long_query_time = 2



[mysqldump]
quick
quote-names
max_allowed_packet	= 16M

[mysql]

[isamchk]
key_buffer		= 16M

innodb_buffer_pool_size=1536M
innodb_log_buffer_size=32M
innodb_log_file_size=1G
innodb_write_io_threads=16
innodb_read_io_threads=16
open_files_limit=8000

!includedir /etc/mysql/conf.d/

`

sudo /etc/init.d/mysql restart


sudo mysql -u root -p

`
GRANT REPLICATION SLAVE ON *.* TO 'repl_user'@'slave server IP' IDENTIFIED BY 'repl_user_password';

SHOW MASTER STATUS\G

mysql> SHOW MASTER STATUS\G
*************************** 1. row ***************************
             File: mysql-bin.000095
         Position: 40386
     Binlog_Do_DB: 
 Binlog_Ignore_DB: 
Executed_Gtid_Set: 
1 row in set (0.00 sec)

`





## Mysql Slave side Configuration

sudo vim /etc/my.cnf

`
[client]
port		= 3306
socket		= /var/run/mysqld/mysqld.sock


[mysqld_safe]
socket		= /var/run/mysqld/mysqld.sock
nice		= 0

[mysqld]
user		= mysql
pid-file	= /var/run/mysqld/mysqld.pid
socket		= /var/run/mysqld/mysqld.sock
port		= 3306
basedir		= /usr
datadir		= /opt/mount_point/database/mysql/mysql
tmpdir		= /tmp
lc-messages-dir	= /usr/share/mysql
open_files_limit=32768
skip-external-locking
key_buffer		= 16M
max_allowed_packet	= 16M
thread_stack		= 192K
thread_cache_size       = 8
myisam-recover         = BACKUP
max_connections        = 255
query_cache_limit	= 1M
query_cache_size        = 16M
log_error = /var/log/mysql/error.log

server-id               = 102
binlog-format   = mixed
log_bin                 = /var/log/mysql/mysql-bin.log
relay-log               = mysql-relay-bin
log-slave-updates = 1
read-only  = 1
binlog-ignore-db = mysql
replicate-ignore-db = mysql
log_output = TABLE
slow_query_log = 1
long_query_time = 2
expire_logs_days        = 1
max_binlog_size         = 100M
slave_sql_verify_checksum = 0

log_output = TABLE
slow_query_log = 1
long_query_time = 2



[mysqldump]
quick
quote-names
max_allowed_packet	= 16M

[mysql]

[isamchk]
key_buffer		= 16M

innodb_buffer_pool_size=1536M
innodb_log_buffer_size=32M
innodb_log_file_size=1G
innodb_write_io_threads=16
innodb_read_io_threads=16
open_files_limit=8000

!includedir /etc/mysql/conf.d/

`

sudo /etc/init.d/mysql restart




# Below commands to configure mysql replication

sudo mysql -u root -p

`
reset slave;

stop slave IO_THREAD;

start slave IO_THREAD;

CHANGE MASTER TO MASTER_HOST='Master server IP', MASTER_PORT=3306, MASTER_USER='repl_user', MASTER_PASSWORD='repl_user_password', MASTER_LOG_FILE='mysql-bin.000095', MASTER_LOG_POS=40386;		


START SLAVE;

SHOW SLAVE STATUS\G

`


## for more info below link:
`https://fromdual.com/how_to_setup_mysql_master-slave_replication`




     
