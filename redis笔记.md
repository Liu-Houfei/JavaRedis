# Redis基础

## CentOS下安装redis


### 1.xshell连接centos8 [桥接模式]

防火墙命令

```
1.查看防火墙状态
systemctl status firewalld.service

2.关闭防火墙
systemctl stop firewalld.service

3.禁用防火墙
systemctl disable firewalld.service
```



ssh命令

```
1.检查是否安装openssh-server
rpm -qa |grep ssh
2.安装openssh-server
yum install openssh-server
3.开启ssh服务
systemctl start sshd.service
4.设置开机启动服务
systemctl enable sshd.service
5.检查服务状态
systemctl status sshd.service
```



查看网络信息

```
ip addr
或者
ifconfig
```



1.Linux下切换用户到root

```
#su -
```

输入root密码后可切换



2.设置IP地址、子网掩码和网关

```
#vi /etc/sysconfig/network-scripts/ifcfg-ens(网卡号)
```

修改为静态IP

```
BOOTPROTO=static
IPADDR=192.168.1.100   要与主机网段一致
NETMASK=255.255.255.0
GATEWAY=192.168.1.2
ONBOOT=yes
```



5.设置主机名(可略过)

```
#vi /etc/sysconfig/network
```

```
NETWORKING=yes
HOSTNAME=mytest01
```

```
#vim /etc/hostname
```



6.重启网络服务

```
service network restart
或者
systemctl restart network
```

出现**Failed to restart network.service: Unit network.service not found**错误的原因:

不同版本的操作系统,重启网络的命令不同

解决方法:

- 可以尝试使用以下命令：

```
service network-manager restart
```

- 如果是 Kali Linux（Debian），则需要用以下命令：

```
service networking restart
```

- 如果是Centos 8，则需要用以下命令：

```
nmcli c reload
```



### 2.安装redis

1.下载redis压缩包(redis)

2.xftp工具连接虚拟机,将redis压缩包放到`/opt/mysoftwares`目录下

3.进入`/opt/mysoftwares`目录下,解压redis到`/opt`目录

```
tar -zvxf redis.xxx.tar.gz -C /opt
```

4.进入解压目录/opt/redisxxx

```
cd /opt/redis-xxx
```

5.安装gcc编译器

```
yum -y install gcc  (联网情况下)
```

6.在/opt/redis-xxxx目录下执行make命令

```
make
```

7.执行清理上次编译

```
make distclean
```

8.再次编译

```
make
```

9.配置环境变量

```
make install
```

```
[root@localhost redis-6.0.6]# cd /
[root@localhost /]# ls
bin  boot  dev  etc  home  lib  lib64  media  mnt  opt  proc  root  run  sbin  srv  sys  tmp  usr  var
[root@localhost /]# cd usr/local/bin/
[root@localhost bin]# ls  
redis-benchmark  redis-check-aof  redis-check-rdb  redis-cli  redis-sentinel  redis-server  (这些都是redis的命令)

```



> Tips:如果make过程中出现了一堆错误:则是由于GCC与redis版本不一致造成的,更换成同一时间段的软件包即可.



### 3.启动redis

```
1.前台启动
redis-server

2.后台启动
redis-server &

3.启动时指定配置文件
redis-server redis.conf &
```

![image-20210924194554812](images/image-20210924194554812.png)



### 4.关闭rendis

```
1.通过kill杀死redis进程
  ps -ef|grep redis
  kill redis编号
  
2.通过redis命令
redis-cli shutdown

```



### 5.启动redis客户端

```
1.直接连接(默认IP:127.0.0.1  端口:6379)
redis-cli

2.指定IP和端口连接
redis-cli -h ip地址 -p 端口
```



### 6.退出redis客户端

```
1.通过关闭进程
  ps -ef|grep redis
  kill pid
  
2.在客户端执行
exit

```



### 7.redis 基础知识

```
1.测试redis性能
redis-benchmark

2.查看redis服务是否正常运行
ping   如果正常--pong

3.查看redis的统计信息
info
```

```
[root@localhost redis-6.0.6]# redis-cli
127.0.0.1:6379> ping
PONG
```

```
127.0.0.1:6379> info  (所有信息)
# Server
redis_version:6.0.6
redis_git_sha1:00000000
redis_git_dirty:0
redis_build_id:cd3585ccb8e2dce0
redis_mode:standalone
os:Linux 4.18.0-305.3.1.el8.x86_64 x86_64
arch_bits:64
multiplexing_api:epoll
atomicvar_api:atomic-builtin
gcc_version:8.4.1
process_id:42794
run_id:d4d0f0d4a6fe56857bbe050bd89619d8988c29e2
tcp_port:6379
uptime_in_seconds:777
uptime_in_days:0
hz:10
configured_hz:10
lru_clock:5095152
executable:/opt/redis-6.0.6/redis-server
config_file:

# Clients
connected_clients:51
client_recent_max_input_buffer:2
client_recent_max_output_buffer:0
blocked_clients:0
tracking_clients:0
clients_in_timeout_table:0

# Memory
used_memory:2426344
used_memory_human:2.31M
used_memory_rss:6467584
used_memory_rss_human:6.17M
used_memory_peak:4901032
used_memory_peak_human:4.67M
used_memory_peak_perc:49.51%
used_memory_overhead:1668342
used_memory_startup:801864
used_memory_dataset:758002
used_memory_dataset_perc:46.66%
allocator_allocated:2467808
allocator_active:2940928
allocator_resident:5218304
total_system_memory:1905602560
total_system_memory_human:1.77G
used_memory_lua:37888
used_memory_lua_human:37.00K
used_memory_scripts:0
used_memory_scripts_human:0B
number_of_cached_scripts:0
maxmemory:0
maxmemory_human:0B
maxmemory_policy:noeviction
allocator_frag_ratio:1.19
allocator_frag_bytes:473120
allocator_rss_ratio:1.77
allocator_rss_bytes:2277376
rss_overhead_ratio:1.24
rss_overhead_bytes:1249280
mem_fragmentation_ratio:2.71
mem_fragmentation_bytes:4083752
mem_not_counted_for_evict:0
mem_replication_backlog:0
mem_clients_slaves:0
mem_clients_normal:866286
mem_aof_buffer:0
mem_allocator:jemalloc-5.1.0
active_defrag_running:0
lazyfree_pending_objects:0

# Persistence
loading:0
rdb_changes_since_last_save:0
rdb_bgsave_in_progress:0
rdb_last_save_time:1632485062
rdb_last_bgsave_status:ok
rdb_last_bgsave_time_sec:0
rdb_current_bgsave_time_sec:-1
rdb_last_cow_size:401408
aof_enabled:0
aof_rewrite_in_progress:0
aof_rewrite_scheduled:0
aof_last_rewrite_time_sec:-1
aof_current_rewrite_time_sec:-1
aof_last_bgrewrite_status:ok
aof_last_write_status:ok
aof_last_cow_size:0
module_fork_in_progress:0
module_fork_last_cow_size:0

# Stats
total_connections_received:906
total_commands_processed:1754708
instantaneous_ops_per_sec:0
total_net_input_bytes:94706720
total_net_output_bytes:1316933403
instantaneous_input_kbps:0.00
instantaneous_output_kbps:0.00
rejected_connections:0
sync_full:0
sync_partial_ok:0
sync_partial_err:0
expired_keys:0
expired_stale_perc:0.00
expired_time_cap_reached_count:0
expire_cycle_cpu_milliseconds:20
evicted_keys:0
keyspace_hits:500000
keyspace_misses:0
pubsub_channels:0
pubsub_patterns:0
latest_fork_usec:298
migrate_cached_sockets:0
slave_expires_tracked_keys:0
active_defrag_hits:0
active_defrag_misses:0
active_defrag_key_hits:0
active_defrag_key_misses:0
tracking_total_keys:0
tracking_total_items:0
tracking_total_prefixes:0
unexpected_error_replies:0

# Replication
role:master
connected_slaves:0
master_replid:5d4a6173e3ed6265f31d1fe1adb671294e14bb7c
master_replid2:0000000000000000000000000000000000000000
master_repl_offset:0
second_repl_offset:-1
repl_backlog_active:0
repl_backlog_size:1048576
repl_backlog_first_byte_offset:0
repl_backlog_histlen:0

# CPU
used_cpu_sys:17.207496
used_cpu_user:12.715607
used_cpu_sys_children:0.005248
used_cpu_user_children:0.001544

# Modules

# Cluster
cluster_enabled:0

# Keyspace
db0:keys=4,expires=0,avg_ttl=0

```




## key命令

### 1. 查看数据库中的所有key

```sql
127.0.0.1:6379> set k1 v1
OK
127.0.0.1:6379> keys *
1) "k1"

```



### 2. 使用 * 表示 0 或多个字符

keys k*：查看数据库中所有以k开头的key

keys k*o：查看数据库中以h开头、以o结尾的key

```sql
127.0.0.1:6379> keys *name
1) "username"
127.0.0.1:6379> keys us*me
1) "username"
```



### 3. 使用 ？ 表示单个

keys h?o：查看数据库中所有以h开头，以o结尾、并且中间只有一个字符的key.

```sql
127.0.0.1:6379> keys *
1) "username"
2) "k1"
127.0.0.1:6379> keys user?ame
1) "username"

```

### 4. 使用[a,b,c]表示包含[]内的字符

```sql
127.0.0.1:6379> keys *
1) "adress"
2) "address"
3) "username"
4) "k1"
127.0.0.1:6379> keys ad[abcder]ress
1) "address"

```



### 5. exists 判断key是否存在

```sql
##存在 key 返回 1，其他返回 0
127.0.0.1:6379> exists username
(integer) 1

##使用多个 key，返回存在的 key 的数量
127.0.0.1:6379> exists username address email
(integer) 2

```



### 6.移动key到指定数据库实例

```sql
127.0.0.1:6379> keys *
1) "adress"
2) "address"
3) "username"
4) "k1"

## 将adress移到数据库实例2
127.0.0.1:6379> move adress 2
(integer) 1

127.0.0.1:6379> keys *
1) "address"
2) "username"
3) "k1"
127.0.0.1:6379> 

```



### 7. expire设置key的生存时间

```sql
127.0.0.1:6379> keys *
1) "address"
2) "username"
3) "k1"

##设置username的生存时间为5秒
127.0.0.1:6379> expire username 5   
(integer) 1

127.0.0.1:6379> keys *
1) "address"
2) "username"
3) "k1"
127.0.0.1:6379> keys *
1) "address"
2) "k1"
```



### 8. ttl 返回key的剩余生存时间

以秒为单位，返回 key 的剩余生存时间（ttl: time to live） 

返回值:

● -1 ：没有设置 key 的生存时间， key 永不过期

● -2 ：key 不存在

● 数字：key 的剩余时间，秒为单位

```sql
127.0.0.1:6379> keys *
1) "readlight"
2) "address"
3) "k1"
127.0.0.1:6379> expire readlight 600
(integer) 1
## 查询readlight的剩余生存时间
127.0.0.1:6379> ttl readlight
(integer) 595
127.0.0.1:6379> ttl address
(integer) -1
127.0.0.1:6379> ttl email
(integer) -2
127.0.0.1:6379> 

```



### 9. 查询key的数据类型

查看 key 所存储值的数据类型返回值：字符串表示的数据类型.

● none (key 不存在)

● string (字符串)

● list (列表)

● set (集合)

● zset (有序集)

● hash (哈希表)

```sql
127.0.0.1:6379> keys *
1) "readlight"
2) "address"
3) "k1"

##type查询key的类型
127.0.0.1:6379> type readlight
string
```



### 10. 删除key

删除存在的 key ，不存在的 key 忽略。

返回值：数字，删除的 key 的数量

```sql
127.0.0.1:6379> del readligth
(integer) 0
127.0.0.1:6379> keys *
1) "readlight"
2) "address"
3) "k1"
127.0.0.1:6379> del k1
(integer) 1
127.0.0.1:6379> keys *
1) "readlight"
2) "address"
127.0.0.1:6379> 

```



### 11.rename重命名key

```sql
127.0.0.1:6379> keys *
1) "readlight"
2) "address"
127.0.0.1:6379> rename address location
OK
127.0.0.1:6379> keys *
1) "location"
2) "readlight"
```







## 5种数据类型





## 字符串类型





## list操作



## set操作



## hash操作



## zset操作





## redis配置文件



## redis持久化



## redis事务



## redis消息的发布与订阅



## redis主从复制



## redis哨兵



## jedis

