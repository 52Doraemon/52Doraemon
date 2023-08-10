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

#### 10、内部类
> 匿名内部类是 Java 中一种特殊的类，它允许你在创建对象的同时直接实现或重写类的方法。匿名内部类通常用于创建临时的、局部的类实例，它可以用来实现接口、继承类、重写方法等。

匿名内部类的优势在于可以在需要的地方定义类的实现，而不必显式地编写一个具名的类。这可以使代码更紧凑，但也需要谨慎使用，以避免过多的复杂性。匿名内部类通常适用于一些简单的、仅在特定上下文中使用的类实现。

在 Java 中，有四种主要的内部类创建方式：

**1、成员内部类（Member Inner Class）：**
定义在外部类的成员位置（字段、方法之外），它可以访问外部类的成员，包括私有成员。使用方式：
~~~java
class OuterClass {
    class InnerClass {
        // 内部类定义
    }
}
~~~
**2、静态内部类（Static Nested Class）：**
与成员内部类类似，但被声明为静态。静态内部类可以独立创建，而不需要先创建外部类实例。使用方式：
~~~java
class OuterClass {
    static class InnerClass {
        // 静态内部类定义
    }
}
~~~
**3、局部内部类（Local Inner Class）：**
定义在方法内部，仅在该方法内可见。局部内部类可以访问方法的参数和局部变量（但必须是 final 或等同于 final 的）。
~~~java
class OuterClass {
    void someMethod() {
        class LocalInnerClass {
            // 局部内部类定义
        }
    }
}
~~~
**4、匿名内部类（Anonymous Inner Class）：**
在创建对象的同时定义类实现或重写方法，通常用于临时需要某个类实现的情况。使用方式：
~~~java
interface MyInterface {
    void someMethod();
}

MyInterface obj = new MyInterface() {
    @Override
    public void someMethod() {
        // 匿名内部类实现
    }
};
~~~
#### 11、Maven和Ant

**Ant的作用：是一种基于Java的build工具**
 
   * 能够用ant编译java类。生成class文件     
   * ant能够自己定义标签、配置文件，用于构建。    
   * ant能够把相关层构建成jar包 。    
   * ant把整个项目生成web包。并公布到Tomcat     
 
  **Ant的长处：**

   * 跨平台性：Ant是纯Java语言编写的，因此具有非常好的跨平台性。   

   * 操作简单：Ant是由一个内置任务和可选任务组成的。Ant执行时须要一个XML文件(构建文件)。   
  
   * Ant通过调用target树，就能够运行各种task：每一个task实现了特定接口对象。因为Ant构建文件时XML格式的文件。所以非常easy维护和书写，并且结构非常清晰。    
   * Ant能够集成到开发环境中：因为Ant的跨平台性和操作简单的特点。它非常easy集成到一些开发环境中去。    
 
**Maven的作用： 除了以程序构建能力为特色之外，还提供高级项目管理工具。**

  Maven除了具备Ant的功能外。还添加了下面基本的功能： 
  
   * 使用Project Object Model来对软件项目管理。    
   * 内置了很多其它的隐式规则，使得构建文件更加简单。    
   * 内置依赖管理和Repository来实现依赖的管理和统一存储；    
   * 内置了软件构建的生命周期；   
 
  **Maven的长处：**
  
   * 拥有约定，知道你的代码在哪里，放到哪里去    
   * 拥有一个生命周期，比如运行 mvn install就能够自己主动运行编译，測试。打包等构建过程    
   * 仅仅须要定义一个pom.xml,然后把源代码放到默认的文件夹，Maven帮你处理其它事情    
   * 拥有依赖管理。仓库管理    
 
  总体的比較： 
	Ant将提供了非常多能够重用的task，比如 copy, move, delete以及junit单元測试Maven则提供了非常多能够重用的过程。

#### 12、GC时机
GC的时间是不确定的，且GC的过程需要经过可达性分析，一个对象只有被标记两次才会被GC。 

下图是一个对象被GC的全过程。 

#### 13、Java和C++
c是面向过程，java和c++都是面向对象，面向对象的三大特征是：封装、继承、多态。

#### 15、多态机制
> 重载是类中的多态，如果在一个类中定义了多个同名的方法，它们有不同的参数个数、有不同的参数类型、不同的参数顺序，则可通过不同的参数对同一个函数名进行动态绑定。即用同样的函数名实现多个不同方法，即多态。

java中提供了两种用于多态的机制:
* 1、通过子类对父类方法的覆盖实现多态
* 2、利用重载来实现多态.即在同一个类中定义多个同名的不同方法来实现多态。

#### 16、创建Statement是不传参的，PreparedStatement是需要传入sql语句
~~~java
Statement sta=con.createStatement(); ResultSet rst=sta.executeQuery("select * from book");
PreparedStatement pst=con.prepareStatement("select * from book"); ResultSet rst=pst.executeQuery();
~~~

> preparedStatement和statement的区别与联系：在JDBC应用中,如果你已经是稍有水平开发者,你就应该始终以PreparedStatement代替Statement.也就是说,在任何时候都不要使用Statement。

PreparedStatement接口继承Statement，
 
PreparedStatement 实例包含已编译的 SQL 语句，
所以其执行速度要快于 Statement 对象。
Statement为一条Sql语句生成执行计划，
如果要执行两条sql语句
~~~sql
select colume from table where colume=1;select colume from table where colume=2;
~~~
会生成两个执行计划
一千个查询就生成一千个执行计划！
PreparedStatement用于使用绑定变量重用执行计划
~~~sql
select colume from table where colume=:x;
~~~
通过set不同数据只需要生成一次执行计划，可以重用

另一个优点就是：防注入，安全
