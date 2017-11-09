# Redis #
## Redis 概述 ##

   Redis是基于ANSI C语言编写，接近汇编语言的机器语言，运行十分快速，其次它是基于内存的读/写，速度自然比数据库的磁盘读/写要快的多，而且相比一般的NOSQL支持的数据类型又比较多，数据结构较为简单，规则较少，因此很受互联网的欢迎
   
   Redis在JavaWeb中一般两个使用场景，一个是缓存，另一个是需要高速读/写的场合
   
## Redis 和 Java结合 ##

这里有一点，就是Redis 数据结构比较单一，我们java中使用的都是对象，因此我们需要将对象序列化,org.springframework.data.redis.serializer序列化
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

 set key value 设置键值对

 get key 通过键获取值

 del key 通过key，删除键值对

 strlen 求key指向字符串的长度

 getset key value 修改原来的key，并返回旧值

 getrange key start end 获取子串

 append key value 将新的字符串value，加入到原来key指向的字符串末
- LIST(列表)
   
- SET(集合)
   
- HASH(哈希散列表)
   
- ZSET(有序集合)
   
- HyperLogLog(基数)

     作用是计算重复的值，已确定存储的数量，只提供基数的运算，不提供返回的功能

