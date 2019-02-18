todo  补上基础知识
https://github.com/Snailclimb/JavaGuide/blob/master/Java%E7%9B%B8%E5%85%B3/Java%E5%9F%BA%E7%A1%80%E7%9F%A5%E8%AF%86.md
# 一 基本类型
## 缓存池
new Integer(123) 与 Integer.valueOf(123) 的区别在于：
new Integer(123) 每次都会新建一个对象；
Integer.valueOf(123) 会使用缓存池中的对象，多次调用会取得同一个对象的引用。
在 Java 8 中，Integer 缓存池的大小默认为 -128~127。
```java
public static Integer valueOf(int i) {
    if (i >= IntegerCache.low && i <= IntegerCache.high)
        return IntegerCache.cache[i + (-IntegerCache.low)];
    return new Integer(i);
}
```
* tips:
基本类型对应的缓冲池如下：
boolean values true and false
all byte values
short values between -128 and 127
int values between -128 and 127
char in the range \u0000 to \u007F

# Java关键字
## final

## static
## this
## super

# 反射
>> Reflection:Java反射机制是在运行状态中，对于任意一个类，都能够知道这个类的所有属性和方法；对于任意一个对象，都能够调用它的任意一个方法和属性；这种动态获取的信息以及动态调用对象的方法的功能称为Java语言的反射机制。
* 反射机制获取类有三种方法 
1. 直接通过类名.class的方式得到(一个类只会被反射出来一次)
Class clazz = Person.class;
2. 通过对象的getClass()方法获取(较少使用)
Object obj = new Person();
Class clazz = obj.getClass();
3. 通过全类名获取，用的就较多
Class clazz = Class.forName("com.test.Person");

>> 关于Class
1. Class是一个类，一个描述类的类（也就是描述类本身），封装了描述方法的Method，描述字段的Filed，描述构造器的Constructor等属性
2. 对象照镜子后（反射）可以得到的信息：某个类的数据成员名、方法和构造器、某个类到底实现了哪些接口。
3. 对于每个类而言，JRE 都为其保留一个不变的 Class 类型的对象。 一个 Class 对象包含了特定某个类的有关信息。
4. Class 对象只能由系统建立对象
5. **一个类在 JVM 中只会有一个Class实例**

# 代理
>> 代理(Proxy)是一种设计模式,提供了对目标对象另外的访问方式;即通过代理对象访问目标对象.这样做的好处是:可以在目标对象实现的基础上,增强额外的功能操作,即扩展目标对象的功能.
>>> 代理模式的关键点是:代理对象与目标对象.代理对象是对目标对象的扩展,并会调用目标对象

[代理模式参考链接](https://www.cnblogs.com/cenyu/p/6289209.html)

## 静态代理
* 静态代理在使用时,*需要定义接口或者父类*,被代理对象与代理对象一起实现相同的接口或者是继承相同父类.

* 静态代理总结:
1.可以做到在不修改目标对象的功能前提下,对目标功能扩展.
2.缺点:因为代理对象需要与目标对象实现一样的接口,所以会有很多代理类,类太多.
同时,一旦接口增加方法,目标对象与代理对象都要维护.

## 动态代理---基于jvm反射实现
* 动态代理有以下特点:
1. 代理对象,不需要实现接口
2. 代理对象的生成,是利用JDK的API,动态的在内存中构建代理对象(需要我们指定创建代理对象/目标对象实现的接口的类型)
3. 动态代理也叫做:JDK代理,接口代理

* 代理类所在包:java.lang.reflect.Proxy
JDK实现代理只需要使用newProxyInstance方法,但是该方法需要接收三个参数,完整的写法是:
 ``` java
 static Object newProxyInstance(ClassLoader loader, Class<?>[] interfaces,InvocationHandler h )
 ```
 >> 注意该方法是在Proxy类中是静态方法,且接收的三个参数依次为:
>>> ClassLoader loader,:指定当前目标对象使用类加载器,获取加载器的方法是固定的
>>> Class<?>[] interfaces,:目标对象实现的接口的类型,使用泛型方式确认类型
>>> InvocationHandler h:事件处理,执行目标对象的方法时,会触发事件处理器的方法,会把当前执行目标对象的方法作为参数传入

* 总结:
代理对象不需要实现接口,但是目标对象一定要实现接口,否则不能用动态代理

## Cglib代理--实现原理：FastClass对Class对象进行特别的处理，比如将会用数组保存method的引用，每次调用方法的时候都是通过一个index下标来保持对方法的引用。
上面的静态代理和动态代理模式都是*要求目标对象是实现一个接口的目标对象*,但是有时候目标对象只是一个单独的对象,并没有实现任何的接口,这个时候就可以使用以目标对象子类的方式类实现代理,这种方法就叫做:Cglib代理

>> Cglib代理,也**叫作子类代理**,它是在内存中构建一个子类对象从而实现对目标对象功能的扩展.
* JDK的动态代理有一个限制,就是使用动态代理的对象必须实现一个或多个接口,如果想代理没有实现接口的类,就可以使用Cglib实现.
* Cglib是一个强大的高性能的代码生成包,它可以在运行期扩展java类与实现java接口.它广泛的被许多AOP的框架使用,例如Spring AOP和synaop,为他们提供方法的interception(拦截)
* Cglib包的底层是通过使用一个小而快的字节码处理框架ASM来转换字节码并生成新的类.不鼓励直接使用ASM,因为它要求你必须对JVM内部结构包括class文件的格式和指令集都很熟悉.

>> Cglib子类代理实现方法:
1. 需要引入cglib的jar文件,但是Spring的核心包中已经包括了Cglib功能,所以直接引入spring-core-3.2.5.jar即可.
2. 引入功能包后,就可以在内存中动态构建子类
3. **代理的类不能为final,否则报错**(final类 不能继承)
4. 目标对象的方法如果为final/static,那么就不会被拦截,即不会执行目标对象额外的业务方法.


# 一致性Hash
对于待存储的海量数据，如何将它们分配到各个机器中去----数据分片与路由

一致性hash特性
1. 平衡性(Balance)
2. 单调性(Monotonicity)：单调性是指如果已经有一些内容通过哈希分派到了相应的缓冲中，又有新的缓冲加入到系统中。哈希的结果应能够保证原有已分配的内容可以被映射到原有的或者新的缓冲中去，而不会被映射到旧的缓冲集合中的其他缓冲区。 
3. 分散性 (Spread)
4. 负载性 (Load) 
5. 平滑性(Smoothness)

原理分析：https://www.cnblogs.com/lpfuture/p/5796398.html

# 多态

https://blog.csdn.net/sinat_34311901/article/details/52208124