#hsqldb用于单元测试#

在阅读pageHelper源码的过程中，发现pageHelper的源码对mybatis的mapper层进行了测试，而且改测试方式是方便移植的，不依赖于运行机器的环境，于是阅读pagehelper源码，并关注于ut与数据库之间的处理。

pagehelper使用HsqlDB代替实际数据库用于测试，这个配置在test.properties文件中。HsqlDB有内存运行模式，所有的数据都将在内存中完成，如果程序退出，则相应的数据也将同时被销毁。连接JDBC的实例为：jdbc:hsqldb:mem:dbname。pageHelper即是使用该连接方式，模拟与实际数据库的连接。

连接和制造数据的过程在MybatisHelper方法中，在静态代码块中，对sqlSessionFactory对象进行操作，读取配置文件，连接 HsqlDB内存数据库后，通过读取sql文件，先在内存数据库中建表。此后使用该sqlSessionFactory产生session并执行sql即可。

hsql的引入mvn配置：
```
<dependency>
<groupId>org.hsqldb</groupId>
<artifactId>hsqldb</artifactId>
<version>2.2.9</version>
<scope>test</scope>
</dependency>
```