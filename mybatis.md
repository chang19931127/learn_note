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

 
