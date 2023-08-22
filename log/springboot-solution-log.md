![image](https://github.com/52Doraemon/52Doraemon/assets/74476598/eb5492e6-513f-44ca-afdc-3ecbb712db99)#### 1、切面方法joinPoint.proceed();的作用
> 将控制权传递给下一个切面或目标方法

point.proceed()方法用于将控制权传递给下一个切面或目标方法。在应用了多个切面的情况下，**每个切面都需要调用proceed()方法来实现对下一个切面或目标方法的执行。**
在一个方法上应用多个切面时，切面按照声明的顺序依次执行。当一个切面中调用了proceed()方法后，**控制权将传递给下一个切面**或最终的目标方法。然后，每个切面都可以在调用proceed()方法前后执行自己的逻辑操作。

#### 2、集合初始化时，指定集合初始化值大小。建议使用Map<String, Object> map = new HashMap<>(16)替代Map<String, Object> map = new HashMap<>()

~~~
建议使用Map<String, Object> map = new HashMap<>(16)的原因如下：

1、初始容量：在创建HashMap时，指定初始容量可以避免扩容的频率，提高HashMap的性能。通过给HashMap指定一个大致估计的初始容量，可以减少重新分配和重新哈希的次数，从而提高性能。在这个例子中，使用初始容量为16。

2、散列冲突：HashMap使用散列函数将键对象映射到桶中，但是由于不同的键对象可能有相同的散列值，可能会导致散列冲突。当发生冲突时，HashMap使用链表或红黑树来存储具有相同散列值的键值对。初始容量足够大，可以减少散列冲突的概率，从而提高HashMap的性能。

3、内存占用：HashMap的初始容量是决定其内部数组的大小的因素之一。如果初始容量设置得太小，可能会导致频繁的扩容操作，从而浪费内存。通过给HashMap指定一个相对较大的初始容量，可以减少内存占用并提高空间利用率。

需要注意的是，选择HashMap的初始容量需要根据具体的情况进行权衡和评估。如果知道大概会存储多少个键值对，可以尝试根据实际情况选择适当的初始容量，以提高HashMap的性能和空间效率。
~~~

#### 3、@NonNull 注解检查的是是否存在变量而不是变量的值

@NonNull String mobilePhone当没有mobilePhone参数将报错java.lang.NullPointerException: mobilePhone is marked non-null but is null

#### 4、关于注入的Bean不能是静态成员变量

~~~java
//问题
//不建议使用 @Autowired 注解注入静态变量。这是因为在 Spring 初始化过程中，静态变量会在 Spring 容器完成 Bean 的实例化和依赖注入之前就已经被初始化，从而导致注入失败。
    @Autowired
    public static RedisTemplate<String, Object> redisTemplate;//redisTemplate为NULL
~~~
~~~java
//去掉static
	@Resource
    private RedisTemplate<String, Object> redisTemplate;
~~~

#### 5、依赖循环问题
~~~java
//解决这种问题的一种方法是使用依赖循环解决方案，其基本思想是通过将某些任务或操作延迟到其他任务或操作完成之后，来打破循环依赖关系。
//在下面的示例中，UserService和EmailService相互依赖。为了解决这种循环依赖，UserServiceImpl使用构造函数注入了UserRepository和EmailService，而EmailServiceImpl使用构造函数注入了UserService。
//通过使用@Autowired注解和构造函数，Spring可以正确地解析依赖关系并创建所需的bean实例，从而避免了依赖循环问题。
//注意：如果依赖关系无法通过构造函数解决，还可以使用setter方法注入依赖。但是，最好的实践是在可能的情况下使用构造函数注入依赖。
//方案一：构造函数注入（最推荐的方法）
@Service
public class UserServiceImpl implements UserService {

    private final UserRepository userRepository;
    private final EmailService emailService;

    @Autowired
    public UserServiceImpl(UserRepository userRepository, EmailService emailService) {
        this.userRepository = userRepository;
        this.emailService = emailService;
    }

    // UserServiceImpl的其他方法

}

@Service
public class EmailServiceImpl implements EmailService {

    private final UserService userService;

    @Autowired
    public EmailServiceImpl(UserService userService) {
        this.userService = userService;
    }

    // EmailServiceImpl的其他方法

}
~~~

~~~java
//方案二：setter方法注入
//在上面的示例中，ServiceA和ServiceB相互依赖。使用setter方法注入，同样避免了循环依赖问题。在创建ServiceA实例时，Spring会自动调用setServiceB方法，并传递一个ServiceB实例作为参数，而在创建ServiceB实例时，会自动调用setServiceA方法，并传递一个ServiceA实例作为参数。
@Service
public class ServiceAImpl implements ServiceA {

    private ServiceB serviceB;

    @Autowired
    public void setServiceB(ServiceB serviceB) {
        this.serviceB = serviceB;
    }

    // ServiceAImpl的其他方法

}

@Service
public class ServiceBImpl implements ServiceB {

    private ServiceA serviceA;

    @Autowired
    public void setServiceA(ServiceA serviceA) {
        this.serviceA = serviceA;
    }

    // ServiceBImpl的其他方法

}
~~~

~~~java
//方案三：循环依赖解决方案
//在下面的示例中，ServiceA和ServiceB相互依赖。使用setter方法注入，同样避免了循环依赖问题。在创建ServiceA实例时，Spring会自动调用setServiceB方法，并传递一个ServiceB实例作为参数，而在创建ServiceB实例时，会自动调用setServiceA方法，并传递一个ServiceA实例作为参数。
@Service
public class ServiceAImpl implements ServiceA {

    private ServiceB serviceB;

    @Autowired
    public void setServiceB(ServiceB serviceB) {
        this.serviceB = serviceB;
    }

    // ServiceAImpl的其他方法

}

@Service
public class ServiceBImpl implements ServiceB {

    private ServiceA serviceA;

    @Autowired
    public void setServiceA(ServiceA serviceA) {
        this.serviceA = serviceA;
    }

    // ServiceBImpl的其他方法

}

@Configuration
public class AppConfig {

    @Bean
    public ServiceA serviceA(ServiceB serviceB) {
        ServiceAImpl serviceA = new ServiceAImpl();
        serviceA.setServiceB(serviceB);
        return serviceA;
    }

    @Bean
    public ServiceB serviceB(ServiceA serviceA) {
        ServiceBImpl serviceB = new ServiceBImpl();
        serviceB.setServiceA(serviceA);
        return serviceB;
    }

}
~~~

~~~java
//方案四：懒加载注解@Lazy
//懒加载的意思是只有在需要使用该bean时才会被创建，而不是在容器启动时就被创建。
//ServiceA和ServiceB相互依赖。通过在构造函数参数上添加@Lazy注解，告诉Spring在需要使用该bean时才创建它，从而避免了循环依赖问题。
//需要注意的是，使用懒加载注解方法可能会导致性能问题，因为bean只有在需要使用时才会被创建。因此，如果依赖循环不是很复杂，最好还是使用构造函数或setter方法注入解决循环依赖问题。
@Service
public class ServiceAImpl implements ServiceA {

    private ServiceB serviceB;

    public ServiceAImpl(@Lazy ServiceB serviceB) {
        this.serviceB = serviceB;
    }

    // ServiceAImpl的其他方法

}

@Service
public class ServiceBImpl implements ServiceB {

    private ServiceA serviceA;

    public ServiceBImpl(@Lazy ServiceA serviceA) {
        this.serviceA = serviceA;
    }

    // ServiceBImpl的其他方法

}

//或

@Service
public class ServiceAImpl implements ServiceA {

    @Lazy
    @Autowired
    private ServiceB serviceB;

    // ServiceAImpl的其他方法

}

@Service
public class ServiceBImpl implements ServiceB {

    @Lazy
    @Autowired
    private ServiceA serviceA;

    // ServiceBImpl的其他方法

}
~~~

#### 6、Controller不能加事物，加上事物会导致代理失败
当您遇到在控制层中不能注入某个Service时，比如注入userService但是为null的问题时，这可能是由于使用了@Transactional注解导致的。在Spring中，使用@Transactional会创建一个代理对象来处理事务。因此，当您在Controller层使用@Transactional时，可能会导致自动装配（Autowired）的Bean无法正确注入。

1. API进来会调用第一个接口，因为有一个默认的接口，一开始会调用一个请求，那个请求是一开始代理的，那时候如果有冲突，是会被@Transactional注解生成的代理对象创建的
2. 后面调用其他接口，是不会被代理对象创建的，会使用的是我们原先的@Autowired注解的service
3. 就出现了刚进来的那个接口，一直都错误，500错误，其他接口都是正常的，比如说查询我查不出来，但是我能增加新的用户

#### 7、一次对象转换为JSON导致的堆内存溢出问题

> 总结：`HttpServletRequest` 是一个复杂的对象，它可能包含大量的数据，而且很可能包含对自身的循环引用。因此，当 Gson 尝试将其转换为 JSON 时，可能会导致内存溢出或堆栈溢出。

~~~java
// joinPoint的定义是ProceedingJoinPoint joinPoint，
Object[] args = joinPoint.getArgs();
// 将切面方法的输入参数转换为json记录到日志中
String params = new Gson().toJson(args);
sysLog.setParams(params);

// joinPoint.getArgs()返回的是一个对象数组（Object[]），数组中的每个元素对应一个切点方法的输入参数。
// 如果你的切点是一个方法public void exampleMethod(String a, int b)，并且你在运行时调用了exampleMethod("test", 123)，那么joinPoint.getArgs()将返回一个包含两个元素的数组："test"和123。

// 这本身并没有问题，关键是切点的方法参数被我增加了一个HttpServletRequest request，导致args获取的值包含request对象。在加上这个参数后这个方法的请求耗时接近30秒，平时只需要58毫秒，关键是抛出的问题只有Handler dispatch failed; nested exception is java.lang.OutOfMemoryError: Java heap space堆溢出。Caused by: java.lang.OutOfMemoryError: Java heap space

// 接着我各种方式查验和请求这个方法都是耗时超级长

// OutOfMemory，内存溢出， 是的，溢出了，什么问题导致的呢，是不是程序的内存给的不够引起的呢。
// 分配了一下启动的最大内存与最小内存，设置虚拟机参数给2G最大内存 -Xms512m -Xmx2048m，还是一下子吃满

// jvisualvm分析一下内存把，第一次请求出现的问题是内存暴涨，既然是内存问题，那么我们就对症下药，分析内存。
// 点击上图堆dump按钮进行堆内存分析
// 发现全是要存储的日志参数，都是被截断的
// 顺藤摸瓜找到那个方法的AOP，打断点排查一下，发现每次都是那个对象转JSON就进入了延时，然后堆内存溢出最后发现对象是个数组，前面三个参数都是我想要的AOP获取的切点方法的输入参数，第四个就是request，这是修改需求加上去的，应该是出在了对象转JSON这里，然后我去搜集资料，错误日志表明，Gson尝试序列化对象时遇到了堆栈溢出问题，看起来像是陷入了一个无限递归的序列化循环。这可能是因为您尝试序列化的对象具有循环引用或深层嵌套结构。


// HttpServletRequest对象包含对网络请求的引用，它本身包含了许多复杂的数据结构和可能具有循环引用的对象，这使得将其转换为JSON非常困难。如果你试图将整个HttpServletRequest对象转换为JSON，可能会遇到一些问题，包括：

// 1、**无限递归：**由于HttpServletRequest中可能存在循环引用的对象，尝试将其转换为JSON可能会导致无限递归，从而导致堆栈溢出错误（StackOverflowError）。

// 2、**序列化问题：**HttpServletRequest对象中可能包含无法序列化为JSON的Java对象，例如输入流、输出流、会话对象等。

// 3、**内存问题：**HttpServletRequest对象可能非常大，尝试将其完整转换为JSON可能会消耗大量内存，这可能导致内存溢出错误（OutOfMemoryError）。

// 因此，当你想要记录HttpServletRequest参数时，最好的做法通常是只记录你需要的具体参数，而不是试图记录整个请求对象。例如，你可以记录请求的URL、方法（GET、POST等）、参数、头信息等，而不是整个HttpServletRequest对象。
~~~

`HttpServletRequest`对象包含有关HTTP请求的所有信息，包括请求头，请求参数，会话数据，以及其他与请求相关的数据。在Servlet容器（如Tomcat）处理请求时，它创建并填充此对象，然后将其传递给相应的控制器方法。

一般情况下，`HttpServletRequest`对象包含了大量的数据，并且数据结构复杂，因为它可能包括对其他复杂对象的引用，例如会话对象、应用程序上下文对象等。然而，这些对象的生命周期由Servlet容器管理，而不是由你的应用程序代码管理，这意味着在你的代码中保留对这些对象的引用可能导致内存泄漏。

此外，由于这些对象的复杂性，尝试将整个`HttpServletRequest`对象序列化到JSON可能会导致问题。它可能包含循环引用，这会导致序列化进程陷入无限循环，最终导致堆栈溢出错误。也可能包含大量数据，尝试在内存中构建整个JSON表示可能会耗尽可用的内存，导致堆内存溢出错误。

再者，`HttpServletRequest`对象可能包含对敏感数据的引用，例如用户密码、会话标识符等。序列化整个`HttpServletRequest`对象并将其保存到日志中可能会引发安全问题。

因此，将`HttpServletRequest`对象直接序列化为JSON的实践并不推荐。更好的做法是仅选择你感兴趣的具体属性，并将这些属性的值转换为JSON，而不是尝试转换整个对象。

例如，你可能只关心请求的URL，HTTP方法，某些特定的请求头，以及某些特定的请求参数。你可以在你的AOP方法中显式地选择这些属性，并将它们序列化为JSON，而不是尝试序列化整个`HttpServletRequest`对象。

~~~java
`HttpServletRequest`对象包含有关HTTP请求的所有信息，包括请求头，请求参数，会话数据，以及其他与请求相关的数据。在Servlet容器（如Tomcat）处理请求时，它创建并填充此对象，然后将其传递给相应的控制器方法。

一般情况下，`HttpServletRequest`对象包含了大量的数据，并且数据结构复杂，因为它可能包括对其他复杂对象的引用，例如会话对象、应用程序上下文对象等。然而，这些对象的生命周期由Servlet容器管理，而不是由你的应用程序代码管理，这意味着在你的代码中保留对这些对象的引用可能导致内存泄漏。

此外，由于这些对象的复杂性，尝试将整个`HttpServletRequest`对象序列化到JSON可能会导致问题。它可能包含循环引用，这会导致序列化进程陷入无限循环，最终导致堆栈溢出错误。也可能包含大量数据，尝试在内存中构建整个JSON表示可能会耗尽可用的内存，导致堆内存溢出错误。

再者，`HttpServletRequest`对象可能包含对敏感数据的引用，例如用户密码、会话标识符等。序列化整个`HttpServletRequest`对象并将其保存到日志中可能会引发安全问题。

因此，将`HttpServletRequest`对象直接序列化为JSON的实践并不推荐。更好的做法是仅选择你感兴趣的具体属性，并将这些属性的值转换为JSON，而不是尝试转换整个对象。

例如，你可能只关心请求的URL，HTTP方法，某些特定的请求头，以及某些特定的请求参数。你可以在你的AOP方法中显式地选择这些属性，并将它们序列化为JSON，而不是尝试序列化整个`HttpServletRequest`对象。
~~~

#### 8、SpringBoot直接启动与jar包启动的不同

> 直接运行main class和 jar包启动主要区别是使用的类加载器不同。

通过Jar包的方式运行的加载器为：`LaunchedURLClassLoader`，会有特殊的目录结构要求，所以普通可以被应用的`jar`和可以被`java -jar`运行的jar的目录结构是不一样的。

* 直接启动

命令行输出的含义为：系统类/应用类加载器。在springbbot程序主函数处，加上一句控制台打印消息，打印出加载当前应用的入口函数所在类的加载器信息。

* Jar包的方式启动

通过maven或者gradle等方式对程序进行打包，生成可运行的Jar包，然后通过java -jar xx.jar的方式启动

