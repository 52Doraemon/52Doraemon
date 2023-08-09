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
