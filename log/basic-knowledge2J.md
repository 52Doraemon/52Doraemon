#### 1、在Java中，对于不再使用的内存资源，如调用完成的方法，“垃圾回收器”会自动将其释放。（错）

方法调用时，会创建栈帧在栈中，调用完是程序自动出栈释放，而不是gc释放；Java 的垃圾回收器（GC）主要针对堆区，所以在栈区中的方法调用就不会由GC释放。 JVM 内存可简单分为三个区： 
 
**1、堆区（heap）**：用于存放所有对象，是线程共享的（注：数组也属于对象） 
 
**2、栈区（stack）**：用于存放基本数据类型的数据和对象的引用，是线程私有的（分为：虚拟机栈和本地方法栈） 
 
**3、方法区（method）**：用于存放类信息、常量、静态变量、编译后的字节码等，是线程共享的（也被称为非堆，即 None-Heap） 
 
**Java 的垃圾回收器（GC）主要针对堆区**

#### 2、A instanceOf B

> A instanceOf B，是判断对象A是否属于B或B的子类，子接口实现类，实现类的实例。任何数据类型都可以赋值给Object对象。

#### 3、java 类加载器
* 引导类加载器(bootstrap class loader):它用来加载 Java 的核心库,是用原生代码（如果说是C++就错了）来实现的

> 引导类加载器(bootstrap class loader):它用来加载 Java 的核心库,是用原生代码来实现的

> 注：元题目并没有指定是Ｃ＋＋，而是说原生代码实现。
> 虚拟机上说Hotspot是由c++实现的，想MRP、Maxine等虚拟机，整个虚拟机本身都是由java编写的，自然Bootstrap ClassLoader是由java语言实现的    

* 扩展类加载器(extensions class loader):它用来加载 Java 的扩展库。

* 系统类加载器(system class loader):它根据 Java 应用的类路径(CLASSPATH)来加载 Java 类

* tomcat 为每个 App 创建一个 Loader,里面保存着此 WebApp 的 ClassLoader。需要加载 WebApp 下的类时,就取出 ClassLoader 来使用

	1）Bootstrap ClassLoader：负责加载$JAVA_HOME中jre/lib/rt.jar里所有的class，由C++实现，不是ClassLoader子类
  
	2）Extension ClassLoader：负责加载java平台中扩展功能的一些jar包，包括$JAVA_HOME中jre/lib/*.jar或-Djava.ext.dirs指定目录下的jar包

  3）App ClassLoader：负责记载classpath中指定的jar包及目录中class

  4）Custom ClassLoader：属于应用程序根据自身需要自定义的ClassLoader，如tomcat、jboss都会根据j2ee规范自行实现ClassLoader

	加载过程中会先检查类是否被已加载，检查顺序是自底向上，从Custom ClassLoader到BootStrap ClassLoader逐层检查，只要某个classloader已加载就视为已加载此类，保证此类只所有ClassLoader加载一次。而加载的顺序是自顶向下，也就是由上层来逐层尝试加载此类。

#### 4、abstract不能与final并列修饰同一个类
> abstract修饰的类，不可实例化，所以需要子类去继承，然后重写其中的抽象方法。但是final修饰类是不可继承的。两者属性相冲。

#### 5、abstract 类中不建议有private的成员
可以有，但是不建议；private成员不能被继承，所以在abstract类中定义private成员没有意义

#### 6、abstract 方法必须在abstract类或接口中
> 抽象类中可以没有抽象方法，但是抽象方法必须在抽象类中或者接口中

#### 7、static方法中不能直接处理非static的属性
static不可以修饰非static的属性，因为类加载的时候，static属性比非static先初始化，那么一个存在的总不能访问一个没有存在的吧。

#### 8、Java创建对象的说法
1、用new语句创建对象，是最常见的创建对象的方法。
2、运用反射手段,调用java.lang.Class或者java.lang.reflect.Constructor类的newInstance()实例方法。
3、调用对象的clone()方法。
4、运用反序列化手段，调用java.io.ObjectInputStream对象的 readObject()方法。

Java有5种方式来创建对象：
1、使用 new 关键字（最常用）：
~~~java
ObjectName obj = new ObjectName();
~~~
2、使用反射的Class类的newInstance()方法：
~~~java
ObjectName obj = ObjectName.class.newInstance();
~~~
3、使用反射的Constructor类的newInstance()方法：
~~~java
ObjectName obj = ObjectName.class.getConstructor.newInstance();
~~~
4、使用对象克隆clone()方法：
~~~java
ObjectName obj = obj.clone();
~~~
5、使用反序列化（ObjectInputStream）的readObject()方法：
~~~java
try (ObjectInputStream ois = new ObjectInputStream(new FileInputStream(FILE_NAME))) {
             ObjectName obj = ois.readObject();
            }
~~~
   
#### 9、构造方法
 
  **构造方法的特点：** 
 
  * 构造方法的方法名与类名相同 
 
  * 构造方法没有返回值类型，也不写void 
 
  * 构造方法可以重载 
 
  **什么时候会用到构造方法：**
 
  * 在创建对象的时候 
 
  **构造方法的作用：** 
 
  * 创建对象的时候给属性赋初值 
 
  **构造方法的分类：** 
 
  * 显示的构造方法和隐式地构造方法 
 
  * 显示的构造方法：**显示的写出构造方法时，系统不会提供默认的无参构造方法** 
 
  * 隐式地构造方法：系统默认提供的无参构造方法 
 
  **代码块：** 
 
  * 分类：静态代码块和实例代码块 
 
  * 静态代码块：static修饰的代码块，在类加载时执行，且只执行一次。因为类就加载一次了。 
 
  * 实例代码块：没有static修饰的代码块，创建对象时执行，每创建一次对象加载一次。 
 
  实例代码块在执行构造方法之前执行。所以优先级高于构造方法。

  **总结：**
  > 静态块>main()>构造块>构造方法 

  当涉及继承时，按照如下顺序执行： 

  * 执行父类的静态块 

  * 执行子类的静态块 

  * 执行父类的构造块，然后执行父类的构造方法 

  * 执行子类的构造块，然后执行子类的构造方法

  
