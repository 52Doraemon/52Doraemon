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

#### 24、Java线程

1. **Thread Name**: 每个线程都有一个名称，便于在调试或者日志输出中区分和识别线程。如果在创建线程的时候没有显式地指定线程名称，系统会为线程分配一个默认的名称。
2. **Thread ID**: 线程的唯一标识符，用于区分不同的线程。线程ID由系统自动生成，并且在线程的生命周期内保持不变。
3. **Priority**: 线程的优先级。在操作系统调度线程运行的时候，优先级高的线程有更大的机会被选中运行。Java中，线程优先级的范围是1（Thread.MIN_PRIORITY）到10（Thread.MAX_PRIORITY）。
4. **Daemon status**: 守护线程标记。如果一个线程是守护线程，那么当所有的非守护线程结束时，它也会自动结束。垃圾回收线程就是一个常见的守护线程。
5. **Thread State**: 线程的状态。线程在其生命周期中会经历多种状态，包括新建（NEW）、可运行（RUNNABLE）、阻塞（BLOCKED）、等待（WAITING）、限期等待（TIMED_WAITING）和终止（TERMINATED）。
6. **Thread Group**: 线程所属的线程组。线程组可以用来管理一组相关的线程，对它们进行批量的控制操作。
7. **Runnable**: 线程要执行的任务，通常是一个实现了Runnable接口的对象。
8. **UncaughtExceptionHandler**: 未捕获异常处理器。当线程在运行过程中出现未捕获的异常时，会调用这个处理器来处理这个异常。
9. **Context Class Loader**: 上下文类加载器。线程的上下文类加载器用于加载类和资源。

#### 25、ThreadLocal和线程的关系

`ThreadLocal`并不是线程的一个参数，而是Java中的一个类，它提供了一种将对象的生命周期绑定到线程的能力，使得每个线程都可以有一个该对象的本地版本。

`ThreadLocal`经常被用来解决多线程编程中，变量线程安全的问题。由于`ThreadLocal`为每个线程创建了一份独立的变量副本，所以每个线程在操作这个变量时，实际上只是在操作自己的副本，从而避免了线程安全问题。

例如，在实现数据库连接、会话管理等需要保证每个线程都拥有独立的状态的情况下，`ThreadLocal`就非常有用。每个线程都可以拥有自己的数据库连接或者会话对象，从而避免了多个线程同时操作同一对象可能出现的线程安全问题。



`ThreadLocal` 是 Java 提供的一种用于实现线程局部变量的机制。每个线程都可以拥有自己的 `ThreadLocal` 变量，每个线程对其进行读写操作只会影响当前线程内的该变量值，不会影响其他线程的该变量值。`ThreadLocal` 变量通常用于防止多线程并发导致数据混乱的问题。

在 Java 中，每个线程都维护了一个 `ThreadLocalMap` 的数据结构，这个 `ThreadLocalMap` 是一个哈希表，用于存储 `ThreadLocal` 变量。键为 `ThreadLocal` 对象，值为该 `ThreadLocal` 对象设置的具体值。

主线程和新创建的线程都有各自的 `ThreadLocal` 变量，它们互不影响。也就是说，`ThreadLocal` 提供了一种方式，使得每个线程都可以拥有自己独立的变量。

ThreadLocal使用的方法

~~~java
public class ThreadLocalExample {

    // 创建 ThreadLocal 实例
    public static ThreadLocal<String> threadLocal = new ThreadLocal<>();

    public static void main(String[] args) {

        // 在主线程中设置 ThreadLocal 的值
        threadLocal.set("Value set in main thread");

        // 在主线程中获取 ThreadLocal 的值
        String valueInMainThread = threadLocal.get();
        System.out.println("In main thread, value is: " + valueInMainThread);

        // 创建一个新的线程
        Thread thread = new Thread(() -> {
            // 在新线程中设置 ThreadLocal 的值
            threadLocal.set("Value set in child thread");

            // 在新线程中获取 ThreadLocal 的值
            String valueInChildThread = threadLocal.get();
            System.out.println("In child thread, value is: " + valueInChildThread);
        });

        thread.start();
        
        // 删除主线程中的 ThreadLocal 的值，防止内存泄漏
        threadLocal.remove();
    }
}
//输出：
//In main thread, value is: Value set in main thread
//In child thread, value is: Value set in child thread
~~~

> **即使 ThreadLocal 的实例是同一个，但在不同线程中设置和获取的值是不同的。这就是 ThreadLocal 的特性。**

#### 26、父子线程

在Java中，并没有父子线程的概念。所有线程都独立执行，并且没有特定的父子关系。当我们创建一个新的线程时，这个线程会独立运行，并且它的生命周期和其他线程的生命周期没有直接的关联。

然而，有一种类似父子关系的概念，那就是线程组（Thread Group）。线程组是一种用于管理线程的机制。每个线程都属于一个线程组，线程组可以包含其他线程组，形成树状结构。

举个例子，当我们创建一个新的线程时，如果没有指定线程组，那么新线程将被默认分配到创建它的线程所在的线程组。也就是说，如果线程A在执行过程中创建了线程B，那么线程B将被分配到线程A所在的线程组，可以视作线程A是线程B的"父线程"，但实际上他们并无父子关系，只是在同一个线程组中。

#### 27、Servlet容器线程池

异步的或者在独立的线程中运行，那么这些线程可能并不会在由 ThreadPoolTaskExecutor 管理的线程池中。

在Spring框架（包括Spring Boot）中，HTTP请求默认是在一个独立的线程中处理的，这些线程通常由Servlet容器（例如Tomcat或Jetty）的线程池管理，而不是由你自己创建的线程池（如`ThreadPoolTaskExecutor`）管理。

所以，即使你在Spring Boot应用程序中配置了自己的`ThreadPoolTaskExecutor`，处理HTTP请求的线程还是由Servlet容器的线程池管理。

另一方面，当我们说“请求是异步的”，通常是指从客户端的角度来看。例如，客户端可以发送一个请求，然后在不等待服务器响应的情况下继续执行其他任务。然而，从服务器（即你的Spring Boot应用程序）的角度来看，每个请求仍然需要在一个线程中被处理，这个线程是由Servlet容器的线程池提供的。

然而，这并不意味着你的`ThreadPoolTaskExecutor`在Spring Boot应用程序中没有用途。你可以在处理HTTP请求的线程中使用`ThreadPoolTaskExecutor`来执行CPU密集型或IO密集型的任务，这样可以避免阻塞处理HTTP请求的线程，提高应用程序的性能和响应能力。

#### 28、Java为什么要有基本数据类型的封装类或者为什么要将基本类型封装为类

> Java是一门面向对象的语言，将基本数据类型封装为类（对象）更加符合Java语言的基本特性。

Integer是基本数据类型int的封装类，在Java里面有八种基本数据类型，它们都有一一对应的封装类型，基本类型和封装类型的区别有很多。

比如：

第一个：int类型可以直接定义一个变量名称赋值即可，但是Integer需要使用new关键字创建对象。

第二个：基本类型和Integer类型混合使用的时候，Java会自动通过拆箱和装箱来实现类型的转换。

第三个：Integer作为一个对象类型，封装了一些方法和属性，可以利用这些方法和属性来操作数据。

第四个：作为成员变量，Integer的默认值是null，而int的默认值是0。

> 之所以要对基础数据类型设计一个对应的封装类型，是因为Java本身就是一门面向对象的语言，对象是Java的基础操作单元，我们时时刻刻都在创建对象，也随时都在使用对象，很多时候在传递数据的时候也需要用到对象类型，比如像ArrayList、HashMap这样一些集合，它只能去存储对象类型，从这个点来说，封装类型存在的意义就很大，其次封装类型其实还有很多好处：安全性比较好，可以避免外部操作随意修改成员变量值，保证成员变量和数据传递的安全性，隐藏了实现细节对使用者更加友好，只需要去调用对象提供的方法，就可以完成对象的操作。

```
拓展：integer和int的区别

integer和int的区别有很多，简单罗列三个方面：

第一个：作为成员变量来说，integer的初始值是null，int的初始值是0。

第二个：Integer是存储在堆内存里面的，因为它是一个对象；int类型是直接存储在栈空间里面的。

第三个：integer是一个对象类型，它封装了很多的方法和属性，我们在使用的时候会更加灵活。

至于为什么要设计成封装类型，我认为的主要原因是Java本身就是一门面向对象的语言，一切操作都是以对象作为基础的，比如说像集合里面存的元素，也只支持Object类型，普通类型是无法通过集合来存储的。
```

#### 29、javax是什么包

> javax 是 Java 的一个扩展（Extension）包命名空间，其中包含一系列用于企业级应用的类和接口。这个命名空间下面有很多子包，这些子包通常都是一些特定的 Java 技术或框架的组成部分。

例如：

* javax.servlet 和 javax.servlet.http：包含了用于开发动态 Web 应用的 Servlet 和 HTTP 相关的类和接口。
* javax.jms：Java 消息服务（Java Message Service）的 API。
* javax.naming：Java 命名和目录接口（Java Naming and Directory Interface, JNDI）的 API。
* javax.transaction：包含了 Java 事务处理的相关类和接口。
* javax.persistence：Java 持久化 API（Java Persistence API, JPA）的一部分。
* 
值得注意的是，随着 Java 的发展和模块化，一些新的包和框架已经开始使用 jakarta 命名空间代替 javax，尤其在 Eclipse Foundation 接手 Java EE（现更名为 Jakarta EE）后。

javax 命名空间内的类和接口通常不是 Java 标准库（Java SE）的一部分，而是需要额外导入的。这些通常会作为独立的库（JAR 文件）提供，或者作为更大框架和服务器（如 Tomcat、WildFly 等）的一部分来提供。

#### 30、NOT NULL属性和空串

> SQL设置了 `NOT NULL`还可以被空字符串（''）作为值插入数据
    
字段虽然确实设置了 NOT NULL，这意味着它不能接受 NULL 值。然而，如果在插入数据时提供了一个空字符串（''）作为字段的值，数据库将允许这个插入操作。因为在这种情况下，并没有尝试插入 NULL，而是插入了一个空字符串，它们是不同的概念。

空字符串（''）被认为是有效的字符串值，只是它的长度为零。当向字段插入数据时，如果提供了一个空字符串，这个操作是符合 NOT NULL 限制的。

如果想确保 nickname 列具有实际值，你可以在插入数据时对其进行验证。例如，可以在应用程序代码中检查 nickname 的长度是否大于零，或者使用数据库触发器在插入之前进行检查。
    
**后台检查**
    > ！=null 并不能检查空字符串（''）
    > 需要通过StringUtils.isEmpty()判断是否为空。
    
StringUtils.isEmpty和!= null是两种用于检查字符串是否为空或为null的方法，但它们的检查方式略有不同。

1、StringUtils.isEmpty：这是Apache Commons Lang库中的一个方法，用于检查字符串是否为空或null。它会检查字符串是否为null，以及字符串长度是否为零（即空字符串）。这意味着，如果字符串为null或为空字符串（长度为零），StringUtils.isEmpty将返回true。
    
2、!= null：这是Java中的基本操作符，用于检查对象（如字符串）是否为null。它不会检查字符串的长度，只会检查字符串是否为null。因此，如果字符串为null，!= null将返回false。

#### 31、Arrays.asList方式初始化列表

Arrays.asList 是一个非常方便的用于初始化列表（List）的方法，它接受一个数组或者是一个可变数量的参数，并将其转换为一个固定大小的列表。

~~~java
// 得到了一个包含三个元素的列表
List<String> list = Arrays.asList("a", "b", "c");
~~~

**需要注意的几点：**

返回的列表是 java.util.Arrays 的一个私有静态内部类，它没有实现 add 或 remove 方法。如果你尝试添加或删除元素，将会得到 UnsupportedOperationException。

返回的列表是对原始数组的一个“视图”，这意味着，如果你改变原始数组，那么列表的内容也会跟着变。

因为它是一个固定大小的列表，所以你不能添加或删除元素，但是可以改变列表中现有元素的值。

如果需要一个可修改的列表，可以创建一个新的 ArrayList：

~~~java
// 这样，modifiableList 就是一个可以自由添加或删除元素的列表了
List<String> modifiableList = new ArrayList<>(Arrays.asList("a", "b", "c"));
~~~

#### 32、Arrays.asList的常见用法

Arrays.asList 是一个非常方便的工具方法，用于初始化固定大小的列表或将数组转换为列表。

基础用法

1、初始化列表
~~~java
List<String> stringList = Arrays.asList("Apple", "Banana", "Cherry");
~~~

2、初始化数字列表
~~~java
List<Integer> integerList = Arrays.asList(1, 2, 3, 4, 5);
~~~

3、初始化多类型列表（不建议）
~~~java
List<Object> mixedList = Arrays.asList(1, "two", 3.0);
~~~

高级用法

1、转换数组到列表
~~~java
// 请注意，这里返回的列表与原数组是“链接”的，修改其中之一会影响另一个。
String[] stringArray = {"Apple", "Banana", "Cherry"};
List<String> listFromArray = Arrays.asList(stringArray);
~~~

2、
~~~java
Set<String> stringSet = new HashSet<>(Arrays.asList("Apple", "Banana", "Cherry"));
~~~

3、用于初始化 Map 的键或值
~~~java
List<String> keys = Arrays.asList("Key1", "Key2");
List<String> values = Arrays.asList("Value1", "Value2");
Map<String, String> map = IntStream.range(0, keys.size())
        .boxed()
        .collect(Collectors.toMap(keys::get, values::get));
~~~

Arrays.asList使用注意事项：

返回的列表是不可变的（大小固定，但元素可以更改）。

不支持基本类型数组，例如 int[], double[] 等。你需要使用包装类型或者手动转换。

返回的列表不是 java.util.ArrayList 的实例，而是 java.util.Arrays 的一个内部类。

使用 Arrays.asList 可以使代码更简洁，但需要注意其限制和特性。

#### 33、
