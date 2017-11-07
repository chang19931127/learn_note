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
