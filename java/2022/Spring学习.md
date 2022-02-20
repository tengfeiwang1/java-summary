# Spring是什么？

## Spring的循环依赖
Spring支持构造方法注入和Setter方法注入，默认单例（Singleton）的场景是支持循环依赖的，不报错；但是原型（Prototype）的场景是不支持循环依赖的，会报错。

在使用构造方法注入时，会出现循环依赖：解决方法，通过Setter方法注入可以避免循环依赖产生

- 结论
Spring内部通过3级缓存来解决循环依赖--DefaultSingletonBeanRegistry
![Spring3级缓存](../pic/Spring3级缓存.png)

## Spring 的AOP
由代理实现

业务场景：事务，日志，安全验证

### AOP常见注解
@Before 前置通知：目标方法之前执行
@After 后置通知：目标方法之后执行（始终执行）
@AfterReturing 返回后通知：执行方法结束前执行（异常不执行）
@AfterThrowing 异常通知：出现异常时执行
@Around 环绕通知：环绕目标方法执行

- 注解执行顺序
Spring4----》Spring5 AOP执行顺序发生了变化,也就是SpringBoot1----》SpringBoot2

- Spring4 执行顺序
正常执行顺序：Before==>After==>AfterReturning
异常执行顺序: Before==>After==>AfterThrowing

- Spring5 执行顺序
正常执行顺序：Before==>AfterReturning==>After
异常执行顺序: Before==>AfterThrowing==>After

![SpringAOP执行顺序对比](../pic/SpringAOP执行顺序对比.png)

## Spring的IOC
IOC是个容器：控制反转，依赖注入
- IOC容器
实际上就是个Map，里面存的是各种对象，根据全限定类名使用反射创建对象放到map里。在代码里需要使用对象时，通过ID注入（autowired,resource等注解，会根据类型或者name（对象名）注入）。

- 控制反转
对象A获取依赖对象B的过程，由主动行为变成了被动行为，控制权颠倒过来了，这就是“控制反转”这个名称的由来。

- 依赖注入
“获得依赖对象的过程被反转了”。控制被反转之后，获得依赖对象的过程由自身管理变为了由IOC容器主动注入。依赖注入是实现IOC的方法，就是由IOC容器在运行期间，动态地将某种依赖关系注入到对象之中。


# Spring Boot

## SpringBoot注解及其实现
- 1. @SpringBootApplication注解：这个注解标识了⼀个SpringBoot⼯程，它实际上是另外三个注解的组
合，这三个注解是：
a. @SpringBootConfiguration：这个注解实际就是⼀个@Configuration，表示启动类也是⼀个配
置类
b. @EnableAutoConfiguration：向Spring容器中导⼊了⼀个Selector，⽤来加载ClassPath下
SpringFactories中所定义的⾃动配置类，将这些⾃动加载为配置Bean
c. @ComponentScan：标识扫描路径，因为默认是没有配置实际扫描路径，所以SpringBoot扫描的
路径是启动类所在的当前⽬录
- 2. @Bean注解：⽤来定义Bean，类似于XML中的<bean>标签，Spring在启动时，会对加了@Bean注解
的⽅法进⾏解析，将⽅法的名字做为beanName，并通过执⾏⽅法得到bean对象
- 3. @Controller、@Service、@ResponseBody、@Autowired等等



# Spring Cloud
Spring Cloud是⼀个微服务框架，提供了微服务领域中的很多功能组件，Dubbo⼀开始是⼀个RPC调⽤框
架，核⼼是解决服务调⽤间的问题，Spring Cloud是⼀个⼤⽽全的框架，Dubbo则更侧重于服务调⽤，所
以Dubbo所提供的功能没有Spring Cloud全⾯，但是Dubbo的服务调⽤性能⽐Spring Cloud⾼，不过
Spring Cloud和Dubbo并不是对⽴的，是可以结合起来⼀起使⽤的。