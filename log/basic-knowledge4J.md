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

#### 17、脏数据
> 脏数据：从目标中取出的数据已经过期、错误或者没有意义，这种数据就叫做脏数据。 脏读：读取出来脏数据就叫脏读。

通常是由于对写操作加锁二不对读操作加锁导致的。

#### 18、CopyOnWrite
CopyOnWrite 是 Java 并发编程中的一个重要概念，是用于处理**读多写少**场景的一种策略。CopyOnWrite 类一般指的是 CopyOnWriteArrayList 和 CopyOnWriteArraySet 这两个类，它们是线程安全的集合类，主要用于多线程环境下的读操作和写操作的处理。

以下是 CopyOnWrite 类的面试考点，逐步分析：

**概念和用途：**

解决的问题：CopyOnWrite 类适用于读多写少的情况，它通过在写操作时创建一个新的副本（复制），从而避免了写操作的锁竞争，实现了读写分离，保证了读操作的无锁访问。
适用场景：适用于读操作远远多于写操作的情况，例如缓存、配置信息、观察者模式等。

**线程安全性：**

读操作：由于每次写操作都会复制数据，所以读操作不会受到写操作的影响，读操作是无锁的，保证了线程安全。
写操作：写操作通过创建一个新的副本来避免锁竞争，保证了线程安全，但需要注意写操作可能会引起内存消耗。

**数据一致性：**

写操作的一致性：由于写操作会创建一个新的副本，所以写操作不会影响其他正在执行的读操作。这意味着，写操作的结果对于执行期间已经发生的读操作是不可见的，只对后续的读操作可见。
读操作的一致性：读操作总是读取一个一致性的快照，不会受到写操作的干扰。

**内存消耗：**

写操作的副本：由于每次写操作都需要创建一个新的副本，可能会导致内存消耗较大。
读操作的开销：读操作不需要锁，但需要复制一份数据副本，因此在频繁的读操作场景下，可能会产生较大的开销。

**适用性考虑：**

频繁写入：如果写操作频繁，或者写操作对于读操作的实时性要求较高，可能不适合使用 CopyOnWrite。
内存限制：如果数据集非常大，频繁的写操作会导致大量的内存消耗，需要谨慎使用。
迭代性能：CopyOnWrite 集合在迭代操作上性能较差，因为每次迭代都需要复制数据。

总之，面试中可能会考察你对 CopyOnWrite 类的原理、适用场景、线程安全性以及性能方面的理解。了解这些内容可以帮助你在设计多线程应用时选择合适的数据结构，以满足应用的性能和安全需求。

#### 19、线程局部存储TLS(thread local storage)技术
> Java平台的java.lang.ThreadLocal是TLS技术的一种实现

 ThreadLocal可以给一个初始值，而每个线程都会获得这个初始化值的一个副本，这样才能保证不同的线程都有一份拷贝。ThreadLocal
  不是用于解决共享变量的问题的，不是为了协调线程同步而存在，而是为了方便每个线程处理自己的状态而引入的一个机制. 

* 解决多线程中的对同一变量的访问冲突的一种技术
* TLS会为每一个线程维护一个和该线程绑定的变量的副本

#### 20、接口继承接口不用实现接口方法

在 Java 中，接口可以继承其他接口，而接口继承接口时并不需要在子接口中重新实现父接口的方法。子接口会继承父接口中的方法声明，但并不需要提供具体的方法实现。具体的方法实现是在实现了子接口的类中完成的。

> Collection 接口继承了 Iterable 接口。这意味着所有实现了 Collection 接口的类都可以被迭代，可以使用增强型的 for 循环（也称为 foreach 循环）或者使用迭代器来遍历集合中的元素。

#### 21、Iterable 接口
> Iterable 接口是 Java 提供的一个用于迭代的基础接口，它定义了一个抽象方法 iterator()，该方法返回一个用于遍历集合元素的迭代器。
> Iterable 接口是 Java 标准库中的一个接口，位于 java.lang 包中，用于表示可迭代的对象。实现了 Iterable 接口的类可以通过迭代器（Iterator）来遍历其元素。
> Iterable 接口定义了一个抽象方法 iterator()，该方法返回一个迭代器对象，用于遍历集合或序列中的元素。迭代器是一个设计模式，它提供了一种在不暴露集合内部结构的情况下遍历集合元素的方式。

#### 22、原生方法是什么意思
> 在 Java 中，原生方法（Native Method）是一种由非 Java 语言（如 C、C++）编写的方法，它可以通过 Java Native Interface（JNI）与 Java 代码进行交互。使用原生方法可以调用底层系统功能、访问硬件、执行与 Java 虚拟机不直接兼容的操作等。原生方法通常在特定情况下使用，例如需要与底层系统进行交互或调用外部库时。

> 在 Java 代码中通过 native 关键字声明一个原生方法。这告诉 Java 编译器该方法的实现将在其他非 Java 语言中提供。
~~~java
public class NativeMethodExample {
    // 声明原生方法
    public native void callNativeMethod();
}
~~~

#### 23、时间的格式

> `Date`、`LocalDate`和`LocalDateTime`，它们都是表示日期和时间的对象，**不包含固定的格式**，而是**通过格式化操作将其转换为特定格式的字符串。**

* `Date`：
  - 旧版的Java日期和时间类，位于`java.util`包中。
  - 表示一个特定的时刻，包含日期和时间信息，精确到毫秒级别。
  - 存在一些问题，如不可变性问题、时区处理问题等，在Java 8之后被新的日期时间API所替代。
* `LocalDate`：
  - Java 8引入的日期类，位于`java.time`包中。
  - 只包含日期部分，不包含时间和时区信息。
  - 用于表示年、月、日，常用于处理与日期相关的操作，如日期比较、日期运算等。
* `LocalDateTime`：
  - Java 8引入的日期时间类，位于`java.time`包中。
  - 包含日期和时间部分，不包含时区信息。
  - 用于表示年、月、日、时、分、秒等，适用于处理同时包含日期和时间的情况。
 
总结起来：

- `Date`是旧版Java的日期和时间类，精确到毫秒级别，存在一些问题，不推荐使用。
- `LocalDate`是只包含日期部分的日期类，用于处理与日期相关的操作。
- `LocalDateTime`是同时包含日期和时间部分的日期时间类，适用于同时处理日期和时间的情况。

在实际开发中，推荐使用新的日期时间API（`java.time`包）中的`LocalDate`和`LocalDateTime`，因为它们提供了更好的设计和更丰富的功能，同时解决了旧版`Date`类存在的问题。
