#mybatis#
##代码逻辑##
                         配置或代码                  openSession()
SqlSessionFactoryBuidler -------> SqlSessionFactory ----------------> SqlSession
SqlSession可以直接通过API去执行一些crud，但我们通常不这么做
SqlSession.getMapper(Class clazz) 来获取影射器
然后通过影射器来进行一些方法调用

##生命周期##
SqlSessionFactoryBuidler得到SqlSessionFactory 就可以毁灭了
SqlSessionFactory类似于连接池，所以需要长久保存，保证单例存在
SqlSession  使用完毕就可以关闭
Mapper  周期小于SqlSession

##配置别名##
TypeAliasRegistry这个类中，配置了常用类和常用来在mybatis中的别名
Configuration 这个类中，同样配置了
当然我们还可以自定义别名

##类型转换##
typeHandler 主要是用来将jdbcType -- javaType 相互转换
系统可以自定义，实现这个接口 TypeHandler
可以在配置文件中针对某个result 来指定TypeHandler
通常typeHandler 用于支持枚举类型EnumOrdinalTypeHandler 和EnumTypeHandler 因为我们有的时候数据类型定义成枚举还很好用
针对文件存储的转换BlobTypeHandler，和ByteArrayTypeHandler

##对象工厂##
mybatis中生成结果集的时候哦，会通过一个对象工厂来完成创建这个结果集实例，也就是通过一个对象工厂产生对象，然后对对象进行构造成我们的结果集
DefaultObjectFactory
当然我们也可以自定义

##插件功能##

##运行环境##
配置事务管理器  Transaction       JdbcTransaction    ManageTransaction
 当然事务管理器也可以自定义
配置数据源       大部分我们会配合spring来进行这方面的操作
 PooledDataSourceFactory     UnpooledDataSourceFactory     JndiDataSourceFactory 三种配置
 数据库池，非数据库池，jndi
 当然也可以自定义数据源工厂        DBCP C3P0 Durid等等
 databaseIdProvider数据库厂商标示       在通过xml 中写sql 的指定能够方便移植，就是说改变我们的sql映射器
 当然这个也可以自定义，只需要实现DatabaseIdProvider接口即可
 
##影射器##
影射器就十分重要了，基本我们也是通过影射器来进行crud，
一个接口对应一个影射xml 或者注解
    select中，参数的传递就如同springMVC中请求参数的传递一样，可以封装成对象传递多个数据，因为最终数据类型是基础的
     map来影射参数，@Param来传递参数，大于5个时用JavaBean,也可以混合使用
    insert中
     useGeneratedKeys 设置主键回填，可以从库中拿到主键来被mybatis使用
特殊字符串的处理(#和$)
延迟加载，针对N+1问题，就是级联的时候，
    例如现在有N个关联关系完成了级联，那么只要在加入一个关联关系就变成了N+1个级联，导致，所有的级联SQL都被执行
  为了避免这种问题，我们需要延迟加载
缓存，mybatis中
  一级缓存是在SqlSession上的缓存，默认一级缓存是开启的，
  二级缓存是在SqlSessionFactory上的缓存，开启二级缓存实体bean 需要实现Serializable接口，就是不同的SqlSession获取同一条记录走的是一条
  二级缓存如何开启，在对应的映射xml中加入<cache/>
  实际环境中，我们都会配置redis 等缓存，那么我们就需要对cache来进行属性的添加了 自定义缓存类  
存储过程也是支持的

##动态sql##
可以通过某些语句，在不同的情况下生成不同的语句
几个关键字if chosse(when,otherwise) trim(where,set) foreach
bind关键字通过OGNL表达式自定义一个上下文变量，例如模糊查询的时候 传入的参数和%的拼接

##MyBatis的解析和运行原理##
两步走 读取配置文件，然后执行配置文件中的条目
 1，读取配置文件缓存到Configuration对象，用以创建SqlSessionFactory
    采用Builder模式创建SqlSessionFactory
      1，使用XMLConfigBuilder解析配置的xml文件，封装到Configuration
      2，使用Configuration对象去构建SqlSessionFactory
      构建影射器的内部主要是三个类
      MappedStatement  影射器节点，select|insert|update|delete
      SqlSource        提供BoundSql的地方 具体的几个实现，DynamicSqlSource，StaticSqlSource
      BoundSql         结果对象，就是SqlSource通过对SQL和参数的联合解析得到的SQL和参数
 2，SqlSession的执行过程
    获取Mapper，然后使用，这里getMapper 使用到了jdk的动态代理
    SqlSession下的四大对象
      Executor 执行器，执行下面Handler
      StatementHandler 使用数据库的Statement(PreparedStatement)来执行
      ParameterHandler 处理SQL参数的
      ResultSetHandler 对数据集(ResultSet)进行封装处理的
 

 
