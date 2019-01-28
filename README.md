# Redis
###  Redis
    
    Redis是一个开源的，C语言编写、遵守BSD协议、支持网络、可基于内存亦的日志型、Key-Value数据库，并提供多种语言的API

    通常被称为数据结构服务器，值可以是字符串, 哈希，列表, 集合和有序集合等类型

    Redis 与其他 key - value 缓存产品有以下三个特点：
        Redis支持数据的持久化，可以将内存中的数据保存在磁盘中，重启的时候可以再次加载进行使用。
        Redis不仅仅支持简单的key-value类型的数据，同时还提供list，set，zset，hash等数据结构的存储。
        Redis支持数据的备份，即 master-slave 模式的数据备份。

1. 安装
1-1）Windows：
    下载地址：https://github.com/MSOpenTech/redis/releases

    打开一个 cmd 窗口 使用 cd 命令切换目录到 C:\redis 运行
    redis-server.exe redis.windows.conf

    或配置环境变量
    redis-server.exe

    开启客户端，另启一个 cmd 窗口，原来的不要关闭，不然就无法访问服务端
    redis-cli.exe -h 127.0.0.1 -p 6379

    设置键值对
    set myKey aaa

    取出键值对
    get myKey


1-2）Linux 
    下载地址：http://redis.io/download，下载最新稳定版本
    $ wget http://download.redis.io/release/redis-2.8.17.tar.gz
    $ tar xzf redis-2.8.17.tar.gz
    $ cd redis-2.8.17
    $ make (创建客户端和服务器程序安装目录src下)

    启动redis-server
     cd src
     ./redis-server  使用默认配置启动redis
     ./redis-server ../redis.conf  指定配置启动

    启动redis-cli
    cd src
    ./redis-cli

    测试
    redis> set foo bar
    redis> get foo

1-3）Ubuntu 
    $sudo apt-get update
    $sudo apt-get install redis-server

    启动 Redis
    $ redis-server
    $ redis-cli

    redis 127.0.0.1:6379> ping
    PONG   成功


    redis.conf 配置详情看文档
    1-3-1）配置 redis 外网可访问
        修改 redis 的配置文件，将所有 bind 信息全部屏蔽
        # bind 192.168.1.100 10.0.0.1 
        # bind 192.168.1.8 
        # bind 127.0.0.1

        修改完成后，需要重新启动 redis 服务
        修改 Linux 的防火墙(iptables)，开启你的 redis 服务端口，默认是 6379
        -A INPUT -m state –state NEW -m tcp -p tcp –dport 6379 -j ACCEPT 
        …… 
        -A INPUT -j REJECT –reject-with icmp-host-prohibited
        一定要将 redis 的防火墙配置放在 REJECT 的前面。然后执行 service iptables restart

     


2. Redis 数据类型及命令
    string（字符串），hash（哈希），list（列表），set（集合）及zset(sorted set：有序集合)

    string 一个键最大能存储512MB
    应用场景：
        ---
    redis 127.0.0.1:6379> SET name "666"
    redis 127.0.0.1:6379> GET name "666"


    hash 特别适合用于存储对象
    每个 hash 可以存储 2(32次方) -1 键值对（40多亿）
    应用场景：
        存储，读取，修改用户属性
    redis> HMSET myhash field1 "Hello" field2 "World"
    redis> HGET myhash field1
    redis> HGET myhash field2


    List 简单的字符串列表，按照插入顺序排序
    列表最多可存储 2(32次方) - 1 元素 (每个列表可存储40多亿)
    应用场景：
        最新消息排行等功能(比如朋友圈的时间线)  
        消息队列
    redis 127.0.0.1:6379> lpush python redis
    (integer) 1     成功返回1


    set 是string类型的无序集合
    集合是通过哈希表实现的，所以添加，删除，查找的复杂度都是O(1)
    应用场景：
        共同好友 
        利用唯一性,统计访问网站的所有独立ip 
        好友推荐时,根据tag求交集,大于某个阈值就可以推荐
    redis 127.0.0.1:6379> sadd python redis
    (integer) 1
    redis 127.0.0.1:6379> smembers python


     zset 和 set 一样也是string类型元素的集合,且不允许重复的成员
     应用场景：
        排行榜
        带权重的消息队列
     redis 127.0.0.1:6379> zadd python 0 redis
     redis 127.0.0.1:6379> > ZRANGEBYSCORE python 0 1000


    Redis支持多个数据库，并且每个数据库的数据是隔离的不能共享
    并且基于单机才有，如果是集群就没有数据库的概念
    使用SELECT命令更换数据库
    redis> SELECT 1
    redis [1] > GET foo



2-1）Redis 命令
    在远程服务上执行命令
    $ redis-cli -h host -p port -a password
    $redis-cli -h 127.0.0.1 -p 6379 -a "mypasswd"
    redis 127.0.0.1:6379>
    redis 127.0.0.1:6379> PING


    避免中文乱码
    redis-cli --raw
    

2-3）Redis 键(key)
    redis 127.0.0.1:6379> SET pythonkey redis
    redis 127.0.0.1:6379> DEL pythonkey 

    key相关命令：
    dump key            序列化给定 key ，并返回被序列化的值
    exits key           检查给定 key 是否存在
    expire key          为给定 key 设置过期时间，以秒second计
    keys pattern        查找所有符合给定模式( pattern)的 key
    MOVE key db         将当前数据库的 key 移动到给定的数据库 db 当中
    PERSIST key         移除 key 的过期时间，key 将持久保持



2-4）Redis 字符串
    用于管理 redis 字符串值
    set python redis
    get python redis

    string 相关命令
    INCR key            将 key 中储存的数字值增一
    DECR key            将 key 中储存的数字值减一
    GETSET key value    将给定 key 的值设为 value ，并返回 key 的旧值
    GETBIT key offset   对 key 所储存的字符串值，获取指定偏移量上的位(bit)




2-5）Redis 哈希
    是一个string类型的field和value的映射表，hash特别适合用于存储对象
    hset pythonkey redis name "PP" description "redis basic commands"
    likes 10 vistors 20000
    hgetall pythonkey

    hash 相关命令
    HGET key field      获取在哈希表中指定 key 的所有字段和值
    HKEYS key           获取所有哈希表中的字段
    HVALS key           获取哈希表中所有值
    HSCAN key cursor [MATCH pattern] [COUNT count] 迭代哈希表中的键值对



2-6）Redis 列表
    简单的字符串列表，按照插入顺序排序。你可以添加一个元素到列表的头部（左边）或者尾部（右边）

    lpush pythonkey redis
    lrange pythonkey 0 10

    list 相关命令

    移出并获取列表的第一个元素
    如果列表没有元素会阻塞列表直到等待超时或发现可弹出元素为止
    blpop key1 [key2 ] timeout     / brpop 最后

    通过索引获取列表中的元素
    lindex key index 

    在列表的元素前或者后插入元素
    linsert key before | after pivot value

    llen key
    获取列表长度

    移除列表元素
    lrem key count value

    通过索引设置列表元素的值
    lset key index value

    为已存在的列表添加值
    rpushx key value

    在列表中添加一个或多个值
    rpush key value1 [vlue_n]




2-7） Redis 集合
    Set 是 String 类型的无序集合,集合成员是唯一的
    通过哈希表实现的，所以添加，删除，查找的复杂度都是 O(1)

    set 相关命令
    SADD key member1 [member2]      向集合添加一个或多个成员
    SCARD key                       获取集合的成员数
    SDIFF key1 [key2]               返回给定所有集合的差集
    SDIFFSTORE des key1 [key2]      返回给定所有集合差集并存储在 des 中
    SINTER key1 [key2]              返回给定所有集合的交集
    SISMEMBER key member            判断 member 元素是否是集合 key 的成员
    SPOP key                        移除并返回集合中的一个随机元素
    SUNION key1 [key2]              返回所有给定集合的并集
    sscan key cursor [match pattern] [COUNT count] 迭代集合中的元素

2-8）Redis 有序集合
    有序集合和集合一样也是string类型元素的集合,且不允许重复的成员
    sorted set相关命令
    ZCARD key                       获取有序集合的成员数
    ZCOUNT key min max              计算在有序集合中指定区间分数的成员数
    ZSCORE key member               返回有序集中，成员的分数值
    ZREVRANK key member             返回有序集合中指定成员的排名
    指定区间内的成员，通过索引，分数从高到底
    ZREVRANGE key start stop [WITHSCORES] 

    当items内容大于64的时候同时使用了hash和skiplist两种设计实现：
        如果添加和删除都需要修改skiplist，复杂度为O(log(n))
        仅仅是查找元素的话可以直接使用hash，其复杂度为O(1)
        range操作复杂度一般为O(log(n))
        如果是小于64的时候，因为是采用了ziplist的设计，其时间复杂度为O(n)

    

2-9）Redis HyperLogLog
    用来做基数统计的算法，在输入元素的数量或者体积非常非常大时，计算基数所需的空间总是固定的、并且是很小的
    单个占 12 KB 内存，就可以计算接近 2^64 个不同元素的基数

    什么是基数?
    比如数据集 {1, 3, 5, 7, 5, 7, 8}
    那么这个数据集的基数集为 {1, 3, 5 ,7, 8}
    基数(不重复元素)为5，基数估计就是在误差可接受的范围内，快速计算基数

    pfadd pythonkey "mysql"
    pfadd pythonkey "redis"
    pfadd pythonkey "mongodb"
    rfcount pythonkey
    > 3

    hyperloglog 相关命令
    pfadd key element [element_n]       添加指定元素到 HyperLogLog 中
    PFCOUNT key [key ...]               返回给定 HyperLogLog 的基数估算值
    PFMERGE destkey sourcekey [sourcekey ...] 
    将多个 HyperLogLog 合并为一个 HyperLogLog




3.  Redis 通信
    Redis 发布订阅(pub/sub)是一种消息通信模式：
    发送者(pub)发送消息，订阅者(sub)接收消息

    Redis 客户端可以订阅任意数量的频道

    工作流程：
    创建订阅频道名为 redisChat
    SUBSCRIBE redisChat

    重新开启个 redis 客户端
    然后在同一个频道 redisChat 发布两次消息，订阅者就能接收到消息
    PUBLISH redisChat "Redis is a great caching technique"
    PUBLISH redisChat "Learn redis "

    # 订阅者的客户端会显示如下消息
    1) "message"
    2) "redisChat"
    3) "Redis is a great caching technique"
    1) "message"
    2) "redisChat"
    3) "Learn redis "



4.  Redis 事务
    Redis 事务可以一次执行多个命令， 并且带有以下两个重要的保证：
    批量操作在发送 EXEC 命令前被放入队列缓存
    收到 EXEC 命令后进入事务执行，事务中任意命令执行失败，其余命令依然被执行
    在事务执行过程，其他客户端提交的命令请求不会插入到事务执行命令序列中

    一个事务从开始到执行会经历以下三个阶段：
    开始事务
    命令入队
    执行事务

    事务相关命令
    DISCARD                 取消事务，放弃执行事务块内的所有命令
    EXEC                    执行所有事务块内的命令
    MULTI                   标记一个事务块的开始
    UNWATCH                 取消 WATCH 命令对所有 key 的监视
    WATCH key [key ...]     监视一个(或多个) key
                            如果在事务执行之前 key 被其他命令所改动，那么事务将被打断


5. Redis 脚本
    Redis 脚本使用 Lua 解释器来执行脚本
    执行脚本的常用命令为 EVAL

    redis 脚本工作过程
    redis 127.0.0.1:6379> EVAL "return {KEYS[1],KEYS[2],ARGV[1],ARGV[2]}" 2 key1 key2 first second

    1) "key1"
    2) "key2"
    3) "first"
    4) "second"
    
    redis 脚本常用命令
    执行 Lua 脚本
    EVAL script numkeys key [key ...] arg [arg ...] 

    查看指定的脚本是否已经被保存在缓存当中
    SCRIPT EXISTS script [script ...] 

    从脚本缓存中移除所有脚本
    SCRIPT FLUSH 

    杀死当前正在运行的 Lua 脚本
    SCRIPT KILL 

    将脚本 script 添加到脚本缓存中，但并不立即执行这个脚本
    SCRIPT LOAD script 



    Redis 连接命令
    AUTH password               验证密码是否正确
    ECHO message                打印字符串
    PING                        查看服务是否运行
	QUIT                        关闭当前连接
    SELECT index                切换到指定的数据库



    获取 redis 服务器的统计信息
    redis 127.0.0.1:6379> INFO

    redis 服务器的相关命令
    bgrewriteaof            异步执行一个 AOF（AppendOnly File）文件重写操作
    bgsave                  在后台异步保存当前数据库的数据到磁盘
    client kill [ip:port]   [ID client_id]     关闭客户端连接
    client list             获取连接到服务器的客户端连接列表
    client getname          获取连接的名称
    client pause timeout    在指定时间内终止运行来自客户端的命令
    INFO [section]          获取 Redis 服务器的各种信息和统计数值
    LASTSAVE                返回最近一次 Redis 成功将数据保存到磁盘上的时间
    MONITOR                 实时打印出 Redis 服务器接收到的命令，调试用
    SAVE                    同步保存数据到硬盘
    SHUTDOWN [NOSAVE] [SAVE]异步保存数据到硬盘，并关闭服务器
    SLOWLOG subcommand [argument] 管理 redis 的慢日志
    SYNC                    用于复制功能(replication)的内部命令



6. Redis 数据备份与恢复
    SAVE 命令用于创建当前数据库的备份
    redis 127.0.0.1:6379> SAVE 

    恢复数据
    如果需要恢复数据，只需将备份文件 (dump.rdb) 移动到 redis 安装目录并启动服务即可
    获取 redis 目录可以使用 CONFIG 命令
    redis 127.0.0.1:6379> CONFIG GET dir
    1) "dir"
    2) "/usr/local/redis/bin"

    创建 redis 备份文件使用命令 BGSAVE，该命令在后台执行
    127.0.0.1:6379> BGSAVE

    Background saving started


    安全
    查看是否设置了密码验证
    127.0.0.1:6379> CONFIG get requirepass
    1) "requirepass"
    2) ""

    修改该参数
    127.0.0.1:6379> CONFIG set requirepass "python"
    127.0.0.1:6379> CONFIG get requirepass
    1) "requirepass"
    2) "python"


    127.0.0.1:6379> AUTH "lee"
    127.0.0.1:6379> SET mykey "Test value"
    127.0.0.1:6379> GET mykey
    "Test value"




7.  Redis 性能测试
    Redis 性能测试是通过同时执行多个命令实现的
    redis-benchmark [option] [option value]

    同时执行 10000 个请求来检测性能
    $ redis-benchmark -n 10000  -q
    MSET (10 keys): 93283.58 requests per second

    redis 性能测试工具可选参数:

    -h	        指定服务器主机名	            127.0.0.1
    -p	        指定服务器端口	                6379
    -s	        指定服务器                      socket	
    -c	        指定并发连接数	                50
    -n	        指定请求数	                    10000
    -d	        以字节的形式指定 SET/GET 值的数据大小	2
    -k	        1=keep alive 0=reconnect	            1
    -r	        SET/GET/INCR 使用随机 key, SADD 使用随机值	
    -P	        通过管道传输 <numreq> 请求	            1
    -q	        强制退出 redis,仅显示 query/sec 值	
    --csv	    以 CSV 格式输出	
    -l	        生成循环，永久执行测试	
    -t	        仅运行以逗号分隔的测试命令列表	
    -I	        Idle 模式,仅打开 N 个 idle 连接并等待



    使用了多个参数来测试 redis 性能
    主机为 127.0.0.1，端口号为 6379，执行的命令为 set,lpush，请求数为 10000，通过 -q 参数让结果只显示每秒执行的请求数

    $ redis-benchmark -h 127.0.0.1 -p 6379 -t set,lpush -n 10000 -q

    SET: 146198.83 requests per second
    LPUSH: 145560.41 requests per second


    Redis 客户端连接
    通过监听一个 TCP 端口或者 Unix socket 的方式来接收来自客户端的连接

    最大连接数
    config get maxclients

    设置最大连接数为 100000
    redis-server --maxclients 100000

    客户端命令
    CLIENT LIST	            返回连接到 redis 服务的客户端列表
	CLIENT SETNAME	        设置当前连接的名称
	CLIENT GETNAME	        获取通过 CLIENT SETNAME 命令设置的服务名称
	CLIENT PAUSE	        挂起客户端连接，指定挂起的时间以毫秒计
	CLIENT KILL	            关闭客户端连接



    Redis 管道技术
    Redis是一种基于客户端-服务端模型以及请求/响应协议的TCP服务

    Redis 管道技术可以在服务端未响应时
    客户端可以继续向服务端发送请求，并最终一次性读取所有服务端的响应

    查看 redis 管道，只需要启动 redis
    $(echo -en "PING\r\n 
        SET pythonkey redis\r\n 
        GET pythonkey\r\n 
        INCR visitor\r\n 
        INCR visitor\r\n 
        INCR visitor\r\n"; 
        sleep 10) | nc localhost 6379

    管道技术最显著的优势是提高了 redis 服务的性能

    测试管道技术对速度的提升效果,Ruby客户端
    开启管道后，我们的速度效率提升了5倍



8.  Redis 分区
    分区是分割数据到多个Redis实例的处理过程
    因此每个实例只保存key的一个子集

    分区的优势:
    通过利用多台计算机内存的和值，允许我们构造更大的数据库
    通过多核和多台计算机，允许我们扩展计算能力
    通过多台计算机和网络适配器，允许我们扩展网络带宽

    redis的一些特性在分区方面表现的不是很好:
    涉及多个key的操作通常是不被支持的
    涉及多个key的redis事务不能使用
    当使用分区时，数据处理较为复杂
    增加或删除容量也比较复杂,presharding的技术对此是有帮助


    分区类型
        范围分区
        哈希分区
        
