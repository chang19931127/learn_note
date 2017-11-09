# Redis #
## Redis 概述 ##

   Redis是基于ANSI C语言编写，接近汇编语言的机器语言，运行十分快速，其次它是基于内存的读/写，速度自然比数据库的磁盘读/写要快的多，而且相比一般的NOSQL支持的数据类型又比较多，数据结构较为简单，规则较少，因此很受互联网的欢迎，并且Redis的操作都是原子操作
   
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

