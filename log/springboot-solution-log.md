![image](https://github.com/52Doraemon/52Doraemon/assets/74476598/eb5492e6-513f-44ca-afdc-3ecbb712db99)
#### 0、Springboot的核心注解

Spring Boot 提供了一系列核心注解，用于配置和启动应用程序。以下是一些常用的 Spring Boot 核心注解：

1. `@SpringBootApplication`：这是一个组合注解，用于标识主要的应用程序类。它包含了以下三个注解的功能：
   - `@Configuration`：指示该类是一个配置类，用于定义 Bean。
   - `@EnableAutoConfiguration`：启用 Spring Boot 的自动配置功能，根据依赖和配置自动配置应用程序。
   - `@ComponentScan`：自动扫描并注册 Spring 组件，包括控制器、服务等。
2. `@RestController`：用于标识控制器类，将其识别为处理 RESTful 请求的类。它结合了 `@Controller` 和 `@ResponseBody` 注解的功能。
3. `@RequestMapping`：用于映射请求路径和处理方法。可以用于类级别和方法级别，指定处理请求的 URL 路径。
4. `@Autowired`：用于自动注入依赖，通过类型匹配进行依赖注入。
5. `@Value`：用于将属性值注入到字段或方法参数中。可以用于注入配置文件中的属性值。
6. `@Component`：用于标识一个类为 Spring 组件，可以被自动扫描并注册到 Spring 容器中。
7. `@Service`：用于标识一个类为业务逻辑层的组件，通常用于服务类。
8. `@Repository`：用于标识一个类为数据访问层的组件，通常用于 DAO 类。
9. `@ConfigurationProperties`：用于将配置文件中的属性值绑定到一个 Java 类中，可以通过 `@ConfigurationProperties(prefix = "prefix")` 指定属性的前缀。

这些注解是 Spring Boot 中常用的核心注解，它们用于配置和控制应用程序的行为、管理依赖注入和声明 Spring 组件等。通过使用这些注解，可以快速搭建和配置 Spring Boot 应用程序。根据具体需求，还可以使用其他更特定的注解来实现更多的功能和配置。


#### 1、切面方法joinPoint.proceed();的作用
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

#### 9、动态数据源和多数据源不是同一个概念

> 动态数据源和多数据源并不是同一个概念，但它们有一定的联系和区别。

1、共同点：

* **数据源的多样性**：无论是多数据源还是动态数据源，都涉及在同一应用程序中管理和使用多个数据源。
关系：

* **动态数据源是多数据源的扩展**：可以将动态数据源看作是多数据源的一种更灵活、更高级的实现。在多数据源的基础上，动态数据源添加了在运行时根据特定条件选择使用哪个数据源的能力。
区别：

* **静态 vs 动态**：多数据源是静态的，意味着每个操作或事务都需要在编码时指定使用哪个数据源。而动态数据源则可以在运行时动态选择数据源，提供了更高的灵活性。
**使用场景**：多数据源主要用于数据存储分离的场景，而动态数据源适用于更复杂的多数据库架构，如读写分离、多租户等。

**总结**：
**多数据源**是在应用中同时配置和使用多个数据库连接的概念。
**动态数据源**是在多数据源的基础上，根据运行时的条件动态选择数据源的高级概念。
因此，虽然这两个概念有共同点和联系，但它们并不完全相同。动态数据源提供了多数据源所没有的灵活性和动态选择能力。

概念分析：

**1、多数据源（Multiple Data Sources）**：

**定义**：在同一应用程序中配置和使用多个不同的数据库连接。
**用途**：当应用程序需要连接到不同的数据库（例如，一个用于业务数据，另一个用于审计或日志）时。
**工作方式**：在配置时定义多个数据源，并在代码中明确指定使用哪一个数据源。
**灵活性**：有限，因为每个操作或事务必须在编码时指定使用哪个数据源。
**适用场景**：数据存储分离的场景，不同的数据源负责不同的业务逻辑或数据存储。

**2、动态数据源（Dynamic Data Sources）**：

**定义**：允许在运行时根据特定条件（例如用户身份、事务类型等）动态选择使用哪个数据源。
**用途**：当需要根据运行时条件动态更改数据源时，例如实现读写分离或多租户架构。
**工作方式**：可以在运行时切换到不同的数据源，通常通过AOP、拦截器等技术在不改变业务代码的情况下实现。
**灵活性**：更高，因为可以在运行时更改数据源，甚至允许每个请求使用不同的数据源。
**适用场景**：更复杂的多数据库架构，需要在运行时灵活切换数据源。

**总结**：
**多数据源**是在应用程序中定义和使用多个数据库连接的能力。它需要在代码中明确指定使用哪个数据源，适用于数据存储分离的场景。
**动态数据源**是在运行时根据特定条件选择使用哪个数据源的能力。它提供了更高的灵活性，适用于需要动态切换数据源的复杂场景，例如读写分离、多租户等。

> 简言之，多数据源是硬编码的、静态的，而动态数据源允许在运行时根据条件动态选择数据源。

> 数据源的切换

**在不同在线数据源之间的查询的时候，需要动态切换数据源再根据动态数据源动态执行对应的SQL获取对不同数据源执行不同操做的查询结果。**

#### 10、从数据库读取的数据源信息属于多数据源还是多态数据源

从数据库中读取的数据源信息可以用于配置多数据源或动态数据源。这个读取的信息只是用来定义和配置数据源的参数，并不直接决定这些数据源是多数据源还是动态数据源。

**1、多数据源**：
如果你在应用程序中配置了多个数据库连接，并在编程时静态指定每个操作或事务使用哪个数据源，则这属于多数据源的概念。

**2、动态数据源**：
如果你配置了一个数据源管理器，能够在运行时根据特定条件动态选择要使用的数据源，则这属于动态数据源的概念。

所以说，从数据库读取的数据源信息本身并不决定这些数据源是多数据源还是动态数据源。这主要取决于如何在应用程序中使用这些数据源信息，以及想实现的数据源管理策略。

#### 11、注解`@Controller`和`@RestController`的区别

在Spring MVC中，`@Controller`和`@RestController`注解用于标识控制器类。两者的区别在于返回值的处理方式：

- `@Controller`：表示该类是一个控制器类，并且处理请求时会根据方法的返回值进行视图解析和渲染。方法可以返回视图名称或`ModelAndView`对象。**`@Controller`注解的处理方式会尝试进行视图解析，而不是直接返回数据。**
- `@RestController`：表示该类是一个控制器类，但所有请求处理方法都会返回数据（例如JSON、XML等）而**不是进行视图解析和渲染**。Spring会自动将返回的数据序列化为相应的格式，并设置正确的响应头。

在Spring MVC中，使用`@Controller`注解标识的类中的方法通常被称为请求处理方法。这些方法用于处理客户端发送的HTTP请求，并生成响应返回给客户端。

根据`@Controller`注解的特性，处理请求方法的返回值可以是以下两种类型：

1、视图名称（String类型）：当方法返回一个视图名称时，Spring会根据视图解析器（View Resolver）来解析该视图名称，并将其渲染为最终的响应内容。视图解析器将根据配置的规则和视图模板来确定要呈现的实际视图。视图名称通常对应于一个视图模板文件（如JSP、Thymeleaf模板等）。

示例代码：

~~~~java
@Controller
public class MyController {

    @RequestMapping("/hello")
    public String hello() {
        return "hello"; // 返回视图名称
    }
}
~~~~

在上述示例中，当请求匹配到`/hello`路径时，`hello()`方法返回一个视图名称`"hello"`。Spring会将其解析为具体的视图模板，然后将模板渲染为响应内容。

2、ModelAndView对象：`ModelAndView`是一个包含视图名称和模型数据的类。通过返回`ModelAndView`对象，你可以同时指定视图名称和要传递给视图的模型数据。

示例代码：

~~~~java
@Controller
public class MyController {

    @RequestMapping("/hello")
    public ModelAndView hello() {
        ModelAndView modelAndView = new ModelAndView();
        modelAndView.setViewName("hello"); // 设置视图名称
        modelAndView.addObject("message", "Hello, World!"); // 设置模型数据
        return modelAndView;
    }
}
~~~~

在上述示例中，`hello()`方法返回一个`ModelAndView`对象，其中设置了视图名称为`"hello"`，并添加了一个名为`"message"`的模型数据。Spring会将模型数据传递给相应的视图模板进行渲染。

总结来说，`@Controller`注解用于标识控制器类，而其中的方法用于处理请求。这些方法可以返回视图名称或`ModelAndView`对象，从而通过视图解析器进行视图解析和渲染，最终生成响应内容返回给客户端。

#### 12、Springboot启动进行初始化操作的常用方法

> Spring Boot 提供了多种进行初始化操作的方法，这些操作通常会在应用启动时执行。

1. ApplicationRunner 和 CommandLineRunner 接口

这两个接口可以用来执行一些初始化任务。ApplicationRunner 提供一个 run(ApplicationArguments args) 方法，而 CommandLineRunner 提供一个 run(String... args) 方法。

~~~java
@Component
public class MyApplicationRunner implements ApplicationRunner {
    @Override
    public void run(ApplicationArguments args) {
        // 初始化代码
    }
}

@Component
public class MyCommandLineRunner implements CommandLineRunner {
    @Override
    public void run(String... args) {
        // 初始化代码
    }
}
~~~

2. @PostConstruct 注解

该注解用于修饰一个非静态的 void 方法，被修饰的方法会在依赖注入完成后，且在 ApplicationContext 被加载后执行。

~~~java
@Component
public class MyInitializer {
    @PostConstruct
    public void init() {
        // 初始化代码
    }
}
~~~

3. 监听 Application 事件

Spring Boot 有一系列的生命周期事件，例如 ApplicationReadyEvent。你可以定义一个监听器来捕捉这些事件。

~~~java
@Component
public class MyApplicationListener implements ApplicationListener<ApplicationReadyEvent> {
    @Override
    public void onApplicationEvent(ApplicationReadyEvent event) {
        // 初始化代码
    }
}
~~~

4. SpringApplication.addInitializers(...)

通过 SpringApplication 类，你可以添加一个或多个 ApplicationContextInitializer 接口的实现。

~~~java
public class MyApplicationContextInitializer implements ApplicationContextInitializer<ConfigurableApplicationContext> {
    @Override
    public void initialize(ConfigurableApplicationContext applicationContext) {
        // 初始化代码
    }
}
~~~

然后在 main 方法中：

~~~java
public static void main(String[] args) {
    SpringApplication application = new SpringApplication(MyApplication.class);
    application.addInitializers(new MyApplicationContextInitializer());
    application.run(args);
}
~~~

#### 13、logback-spring.xml

~~~xml
<?xml version="1.0" encoding="UTF-8"?>
<!-- 日志级别从低到高分为TRACE < DEBUG < INFO < WARN < ERROR < FATAL，如果设置为WARN，则低于WARN的信息都不会输出 -->
<!-- scan:当此属性设置为true时，配置文档如果发生改变，将会被重新加载，默认值为true -->
<!-- scanPeriod:设置监测配置文档是否有修改的时间间隔，如果没有给出时间单位，默认单位是毫秒。
                 当scan为true时，此属性生效。默认的时间间隔为1分钟。 -->
<!-- debug:当此属性设置为true时，将打印出logback内部日志信息，实时查看logback运行状态。默认值为false。 -->
<configuration scan="true" debug="true" scanPeriod="30 seconds">

	<!--  日志文件储存地址, 通过application配置文件传入-->
   	<!-- 从配置文件中获取变量 -->
    	<springProperty scope="context" name="serverPort" source="server.port"/>
    	<springProperty scope="context" name="appName" source="spring.application.name"/>

	<!-- name的值是变量的名称，value的值时变量定义的值。通过定义的值会被插入到logger上下文中。定义变量后，可以使“${}”来使用变量。 -->
	<property name="APP_NAME" value="${appName}" />
	<!--定义日志文件的存储地址 -->
	<property name="LOG_HOME" value="./logs/${appName}" />

	<!--<property name="COLOR_PATTERN" value="%black(%contextName-) %red(%d{yyyy-MM-dd HH:mm:ss}) %green([%thread]) %highlight(%-5level) %boldMagenta( %replace(%caller{1}){'\t|Caller.{1}0|\r\n', ''})- %gray(%msg%xEx%n)" />-->

	<!--控制台日志，控制台输出 -->
	<appender name="STDOUT" class="ch.qos.logback.core.ConsoleAppender">
		<encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
			<!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符-->
			<!--<pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50}:%L - %msg%n</pattern>-->
			<pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %highlight(%-5level) %cyan(%logger{50}:%L) - %msg%n</pattern>
		</encoder>
	</appender>

	<!-- 按照每天生成日志文件 -->
	<appender name="FILE" class="ch.qos.logback.core.rolling.RollingFileAppender">
		<rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
			<!--日志文件输出的文件名 -->
			<FileNamePattern>${LOG_HOME}/${APP_NAME}-%d{yyyy-MM-dd}.%i.log</FileNamePattern>
			<!--日志文件保留天数 -->
			<MaxHistory>30</MaxHistory>
			<maxFileSize>50MB</maxFileSize>
		</rollingPolicy>
		<encoder class="ch.qos.logback.classic.encoder.PatternLayoutEncoder">
			<!--格式化输出：%d表示日期，%thread表示线程名，%-5level：级别从左显示5个字符宽度%msg：日志消息，%n是换行符 -->
			<pattern>%d{yyyy-MM-dd HH:mm:ss.SSS} [%thread] %-5level %logger{50}:%L - %msg%n</pattern>
		</encoder>
	</appender>

	<!-- 生成 error html格式日志开始 -->
	<appender name="HTML" class="ch.qos.logback.core.FileAppender">
		<filter class="ch.qos.logback.classic.filter.ThresholdFilter">
			<!--设置日志级别,过滤掉info日志,只输入error日志-->
			<level>ERROR</level>
		</filter>
		<encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
			<layout class="ch.qos.logback.classic.html.HTMLLayout">
				<pattern>%p%d%msg%M%F{32}%L</pattern>
			</layout>
		</encoder>
		<file>${LOG_HOME}/error-log.html</file>
	</appender>
	<!-- 生成 error html格式日志结束 -->

	<!-- 每天生成一个html格式的日志开始 -->
	<appender name="FILE_HTML" class="ch.qos.logback.core.rolling.RollingFileAppender">
		<rollingPolicy class="ch.qos.logback.core.rolling.SizeAndTimeBasedRollingPolicy">
			<!--日志文件输出的文件名 -->
			<FileNamePattern>${LOG_HOME}/${APP_NAME}-%d{yyyy-MM-dd}.%i.html</FileNamePattern>
			<!--日志文件保留天数 -->
			<MaxHistory>30</MaxHistory>
			<MaxFileSize>10MB</MaxFileSize>
		</rollingPolicy>
		<encoder class="ch.qos.logback.core.encoder.LayoutWrappingEncoder">
			<layout class="ch.qos.logback.classic.html.HTMLLayout">
				<pattern>%p%d%msg%M%F{32}%L</pattern>
			</layout>
		</encoder>
	</appender>
	<!-- 每天生成一个html格式的日志结束 -->

	<!--myibatis log configure -->
	<logger name="com.apache.ibatis" level="TRACE" />
	<logger name="java.sql.Connection" level="DEBUG" />
	<logger name="java.sql.Statement" level="DEBUG" />
	<logger name="java.sql.PreparedStatement" level="DEBUG" />

	<!-- 日志输出级别 -->
	<root level="DEBUG">
		<appender-ref ref="STDOUT" />
		<appender-ref ref="FILE" />
		<appender-ref ref="HTML" />
		<appender-ref ref="FILE_HTML" />
	</root>

</configuration>
~~~

#### 14、Servlet对于SpringBoot

Servlet（Server Applet）是用于Java Web应用的服务器端程序模型，并且Servlet API是Java企业版（Java EE）的一部分。在早期的Java Web应用开发中，Servlet扮演着非常重要的角色，负责处理客户端请求并生成响应。

Spring Boot是一个简化Spring应用开发的框架，它内置了对Servlet的支持，但在日常的Spring Boot应用开发中，直接使用Servlet并不是很常见。这是因为Spring Boot提供了更高级的抽象，例如使用@Controller和@RestController注解创建RESTful API，这些抽象背后还是依赖于Servlet。

Spring Boot能够自动配置嵌入的Servlet容器（如Tomcat、Jetty或Undertow），使开发者不需要过多关注底层的Servlet细节。但是，对于某些特殊的用例，例如，需要自定义HTTP请求和响应的处理流程，或者需要与遗留系统集成，了解Servlet仍然是有益的。

简而言之，Servlet对于Spring Boot的工作原理和底层实现来说是重要的，但在常见的应用开发场景中，开发者不一定需要直接使用或深入了解Servlet。然而，具备Servlet知识对于理解Web应用的工作机制以及处理一些特殊需求是有帮助的。

#### 15、Servlet原理和作用

> Servlet（Server Applet）是一种运行在服务器端的Java程序，用于处理客户端请求并生成响应。它是Java EE（Java Platform, Enterprise Edition）规范的一部分，尤其是用于构建Web应用。

**Servlet原理**

* 生命周期：Servlet在第一次被访问或根据配置被服务器加载时实例化。之后，它将一直在内存中存在，直到Web应用被卸载或服务器关闭。

* 请求处理：当HTTP请求到达Servlet容器（例如Tomcat）时，容器会创建一个新的线程（或从线程池中取出一个线程）并调用Servlet的service()方法。

* service()方法：这个方法会根据HTTP请求的类型（GET、POST、PUT、DELETE等）来调用相应的doGet(), doPost()等方法。

* 响应生成：Servlet使用HTTPServletResponse对象来生成响应，这个响应随后由Servlet容器发送回客户端。

* 销毁：当Web应用被卸载或服务器关闭时，Servlet的destroy()方法被调用，用于释放资源。

**Servlet作用**

* 请求处理：Servlet主要用于处理来自Web客户端（通常是浏览器）的请求。

* 数据处理与储存：Servlet可以与数据库交互，执行CRUD（创建、读取、更新、删除）操作。

* 会话管理：Servlet支持多种会话管理机制，如Cookie、Session等。

* MVC架构：在MVC（Model-View-Controller）架构中，Servlet通常作为控制器（Controller）来使用，处理业务逻辑并协调视图（View）和模型（Model）。

* API提供：除了生成HTML页面，Servlet也可以用于创建RESTful或SOAP Web服务。

* 过滤和拦截：通过Filter，Servlet可以实现请求的预处理和响应的后处理，用于安全认证、日志记录等。

* 与前端交互：Servlet可以生成HTML、XML或JSON数据，与前端JavaScript代码交互。

* 集群与负载均衡：Servlet容器通常提供了集群和负载均衡的解决方案，这使得Servlet能够更好地适应大规模、高并发的环境。

了解Servlet的原理和作用不仅能帮助你更好地理解Web应用的底层机制，还能让你在遇到复杂或特殊需求时，有更多的解决方案可供选择。

#### 16、Servlet和Tomcat的关系

Servlet和Tomcat之间的关系非常紧密，但两者在功能和定位上有明确的差异。

**Servlet：**

* 定义和规范：Servlet 是一种 Java 类，用于扩展能够响应客户端请求的服务器的功能。Servlet 是 JavaEE 规范的一部分。

* 程序逻辑：Servlet 负责处理来自 Web 客户端（通常是 Web 浏览器）的请求，执行业务逻辑，然后生成响应给客户端。

* 通用性：Servlet 不依赖特定的 Web 服务器，任何遵循 JavaEE 规范的 Web 服务器都能运行 Servlet。

* 生命周期：由 Servlet 容器（如 Tomcat）负责管理。

**Tomcat：**

* Web 容器：Tomcat 是一个实现了 Servlet、JSP 和 Java WebSocket 规范的 Web 容器。

* Servlet 管理：Tomcat 负责管理 Servlet 的生命周期，包括加载、初始化、处理请求和销毁。

* HTTP 服务器：虽然主要是作为 Web 容器使用，但 Tomcat 也是一个功能简单的 HTTP 服务器。

* 配置与扩展：Tomcat 有许多配置选项，用于处理日志、安全性、数据库连接等，还可以通过自定义 Valve、Filter、Listener 等进行扩展。

**关系：**

* 承载与执行：Tomcat 是一个常用的 Servlet 容器，用于承载并执行 Servlet。

* 生命周期管理：Tomcat 负责管理 Servlet 的整个生命周期，包括初始化、请求处理和销毁。

* 请求与响应：当一个 HTTP 请求到达 Tomcat，Tomcat 会将这个请求交给相应的 Servlet 进行处理，并将 Servlet 的响应返回给客户端。

* 资源管理：Tomcat 提供了各种服务和资源（如 JNDI、线程池、数据库连接池等）给 Servlet 使用。

* 部署与发布：开发者通常将编写好的 Servlet 打包成 WAR 文件，然后部署到 Tomcat 上。

简言之，Servlet 是用于处理 HTTP 请求和生成响应的 Java 程序，而 Tomcat 是一个 Servlet 容器，用于管理和运行 Servlet。

#### 17、Servlet实际上是java的一个类？

Servlet 不仅仅是一个 Java 类，它是一种 Java API 规范，用于 Web 开发。这个规范定义了用于创建动态 Web 页面的类和接口。在这个规范中，javax.servlet.Servlet 是一个核心接口，任何 Servlet 类都必须实现这个接口（或者继承已经实现这个接口的类，如 HttpServlet）。

当我们谈到一个具体的 "Servlet" 时，通常是指一个实现了 Servlet 接口（或扩展了某个已经实现了 Servlet 接口的类）的 Java 类。这个类需要重写某些方法，如 doGet() 或 doPost()，以处理 HTTP GET 或 POST 请求。

例如，一个简单的 HttpServlet 类可能看起来像这样：

~~~java
import javax.servlet.http.HttpServlet;
import javax.servlet.http.HttpServletRequest;
import javax.servlet.http.HttpServletResponse;
import java.io.IOException;

public class MyServlet extends HttpServlet {
    @Override
    protected void doGet(HttpServletRequest req, HttpServletResponse resp) throws IOException {
        resp.getWriter().write("Hello, World!");
    }
}
~~~

这个类继承自 HttpServlet，并重写了 doGet 方法以处理 HTTP GET 请求。

总的来说，当我们说 Servlet 是一个 Java 类时，是指它是一个遵循特定 Java API 规范的 Java 类。这个规范定义了如何接收和响应客户端请求，以及如何与 Web 服务器（或 Servlet 容器，如 Tomcat）交互。
