# Redis #
## Redis 概述 ##

   Redis是基于ANSI C语言编写，接近汇编语言的机器语言，运行十分快速，其次它是基于内存的读/写，速度自然比数据库的磁盘读/写要快的多，而且相比一般的NOSQL支持的数据类型又比较多，数据结构较为简单，规则较少，因此很受互联网的欢迎，并且Redis的操作都是原子操作，[git地址](https://github.com/antirez/redis) 
   Redis在JavaWeb中一般两个使用场景，一个是缓存，另一个是需要高速读/写的场合
   
## Redis 和 Java结合 ##

这里有一点，就是Redis 数据结构比较单一，我们java中使用的都是对象，因此我们需要将对象序列化,org.springframework.data.redis.serializer序列化

RedisTemplate帮助我们封装了很多重复的代码
```xml
<bean id="redisTemplate" class="org.springframework.data.redis.core.RedisTemplate">
  <property name="connectionFactory" ref="connectionFactory"/>
  <property name="keySerializer" ref="X"/>
  <property name="valueSerializer" ref="X"/>
</bean>
```

代码就不再粘了，这里指出两个特例，都是配合Spring使用的

来自同一个Redis连接池的不同Redis连接,直接使用RedisTemplate
```java
Role role = new Role();
redisTemplate.opsForValue().set("role_1",role);
Role role1 = (Role)redisTemplate.opsForValue().get("role_1");
```

来自同一个Redis连接池的相同Redis连接，使用SessionCallback或者RedisCallback
```java
Role role = new Role();
SessionCallback<Role> callBack = new SessionCallback<>(){
  @Override
  public Role execute(RedisOperations ops) throws DataAccessException{
      ops.boundValueOps("role_1").set(role);
      return (Role)ops.boundValueOps("role_1").get();
  }
};
Role savedRole = (Role)redisTemplate.execute(callBack);
```

## Redis 的6种数据结构 ##

- STRING(字符串)

     基础的数据结构，它将一个key和一个value存储到Redis内部，类似java中的Map，通过key去找value，Redis字符串如果是数字，那么Redis还支持简单的运算，只支持简单的加减运算
     
- LIST(列表，链表)

     可以存储多个String，并且他们是有序的，如果内存足够大可以存储2的32次方-1 40 多亿，Redis链表是双向的
- SET(集合)

     Redis的集合的数据结构是，整数集，内部根据地址查找数据，或，hash表，内部会根据hash分子来存储和查找数据，特点是，每个元素不能重复，集合无序，每个元素都是String
- HASH(哈希散列表)

     Redis中的哈希就如同Java的map一样，一个对象里面有许多的键值对，特别适合存储对象的，如果内存足够大可以存储2的32次方-1 40 多亿。并且这一点很重要，hash是一个String类型的field和value的映射表，存储的数据实际上还是String，hash的键值对在内存中是一种无序的状态
- ZSET(有序集合)

     有序集合和集合类似，只是说它是有序的，数据结构是，压缩表(hash表)，搭配跳表，和无序集合的主要区别在于每一个元素除了值之外，还会多一个分数，这个分数在跳表结构中放着，分数是一个浮点数，根据分数，就可以排序了，和集合同样，数据不能重复，元素同样String 
- HyperLogLog(基数)

     作用是计算重复的值，已确定存储的数量，只提供基数的运算，不提供返回的功能
     
     概括的说，基数是一个算法，举例，一本英文著作由数百万个单词组成，而你的内存不足以存储他们，那么就需要针对业务分析，单词数量是有限的，并且数百万个单词有许多重复的，那么就扣除重复的单词，那么剩下的单词就不多了，那么内存就可以存储他们，例如数字集合{1,2,5,7,9,1,5,9}那么基数(不重复元素就是)5,基数的作用是评估大约需要准备多少个存储单元去存储数据，Redis2.8.9加入的
     
## Redis 基础事务 ##

     Redis有自己的事务，虽说没有关系型数据库事务能力那么强
     
     Redis事务是通过NULTI-EXEC的命令组合来实现的，事务是一个被隔离的操作，事务中的方法都会被Redis进行序列化并按顺序执行，事务在执行的过程中也不会被其他客户端的命令所打断，事务是一个原子性的操作，他要么全部执行，要么就什么都不执行。
     
     因为事务需要的是多个指令，并且存在于一个连接中，那么就要求的是开头所说的Spring中通过SessionCallback接口进行处理，那么Redis中使用事务的过程就是
- 使用watch命令(监控一个key)，看这个key在执行完事务的时候是否发生改变(CAS，也就是乐观锁)
- 使用mulit命令来开启事务，
- 命令进入队列，
- 使用exec命令来执行事务。

## Redis 流水线 ##
     
     由于Redis读/写操作很快，因此网络传递就是瓶颈，如果某一个传输了很多个命令来执行，传统情况就要等待下去，但由于流水线的存在，那么很快的区处理命令，流水线是一种通讯协议(书上看得),可以理解成数据库中的批处理把
    
     javaAPI
     
```java
     Jedis jedis = pool.getResource();
     Piepline pipeline = jedis.pipelined();
     for(int i = 0; i<10000 ;i++){
          int j = i+1;
          pipeline.set("pipeline_key_"+j,pipeline_value_"+j);
          pipeline.get("pipeline_key_"+j);
     }
     List result = pipeline.syncAndReturnAll();
```
     
     Spring RedisTemplate
     
```java
     SessionCallable callBack = (SessionCallback) (RedisOperations ops) -> {
          for(int i = 0 ; i < 10000 ; i++){
               int j = i + 1;
               ops.boundValueOps("pipeline_key_"+j).set("pipeline_value_"+j);
               ops.boundValueOps("pipeline_key_"+j);
          }
          return null;
     };
     List resultList = redisTemplate.executePipelined(callBack);
```

## Redis 发布订阅 ##

Redis 支持发布订阅模式

两点，要有发送的消息渠道，要有订阅者，订阅这个频道 Message Channel

```redis
SUBSCRIBE chat
publish char "let's go!"
那么subscribe chat 的客户端就会收到   我们走
```

## Redis 超时命令 ##

同样Redis支持超时命令，明白什么意思吧

## Redis可以执行Lua语言 ##

Redis除了自身命令外，还可以使用Lua语言来操作，单纯的redis命令并没有什么特别的计算能力，而是用Lua语言则在很大程度上弥补了Redis的这个不足

Redis中执行Lua语言是原子性的，也就是说执行Lua的是偶是不会被中断的，具有原子性，这个特性有助于Redis对并发数据一致性的支持

Redis支持两种方式运行Lua脚本，一种直接输入脚本，一种执行Lua语言的文件

对于直接输入的方式，Redis支持缓存脚本，只是他会使用SHA-1算法对脚本进行签名，然后把SHA-1标识返还回来，只要通过这个标识就可以运行了
```lur
eval 代表执行Lua语言的命令
然后跟上Lur语言脚本
然后跟上key 的个数，从1开始，0代表没有
然后跟上value ，这个可填可不填
eval "redis.call('set',KYES[1],ARGV[1])" 1 lua-key lua-value 
这样key，value就被存入到redis中了
get lua-key 就可以得到 lue-value
语法就是redis.call(command,key[param1,parap2...])

由于Redis具有缓存脚本的功能，所以一般使用的时候都缓存了
首先使用
script load (script)
会返回给你一个SHA-1
然后
evalsha shastring 参数就可以了，
当然这样的api方式java也可以用的
```

## Redis 基础配置文件

作为一个软件，必不可少的就是存在一些配置文件，我们使用者可以通过配置文件来更改软件给我们提供的功能，同样Redis也给我们提供了配置文件，文件就是redis.window.conf(Windows系统下)，redis.conf(Linux系统下)

## Redis 备份(持久化)

Redis是一个内存的数据库，那么断电了，不就会造成很大的影响，因此Redis提供了备份，两种备份
- 快照(snapshotting)，他是备份当前瞬间Redis在内存中的数据记录
- 只追加文件(Append-Only File,AOF),其作用就是当Redis执行写命令后，在一定的条件下执行过的写命令依次保存在Redis的文件中

Redis可以任意使用这两种，或者其中一种，或者都不用,在Redis的配置文件中可以进行配置

redis.conf
```
#当900秒执行一个写命令，启动快照备份
save 900 1
#当300秒执行10个写命令，启动快照备份
save 300 10
#当60秒执行10000个写命令，启动快照备份
save 60 10000
#bgsave异步保存命令，另外启动一条线程，save是阻塞的，为什么出现这个是因为Redis执行save命令的时候禁止写入命令，解决冲突么，我作你不作
stop-writes-on-bgsave-error yes
#对rdb文件进行检验，rdb文件就是快照备份文件
rdbchecksum yes
#指名rdb数据持久化文件
dbfilename dump.rdb
#是否启动AOF方法进行持久化
appendonly no
#持久化文件
appendfilename "appendonly.aof"
#同步追加，配置频率always，everysec，no
appendfsync always
#是否在后台AOF文件rewrite期间调用fsync
no-appendfsync-on-rewrite no
#指定Redis重写AOF文件的条件，默认是100，表示与上次rewrite的AOF文件大小相比较,百分比
auto-aof-rewrite-percentage 100
#触发rewrite的AOF文件大小，如果小于该值，不管上面百分比怎么设置，也会触发rewrite
auto-aof-rewrite-min-size
```

## Redis 内存不足的回收策略
- volatile-lru:采用最近使用最少的淘汰策略，Redis将回收那些超时的键值对，使用lru
- allkeys-lru:采用淘汰最近最少使用的策略，Redis将针对所有键值对进行lru
- volatile-random:采用随机的淘汰策略，Redis将回收那些超时的键值对，使用random
- allkeys-random:采用随机的淘汰策略，Redis将针对所有键值对进行，random
- volatile-ttl:采用删除存活时间最短的键值对策略
- noeviction:不进行回收，如果内存满了，就报错    默认行为

上面的算法都不是很绝对，为了性能，就是一个大致的回收效果，

## Redis 主从同步(复制)

数据量不断的变大，对Redis的请求数量变多，那么单机Redis肯定不够用，就要考虑分布式Redis集群了，常用的就是，redis自己提供了分布式方案，也可以使用Codis集群方案，Twemproxy方案 [codis豌豆荚开源](https://github.com/CodisLabs/codis/)

还是多了解下分布式的原理，这样肯定有好处

集群方案我们并不能保证单点的可用性.只能保证一个节点挂了，使用其他的补上

但是针对某一个redis服务，如何保证可用性，Redis给我们提供了哨兵模式(Sentinel)

主从同步，通常有这样的感觉，就是说，一主多从，写用于主，读用于从，这样主改变了，复制到从中，就可以了，又是又可以多主多从，反正分布式很大很奇妙

使用哨兵(Sentinel)模式---仅仅提供的还是redis单机服务，只是提高了单机服务的健壮性

哨兵来帮助我们检测master是否宕机，可以通过配置文件来进行修改，哨兵是作为一个单独的进程执行的，先启动哨兵，在启动redis服务器进程

哨兵提供了一套Redis哨兵的API来进行操作的，也就是说我们可以通过编程来使用哨兵，貌似这里使用哨兵，只是配置哨兵的属性，哨兵的功能还是redis来进行操作的
