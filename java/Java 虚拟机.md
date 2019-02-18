
jstat

# JVM内存结构
## JVM运行态数据分区
//todo  
## 内存模型JMM
//todo

# GC垃圾回收算法


# 类加载机制
![类的生命周期](./pic/loadclass.png)
1. 装载(Load)：查找和加载Class(二进制)文件；
2. 链接(Link)：把类的二进制数据合并到JRE中；
        (a)校验：检查载入Class文件数据的正确性；
        (b)准备：给类的静态变量分配存储空间，并将其初始化为默认值；
        (c)解析：将类中的符号引用转成直接引用；
3. 初始化(Initialize)：对类的静态变量，静态代码块执行初始化操作
4. 使用
5. 卸载

## "双亲委派"机制介绍
JVM在加载类时默认采用的是双亲委派机制。通俗的讲，就是某个特定的类加载器在接到加载类的请求时，首先将加载任务委托给父加载器，依次递归，如果父加载器可以完成类加载任务，就成功返回；只有父加载器无法完成此加载任务时，才自己去加载。
![委托](./pic/delegation.png)
　　

* 启动类加载器：Bootstrap ClassLoader，负责加载存放在JDK\jre\lib(JDK代表JDK的安装目录，下同)下，或被-Xbootclasspath参数指定的路径中的，并且能被虚拟机识别的类库（如rt.jar，所有的java.*开头的类均被Bootstrap ClassLoader加载）。启动类加载器是无法被Java程序直接引用的。
* 扩展类加载器：Extension ClassLoader，该加载器由sun.misc.Launcher$ExtClassLoader实现，它负责加载DK\jre\lib\ext目录中，或者由java.ext.dirs系统变量指定的路径中的所有类库（如javax.*开头的类），开发者可以直接使用扩展类加载器。
* 应用程序类加载器：Application ClassLoader，该类加载器由sun.misc.Launcher$AppClassLoader来实现，它负责加载用户类路径（ClassPath）所指定的类，开发者可以直接使用该类加载器，如果应用程序中没有自定义过自己的类加载器，一般情况下这个就是程序中默认的类加载器。
* 用户自定义类加载器：User ClassLoader，属于应用程序根据自身需要自定义的ClassLoader，如tomcat、jboss都会根据j2ee规范自行实现ClassLoader(继承该类)，加载过程中会先检查类是否被已加载，检查顺序是自底向上，从User ClassLoader到BootStrap ClassLoader逐层检查，只要某个classloader已加载就视为已加载此类，保证此类在所有ClassLoader只加载一次。而加载的顺序是自顶向下，也就是由上层来逐层尝试加载此类。


# 服务响应慢的处理方式：
http://www.importnew.com/27436.html

一般来说，需要获取的信息大概分几类：
1. 服务表现：问题的具体表现（出错、超时等）、应用日志、依赖服务的状态等
2. 系统状态：操作系统指标（系统管理的各种资源的状态(端口连接数，服务连接数)、系统日志等）、jvm指标（主要是gc）
3. 硬件指标：cpu、内存、网络、硬盘是否达到瓶颈

# 虚拟机性能监控和故障处理
