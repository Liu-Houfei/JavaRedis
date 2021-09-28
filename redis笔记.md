# Redis基础

## 1.CentOS下安装redis

### 1.1 xshell连接centos8 [桥接模式]

> Tips:一些Linux命令,可能会用到

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



#### 1.1.1 Linux下切换用户到root

```
#su -
```

输入root密码后可切换



#### 1.1.2 设置IP地址,子网掩码,网关和DNS(联网)

```
#vi /etc/sysconfig/network-scripts/ifcfg-ens(网卡号)
```

```
#改以下内容
BOOTPROTO=static
IPADDR=192.168.1.100   要与主机网段一致
NETMASK=255.255.255.0
GATEWAY=192.168.1.2
ONBOOT=yes
```



添加DNS

```sql
vi /etc/resolv.conf

#添加DNS
nameserver 192.168.1.1
```





#### 1.1.3 设置主机名(可略过)

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



#### 1.1.4重启网络服务

```
service network restart
或者
systemctl restart network
```

> Bug:出现**Failed to restart network.service: Unit network.service not found**错误的原因:

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



### 1.2 Linux下安装redis

#### 1.2.1 下载redis压缩包(redis)

#### 1.2.2 xftp工具连接虚拟机,将redis压缩包放到/opt/mysoftwares目录下

#### 1.2.3 进入/opt/mysoftwares目录下,解压redis到/opt目录

```
tar -zvxf redis.xxx.tar.gz -C /opt
```

#### 1.2.4 进入解压目录/opt/redisxxx

```
cd /opt/redis-xxx
```

#### 1.2.5 安装gcc编译器

```
yum -y install gcc  (联网情况下)
```

#### 1.2.6 在/opt/redis-xxxx目录下执行make命令

```
make
```

> Tips:
执行清理上次编译,再make失败时使用

```
make distclean
```

再次编译

```
make
```

#### 1.2.7 配置环境变量

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



#### 1.2.8 启动redis

```
1.前台启动
redis-server

2.后台启动
redis-server &

3.启动时指定配置文件
redis-server redis.conf &
```

![image-20210924194554812](images/image-20210924194554812.png)



#### 1.2.9 关闭rendis

```
1.通过kill杀死redis进程
  ps -ef|grep redis
  kill redis编号
  
2.通过redis命令
redis-cli shutdown

```



#### 1.2.10 启动redis客户端

```
1.直接连接(默认IP:127.0.0.1  端口:6379)
redis-cli

2.指定IP和端口连接
redis-cli -h ip地址 -p 端口
```



#### 1.2.11 退出redis客户端

```
1.通过关闭进程
  ps -ef|grep redis
  kill pid
  
2.在客户端执行
exit

```



### 1.3 redis 基础知识

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

### 1.4 查看redis信息
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



## 2.key命令和5种数据类型

### redis命令大全*

http://redisdoc.com



### 2.1 key命令

#### 2.1.1 set

设置key-value

如果key已经存在,会被覆盖

```sql
127.0.0.1:6379> set k1 v1
OK
```



#### 2.1.2  key查询相关

##### (1) keys *

查看所有key

```sql
127.0.0.1:6379> keys *
1) "k1"
```



##### (2)使用 * 表示 0 或多个字符

keys k*：查看数据库中所有以k开头的key

keys k*o：查看数据库中以h开头、以o结尾的key

```sql
127.0.0.1:6379> keys *name
1) "username"
127.0.0.1:6379> keys us*me
1) "username"
```



##### (3)2.3 使用 ？ 表示单个

keys h?o：查看数据库中所有以h开头，以o结尾、并且中间只有一个字符的key.

```sql
127.0.0.1:6379> keys *
1) "username"
2) "k1"
127.0.0.1:6379> keys user?ame
1) "username"

```



##### (4)使用[a,b,c]表示包含[]内的字符

```sql
127.0.0.1:6379> keys *
1) "adress"
2) "address"
3) "username"
4) "k1"
127.0.0.1:6379> keys ad[abcder]ress
1) "address"

```



#### 2.1.3 exists

判断key是否存在

```sql
##存在 key 返回 1，其他返回 0
127.0.0.1:6379> exists username
(integer) 1

##使用多个 key，返回存在的 key 的数量
127.0.0.1:6379> exists username address email
(integer) 2

```



#### 2.1.4 move

移动key到指定数据库实例

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



#### 2.1.5 key过期时间相关

##### (1) 以毫秒为单位设置key过期时间

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



##### (2)  设置过期时间(UNIX时间戳)

```sql
##设置key的过期时间,时间为UNIX时间戳
127.0.0.1:6379> expireat location 1000000
(integer) 1
```



##### (3) 以秒为单位返回key剩余过期时间

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



##### (4) 以毫秒为单位返回key剩余过期时间

```sql
127.0.0.1:6379> expire k1 600
(integer) 1
## pttl,以毫秒返回key剩余过期时间 
127.0.0.1:6379> pttl k1
(integer) 596314
```



##### (5) 取消key的过期时间,key将持久保持

```sql
##设置过期时间
127.0.0.1:6379> expire k1 6000
(integer) 1
##查看key剩余生存时间
127.0.0.1:6379> ttl k1
(integer) 5988
##取消key的过期时间
127.0.0.1:6379> persist k1
(integer) 1
127.0.0.1:6379> ttl k1
(integer) -1

```



#### 2.1.6 查询key的数据类型

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



#### 2.1.7 删除key

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



#### 2.1. 8 直接修改key名称

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



#### 2.1.9 仅当newKey不存在时,将key修改为newKey

```sql
127.0.0.1:6379> keys *
1) "k2"
2) "k1"
3) "k3"
##直接修改key名称,不论newKey是否已经存在
127.0.0.1:6379> rename k1 k2
OK
127.0.0.1:6379> keys *
1) "k2"
2) "k3"
##如果newKey存在,则不会修改
127.0.0.1:6379> renamenx k2 k3
(integer) 0
127.0.0.1:6379> keys *
1) "k2"
2) "k3"
127.0.0.1:6379> 

```



#### 2.1.10 序列化指定key

```sql
127.0.0.1:6379> set k1 v1
OK

## dum key 序列化指定key
127.0.0.1:6379> dump k1
"\x00\x02v1\b\x00\xe6\xc8\\\xe1bI\xf3c"

127.0.0.1:6379> keys *
1) "k1"
2) "location"
```





#### 2.1.11从当前数据库随机返回一个key

```sql
127.0.0.1:6379> keys *
1) "k2"
2) "k1"
3) "k3"
127.0.0.1:6379> randomkey
"k1"
127.0.0.1:6379> randomkey
"k3"
```



#### 2.1.12 flushall清空所有key

```sql
127.0.0.1:6379> flushall
2193:M 28 Sep 08:04:08.066 * DB saved on disk
OK
```



### 2.2  string命令

string是redis最基本的类型，你可以理解成与Memcached一模一样的类型，一个key对应一个value。

string类型是二进制安全的。意思是redis的string可以包含任何数据。比如jpg图片或者序列化的对象 。

string类型是Redis最基本的数据类型，一个redis中字符串value最多可以是512M.

| key      | value |
| -------- | ----- |
| username | 张三  |



#### 2.2.1设置和获取key-value操作

##### (1)set/get 

设置/获取单个key-value

如果key已经存在,则覆盖

```sql
127.0.0.1:6379> set k4 v4 
OK
127.0.0.1:6379> get k4
"v4"
```



##### (2)mset/mget 

设置/获取多个key-value

如果key已经存在,则覆盖

```sql
127.0.0.1:6379> mset k5 v5 k6 v6
OK
127.0.0.1:6379> mget k5 k6
1) "v5"
2) "v6"
```



##### (3)setnx/get 

设置/获取单个key-value

如果key已经存在,则失败

```sql
127.0.0.1:6379> setnx k5 v5
(integer) 0
127.0.0.1:6379> get k5
"v5"
127.0.0.1:6379> setnx k7 v7
(integer) 1
127.0.0.1:6379> get k7
"v7"
```



##### (4)msetnx/mget 

设置/获取多个key-value

如果key已经存在,则整体失败

```sql
127.0.0.1:6379> msetnx k5 v5 k8 v8
(integer) 0
127.0.0.1:6379> get k8
(nil)
127.0.0.1:6379> get k5
"v5"
```



##### (5)setex

设置key时附加过期时间

```sql
127.0.0.1:6379> setex k8 10000 v8
OK
127.0.0.1:6379> get k8
"v8"
127.0.0.1:6379> ttl k8
(integer) 9992
127.0.0.1:6379> pttl k8
(integer) 9986216
```



##### (6)getset

先获取value再设置key

```sql
127.0.0.1:6379> getset k9 v9
(nil)
127.0.0.1:6379> getset k8 lalala
"v8"
127.0.0.1:6379> get k8
"lalala"
```



##### (7)strlen

获取字符串长度

```sql
##strlen key
127.0.0.1:6379> strlen k8
(integer) 6
```



##### (8)getrange/setrange

范围获取和/范围设置

获取 key 中字符串值从 start 开始 到 end 结束 的子字符串,包括 start 和 end, 负数表示从字符串的末尾开始， -1 表示最后一个字符，负下标表示自右向左.

```sql
127.0.0.1:6379> set k10 helloworld
OK
##getrange key start end
127.0.0.1:6379> getrange k10 1 3
"ell"
##setrange key offset value
127.0.0.1:6379> setrange k10 1 00000
(integer) 10
127.0.0.1:6379> get k10
"h00000orld"
```



#### 2.2.2 del

删除key-value

```sql
##del key
127.0.0.1:6379> del k10
(integer) 1
```



#### 2.2.3 append

附加

```sql
##如果key不存在,则创建,并返回key的长度
127.0.0.1:6379> append k10 hello
(integer) 5
127.0.0.1:6379> get k10 
"hello"

##如果key存在,则附加,并返回key的长度
127.0.0.1:6379> append k10 hello
(integer) 10
127.0.0.1:6379> get k10 
"hellohello"
```



#### 2.2.4incr/decr/incrby/decrby

字符串加减

只有数字格式的才能加减,不是数字会报错

incr 加1

decr减1

incrby 加指定值

decrby 减指定值

```sql
127.0.0.1:6379> set k11 11
OK
##incr key
127.0.0.1:6379> incr k11
(integer) 12
##incrby key value
127.0.0.1:6379> incrby k11 5
(integer) 17
##decrby key value
127.0.0.1:6379> decrby k11 5
(integer) 12
##decr key
127.0.0.1:6379> decr k11
(integer) 11
```





### 2.3  list操作

它是一个字符串链表，left、right都可以插入添加；
如果键不存在，创建新的链表；
如果键已存在，新增内容；
如果值全移除，对应的键也就消失了。
链表的操作无论是头和尾效率都极高，但假如是对中间元素进行操作，效率就很惨淡了。



#### 2.3.1 lpush / rpush 

创建list

lpush类似创建一个栈,先进后出,逆序

rpush类似创建一个队列,先进先出,顺序

lrange 获取指定范围list的value

0是第一个,-1是最后一个

```sql
##语法:lpush key value[value...]
127.0.0.1:6379> lpush list06 1 2 3 a b c
(integer) 6
127.0.0.1:6379> lrange list06 0 -1
1) "c"
2) "b"
3) "a"
4) "3"
5) "2"
6) "1"

##语法:rpush key value[value...]
127.0.0.1:6379> rpush list07 1 2 3  a b c
(integer) 6
127.0.0.1:6379> lrange list07 0 -1
1) "1"
2) "2"
3) "3"
4) "a"
5) "b"
6) "c"
```



#### 2.3.2 lpop / rpop 

 弹出list最左/最右值

```sql
127.0.0.1:6379> lrange list06 0 -1
1) "c"
2) "b"
3) "a"
4) "3"
5) "2"
6) "1"
##语法:lpop key
127.0.0.1:6379> lpop list06
"c"
##语法:rpop key
127.0.0.1:6379> rpop list06
"1"
```



#### 2.3.3 lindex 

访问list指定index的值

```sql
127.0.0.1:6379> lpush list01 1 2 3 4 5 6 7 8 9 10
(integer) 10
##语法:lindex key index
127.0.0.1:6379> lindex list01 0
"10"
127.0.0.1:6379> lindex list01 -1
"1"

127.0.0.1:6379> rpush list02 1 2 3 4 5 6 7 8 9 10
(integer) 10
127.0.0.1:6379> lindex list02 0
"1"
127.0.0.1:6379> lindex list02 -1
"10"
```



#### 2.3.4 llen

获取list长度

```sql
##语法:llen key
127.0.0.1:6379> llen list01
(integer) 10
127.0.0.1:6379> llen list02
(integer) 10
```



#### 2.3.5 lrem

 删除n个value

```sql
##语法:lrem key count value
127.0.0.1:6379> lrem list01 1 2
(integer) 1
127.0.0.1:6379> lrange list01 0 -1
1) "10"
2) "9"
3) "8"
4) "7"
5) "6"
6) "5"
7) "4"
8) "3"
9) "1"
```



#### 2.3.6  ltrim

截取指定范围的值再赋值给key

```sql
##语法:ltrim key start end
127.0.0.1:6379> ltrim list02 1 -2
OK
127.0.0.1:6379> lrange list02 0 -1
1) "2"
2) "3"
3) "4"
4) "5"
5) "6"
6) "7"
7) "8"
8) "9"
```



#### 2.3.7 rpoplpush 

 将一个list中的值出栈,并且添加到另一个list的最左侧

```sql
127.0.0.1:6379> lpush list03 1 2 3 4 5
(integer) 5
127.0.0.1:6379> lpush list04 6 7 8 9 10
(integer) 5
## 将list03中的1出栈,添加到list04最左侧
##语法: rpoplpush source destnation
127.0.0.1:6379> rpoplpush list03 list04
"1"
127.0.0.1:6379> lrange list04 0 -1
1) "1"
2) "10"
3) "9"
4) "8"
5) "7"
6) "6"
127.0.0.1:6379> lrange list03 0 -1
1) "5"
2) "4"
3) "3"
4) "2"
127.0.0.1:6379> 

```



#### 2.3.8 lset 

从左向右替换index上的元素

```sql
127.0.0.1:6379> lpush list5 c java c++
(integer) 3
127.0.0.1:6379> lrange list5 0 -1
1) "c++"
2) "java"
3) "c"
##将第二个位置上的元素java修改为python
##语法:lset key index value
127.0.0.1:6379> lset list5 1 python
OK
127.0.0.1:6379> lrange list5 0 -1
1) "c++"
2) "python"
3) "c"
```



#### 2.3.9 linsert

 在list某个已有值的前后再添加具体值

```sql
127.0.0.1:6379> lrange list07 0 -1
1) "1"
2) "2"
3) "3"
4) "a"
5) "b"
6) "c"

##linsert key before|after index value
127.0.0.1:6379> linsert list07 before 1 lalala
(integer) 7
127.0.0.1:6379> lrange list07 0 -1
1) "lalala"
2) "1"
3) "2"
4) "3"
5) "a"
6) "b"
7) "c"

127.0.0.1:6379> linsert list07 after 1 hihihi
(integer) 9
127.0.0.1:6379> lrange list07 0 -1
1) "lalala"
2) "1"
3) "hihihi"
4) "2"
5) "3"
6) "a"
7) "b"
8) "c"

```



### 2.4 set操作

Redis的Set是string类型的无序集合。它是通过HashTable实现实现的.

set命令表格

![image-20210928075714255](images/image-20210928075714255.png)



set是单值多value的

#### 2.4.1 sadd/smembers 

添加set/获取set值

- sadd 将一个或多个 member 元素加入到集合 key 当中，已经存在于集合的 member 元素将被忽略，不会再加入。
  - 返回值：加入到集合的新元素的个数。不包括被忽略的元素
- smembers 获取集合 key 中的所有成员元素，不存在的 key 视为空集合

```sql
#添加set
127.0.0.1:6379> sadd k1 v1
(integer) 1
#如果value已经存在则不会添加
127.0.0.1:6379> sadd k1 v1
(integer) 0
#如果value和之前的不同则添加成功
127.0.0.1:6379> sadd k1 v1 v2
(integer) 1
127.0.0.1:6379> sadd k1 v1 v2 v3
(integer) 1
#获取set的成员
127.0.0.1:6379> smembers k1
1) "v2"
2) "v1"
3) "v3"
```



#### 2.4.2.scard 

获取集合里面元素的个数

```sql
127.0.0.1:6379> scard k1
(integer) 3
```



#### 2.4.3 srem

删除集合中的元素

```sql
127.0.0.1:6379> smembers k1
1) "v2"
2) "v1"
3) "v3"
127.0.0.1:6379> srem k1 v1
(integer) 1 
127.0.0.1:6379> smembers k1
1) "v2"
2) "v3"
```



#### 2.4.4 srandmember

 随机出集合中的几个元素

 *   从set集合里面随机取出2个
 *   如果超过最大数量就全部取出，
 *   如果写的值是负数，比如-3 ，表示需要取出3个，但是可能会有重复值。

```sql
127.0.0.1:6379> sadd k1 1 2 3 4 5 6 7 8 9 10 a b c d  e f g
(integer) 17
127.0.0.1:6379> srandmember k1 5
1) "6"
2) "5"
3) "d"
4) "2"
5) "7"
```



#### 2.4.5 spop

随机弹出几个元素

```sql
127.0.0.1:6379> smembers k1
 1) "1"
 2) "d"
 3) "e"
 4) "c"
 5) "a"
 6) "3"
 7) "7"
 8) "5"
 9) "6"
10) "g"
11) "4"
12) "b"
13) "8"
14) "9"
15) "f"
16) "2"
17) "10"
127.0.0.1:6379> spop k1 5
1) "g"
2) "7"
3) "1"
4) "5"
5) "f"
127.0.0.1:6379> smembers k1
 1) "6"
 2) "4"
 3) "b"
 4) "8"
 5) "9"
 6) "a"
 7) "c"
 8) "e"
 9) "3"
10) "2"
11) "10"
12) "d"
```



#### 2.4.6 smove

将k1中的某个元素交给k2

```sql
127.0.0.1:6379> sadd k1 1 2 3 4 5
(integer) 5
127.0.0.1:6379> sadd k2 6 7 8 9 10
(integer) 5
127.0.0.1:6379> smove k1 k2 1
(integer) 1
127.0.0.1:6379> smembers k1
1) "2"
2) "3"
3) "4"
4) "5"
127.0.0.1:6379> smembers k2
1) "1"
2) "6"
3) "7"
4) "8"
5) "9"
6) "10"

#如果要转移的元素不存在,则返回0
127.0.0.1:6379> smove k1 k2 a
(integer) 0
```



#### 2.4.7sdiff/sinter/sunion

集合运算

```sql
127.0.0.1:6379> smembers k1
1) "1"
2) "2"
3) "3"
4) "4"
5) "5"
127.0.0.1:6379> smembers k2
1) "1"
2) "6"
3) "7"
4) "8"
5) "9"
6) "10"
#在第一个set里面而不在后面任何一个set里面的项
127.0.0.1:6379> sdiff k1 k2 
1) "2"
2) "3"
3) "4"
4) "5"
#交集
127.0.0.1:6379> sinter k1 k2
1) "1"
#并集
127.0.0.1:6379> sunion k1 k2
 1) "1"
 2) "2"
 3) "3"
 4) "4"
 5) "5"
 6) "6"
 7) "7"
 8) "8"
 9) "9"
10) "10"
```



#### 2.4.8 sismembers

判断 member 元素是否是集合 key 的成员,返回值：member 是集合成员返回 1，其他返回 0 

```sql
127.0.0.1:6379> sismember k1 1
(integer) 1
127.0.0.1:6379> sismember k1 b
(integer) 0
```





### 2.5  hash操作*

Redis hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象。

```java
class user{
    private int id;
    private String userName;
  	private String location;
    
}
```

Redis hash 是一个键值对集合。
Redis hash是一个string类型的field和value的映射表，hash特别适合用于存储对象。

类似Java里面的Map<String,Object>

redis hash 是一个 string 类型的 field 和 value 的映射表，hash 特别适合用于存储对象。

单key:field-value

   field-value

students:id-1001

​    name-zhangsan

​    age-20



![image-20210928085144871](images/image-20210928085144871.png)



k-v模式不变,但是v是一个键值对.



#### 2.5.1 hset /hmset/hsetnx

- hset:将哈希表 key 中的域 field 的值设为 value ，如果 key 不存在，则新建 hash 表，执行赋值，如果有 field ,则覆盖值。
- hmset:同时将多个 field-value (域-值)设置到哈希表 key 中，此命令会覆盖已经存在的 field，hash 表 key 不存在，创建空的 hash 表，执行 hmset.

- hsetnx:如果 key 不存在，则新建 hash 表，执行赋值，如果有 field ,则失败.

```sql
#hset key field
127.0.0.1:6379> hset user id 1
(integer) 1
127.0.0.1:6379> hset user name zhangsan
(integer) 1
127.0.0.1:6379> hset user location beijing
(integer) 1

#hmset key field[field...]
127.0.0.1:6379> hmset user2 id 2 name lisi location shanghai
OK

#hsetnx key field value
127.0.0.1:6379> hsetnx user1 id 1
(integer) 0
127.0.0.1:6379> hsetnx user2 id 2
(integer) 0
127.0.0.1:6379> hsetnx user3 id 3
(integer) 1
```



#### 2.5.2 hget/hmget/hgetall

- hget:获取哈希表 key 中给定域 field 的值,返回值：field 域的值，如果 key 不存在或者 field 不存在返回 nil.

- hmget:取哈希表 key  中一个或多个给定域的值,返回和 field 顺序对应的值，如果 field 不存在，返回 nil.

- hgetall:获取哈希表 key  中所有的域和值,以列表形式返回 hash 中域和域的值 ，key 不存在，返回空 hash.

```sql
#hget key field
127.0.0.1:6379> hget user1 id
"1"
127.0.0.1:6379> hget user2 id
"2"
127.0.0.1:6379> hget user1 name
"zhangsan"
127.0.0.1:6379> hget user2 name
"lisi"
127.0.0.1:6379> hget user1 location
"beijing"
127.0.0.1:6379> hget user2 location
"shanghai"

#hmget key field[field...]
127.0.0.1:6379> hmget user1 id name location
1) "1"
2) "zhangsan"
3) "beijing"
127.0.0.1:6379> hmget user2 id name location
1) "2"
2) "lisi"
3) "shanghai"

#hgetall key
127.0.0.1:6379> hgetall user1
1) "id"
2) "1"
3) "name"
4) "zhangsan"
5) "location"
6) "beijing"
127.0.0.1:6379> hgetall user2
1) "id"
2) "2"
3) "name"
4) "lisi"
5) "location"
6) "shanghai"
```



#### 2.5.3 hdel

删除哈希表 key 中的一个或多个指定域 field，不存在 field 直接忽略返回值：成功删除的 field 的数量

```sql
#hdel key field[field...]
127.0.0.1:6379> hdel user2 name location
(integer) 2
127.0.0.1:6379> hgetall user2
1) "id"
2) "2"
127.0.0.1:6379> 
```



#### 2.5.4 hlen



```sql
#hlen key
127.0.0.1:6379> hlen user1
(integer) 3
127.0.0.1:6379> hlen user2
(integer) 1
```



#### 2.5.5 hexists

查看哈希表 key 中，给定域 field 是否存在返回值：如果 field 存在，返回 1， 其他返回 0

```sql
127.0.0.1:6379> hgetall user1
1) "id"
2) "1"
3) "name"
4) "zhangsan"
5) "location"
6) "beijing"
127.0.0.1:6379> hexists user1 name
(integer) 1
127.0.0.1:6379> hexists user1 email
(integer) 0
```



#### 2 5.6 hkeys/hvals

- hkeys:查看哈希表 key 中的所有 field 域,返回值：包含所有 field 的列表，key 不存在返回空列表.

- hvals:返回哈希表 中所有域的值返回值：包含哈希表所有域值的列表，key 不存在返回空列表.

```sql
#hkeys key
127.0.0.1:6379> hkeys user1
1) "id"
2) "name"
3) "location"

#hvals key
127.0.0.1:6379> hvals user1
1) "1"
2) "zhangsan"
3) "beijing"
127.0.0.1:6379> 
```



#### 2.5.7 hincrby

对指定hash表中指定field值进行加法运算。

```sql
#对象指定数据域做加法,返回值为加完后的值
#hincrby key field increment
127.0.0.1:6379> hincrby user1 id 5
(integer) 6
#对非数据字段做加法报错!
127.0.0.1:6379> hincrby user1 name 5
(error) ERR hash value is not an integer
#不能加一个负数
127.0.0.1:6379> hincrby user id -5
(integer) -5
127.0.0.1:6379> hincrby user1 name -5
(error) ERR hash value is not an integer
127.0.0.1:6379> hvals user1
1) "6"
2) "zhangsan"
3) "beijing"
```



#### 2.5.8 hincrbyfloat

对指定hash表中指定field值进行浮点数加法运算。

```sql
#hincrbyfloat key field floatvalue
127.0.0.1:6379> hincrbyfloat user1 id 0.99
"6.99"
127.0.0.1:6379> hvals user1
1) "6.99"
2) "zhangsan"
3) "beijing"
```



### 2.6 zset操作

zset(sorted set：有序集合)
Redis zset 和 set 一样也是string类型元素的集合,且不允许重复的成员。
不同的是每个元素都会关联一个double类型的分数。
redis正是通过分数来为集合中的成员进行从小到大的排序。zset的成员是唯一的,但分数(score)却可以重复。

redis 有序集合zset和集合set一样也是string类型元素的集合，且不允许重复的成员。

不同的是 zset 的每个元素都会关联一个分数（分数可以重复），redis 通过分数来为集合中的成员进行从小到大的排序。分数可以重复



#### 2.6.1 zadd

将一个或多个 member 元素及其 score 值加入到有序集合 key 中，如果 member存在集合中，则更新值；score 可以是整数或浮点数.

```sql
#zadd key score member [score member…]
127.0.0.1:6379> zadd zs1 10 zhangsan 20 lisi 99 xiaoming
(integer) 3
```



#### 2.6.2 zrem

删除有序集合 key 中的一个或多个成员，不存在的成员被忽略,返回值：被成功删除的成员数量，不包括被忽略的成员。

```sql
#zrem key member [member…]
127.0.0.1:6379> zrem zs1 lisi ll
(integer) 1
127.0.0.1:6379> zrange zs1 0 -1
1) "zhangsan"
2) "xiaoming"
```



#### 2.6.3 zcard

获取有序集 key 的元素成员的个数

返回值：key 存在返回集合元素的个数， key 不存在，返回 0

```sql
#zcard key
127.0.0.1:6379> zcard zs1
(integer) 3
```





#### 2.6.4 zrange

查询有序集合，指定区间的内的元素。集合成员按 score 值从小到大来排序。 start，stop 都是从 0 开始。0 是第一个元素，1 是第二个元素，依次类推。以 -1 表示最后一个成员，-2 表示倒数第二个成员。WITHSCORES 选项让 score 和 value 一同返回。返回值：自定区间的成员集合.

```sql
127.0.0.1:6379> zadd zs1 10 zhangsan 20 lisi 99 xiaoming
(integer) 3
#zrange key start end
127.0.0.1:6379> zrange zs1 0 -1
1) "zhangsan"
2) "lisi"
3) "xiaoming"
```





#### 2.6.5 zrevrange

返回有序集 key 中，指定区间内的成员。其中成员的位置按 score 值递减(从大到小) 来排列。其它同 zrange 命令。

```sql
127.0.0.1:6379> zrange zs1 0 -1
1) "zhangsan"
2) "lisi"
3) "xiaoming"

#zrevrange key start stop [WITHSCORES]
127.0.0.1:6379> zrevrange zs1 0 -1
1) "xiaoming"
2) "lisi"
3) "zhangsan"
```



#### 2.6.6 zrangebyscore

获取有序集 key 中，所有 score 值介于 min 和 max 之间（包括 min 和 max）的成员，有序成员是按递增（从小到大）排序。

min ,max 是包括在内 ， 使用符号 ( 表示不包括。 min ， max 可以使用 -inf ,+inf 表示最小和最大.

limit 用来限制返回结果的数量和区间。

withscores 显 示 score 和 value

返回值：指定区间的集合数据

```sql
127.0.0.1:6379> zadd zs 1 a 2 b 3 c 4 d 5 e 6 f
(integer) 6
127.0.0.1:6379> zrange zs 0 -1
1) "a"
2) "b"
3) "c"
4) "d"
5) "e"
6) "f"

#zrangebyscore key mix max
127.0.0.1:6379> zrangebyscore zs 1 3 
1) "a"
2) "b"
3) "c"

#zrangebyscore key mix max withscores
127.0.0.1:6379> zrangebyscore zs 1 3 withscores 
1) "a"
2) "1"
3) "b"
4) "2"
5) "c"
6) "3"

#zrangebyscore key (mix max withscores
127.0.0.1:6379> zrangebyscore zs (1 3 withscores 
1) "b"
2) "2"
3) "c"
4) "3"

#zrangebyscore key mix (max withscores
127.0.0.1:6379> zrangebyscore zs 1 (3 withscores 
1) "a"
2) "1"
3) "b"
4) "2"
```





#### 2.6.7 zrevrangebyscore

zrevrangebyscore key max min [WITHSCORES ] [LIMIT offset count]

返回有序集 key 中， score 值介于 max 和 min 之间(默认包括等于 max 或 min )的所有的成员。有序集成员按 score 值递减(从大到小)的次序排列。其他同 zrangebyscore.

```sql
127.0.0.1:6379> zrevrange zs 0 -1 
1) "f"
2) "e"
3) "d"
4) "c"
5) "b"
6) "a"
127.0.0.1:6379> zrevrange zs 0 -1 withscores
 1) "f"
 2) "6"
 3) "e"
 4) "5"
 5) "d"
 6) "4"
 7) "c"
 8) "3"
 9) "b"
10) "2"
11) "a"
12) "1"
127.0.0.1:6379> zrevrangebyscore zs 6 3 withscores 
1) "f"
2) "6"
3) "e"
4) "5"
5) "d"
6) "4"
7) "c"
8) "3"
127.0.0.1:6379> zrevrangebyscore zs (6 3 withscores 
1) "e"
2) "5"
3) "d"
4) "4"
5) "c"
6) "3"
127.0.0.1:6379> zrevrangebyscore zs 6 (3 withscores 
1) "f"
2) "6"
3) "e"
4) "5"
5) "d"
6) "4"
127.0.0.1:6379> zrevrangebyscore zs (6 (3 withscores 
1) "e"
2) "5"
3) "d"
4) "4"
```



#### 2.6.8zcount

zcount key min max

返回有序集 key 中， score 值在 min 和 max 之间(默认包括 score 值等于 min 或 max )的成员的数量

```sql
127.0.0.1:6379> zcount zs 1 4
(integer) 4
```





#### 2.6.9 zrank

zrank key member

在获取指定有序集合中指定元素的排名（排名从0开始）

```sql
127.0.0.1:6379> zrank zs a
(integer) 0
127.0.0.1:6379> zrank zs b
(integer) 1
127.0.0.1:6379> zrank zs c
(integer) 2
```



#### 2.6.10 zscore

zscore key member

获取指定有序集合中指定元素的分数

```sql
127.0.0.1:6379> zscore zs a
"1"
127.0.0.1:6379> zscore zs b
"2"
127.0.0.1:6379> zscore zs c
"3"
```



## redis配置文件

redis.conf 配置文件的安装位置: /opt/redis.xxx/redis.conf

```sql
[root@localhost redis-4.0.11]# ls
00-RELEASENOTES  CONTRIBUTING  deps      INSTALL   MANIFESTO  redis.conf  runtest-cluster   sentinel.conf  tests
BUGS             COPYING       dump.rdb  Makefile  README.md  runtest     runtest-sentinel  src            utils
[root@localhost redis-4.0.11]# pwd
/opt/redis-4.0.11
```



## redis持久化



## redis事务



## redis消息的发布与订阅



## redis主从复制



## redis哨兵



## jedis

