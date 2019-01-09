# 获取资源文件 #

关于资源文件路径的获取，主要在于，以往的获取方式，在单元测试的时候，会失效，资源文件加载失败，导致不得不舍弃单元测试，才能编译通过。
在阅读pagehelper源码单元测试部分的时候，对资源文件的获取，进行了关注。
在TestUtil类中，采用了TestUtil.class.getResourceAsStream("/test.properties");。
getResourceAsStream方法，path 不以’/'开头时默认是从此类所在的包下取资源，以’/'开头则是从ClassPath根下获取。其只是通过path构造一个绝对路径，最终还是由ClassLoader获取资源。
ClassPath在maven 工程里面对应开发中的resource目录
这个地方理解还是不是很深入，需要写一个工程测一下，为什么需要那样设置

```
if(Constant.isDev){//开发环境
URLconf=App.class.getResource("/");//getProjectPath()+\\src\\main\\resources\\jryk.conf";
logger.info("readConfig获取绝对路径获取成功:"+conf.getPath());
path=conf.getPath();
}else{
logger.info("readConfig获取绝对路径获取成功:"+System.getProperty("user.dir"));
path=System.getProperty("user.dir");
}
```
这样设置的原因是，相关配置文件是一个目录下的所有文件，在被打入jar包以后，获取URL的方法就失效了，无法直接就jar包内的目录路径获取URL然后去遍历。所有已经使用的其他方法出现了问题。因此这里选择了user.dir。把配置文件放到外面。

注意，
1.配置文件只有jar包外面 的有效。所以相关配置文件不可缺少。
2.单元测试也可以做，相关配置文件在test目录下放置一份就可以了。
