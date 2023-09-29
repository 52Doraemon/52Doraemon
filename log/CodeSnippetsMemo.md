# 碎片代码备忘录
#### 1、获取插入数据的主键（SpringBoot + MybatisPlus）
> 在使用 MyBatis Plus 的 save 方法保存实体类时，可以通过实体类的属性来获取插入后的主键值。

~~~java
// 创建实体类：
@Data
@TableName("user") // 指定对应的数据库表名
public class User {
    @TableId // 指定该属性为主键。@TableId 注解用于标识实体类的主键属性，确保数据库表和实体类的主键属性对应。
    private Long id;

    private String username;
    private String email;
}
~~~

~~~java
// 调用 save 方法：
@Service
public class UserService {
    private final UserMapper userMapper;

    @Autowired
    public UserService(UserMapper userMapper) {
        this.userMapper = userMapper;
    }

    public Long saveUser(User user) {
        userMapper.insert(user);
        return user.getId(); // 获取插入后的主键值
    }
}
~~~

**为什么这样可以获取主键，这个是在插入数据库前生成还是之后生成的？**

MyBatis Plus 提供了主键生成的自动化处理，使得在插入数据到数据库之前或之后都能够获取生成的主键值。这是通过以下几个关键点实现的：

* 主键生成策略配置：在实体类的主键属性上使用 @TableId 注解，同时指定主键生成策略。MyBatis Plus 支持多种主键生成策略，例如自增主键、UUID、雪花算法等。你可以根据实际情况选择适合的主键生成策略。

* 数据库插入操作：在调用 MyBatis Plus 的 insert 方法插入数据时，会根据主键生成策略在插入数据前或后生成主键值。

* 获取主键值：MyBatis Plus 会自动将生成的主键值回写到实体类的主键属性上，使得你可以在插入后直接通过实体类的方法获取到主键值。

具体来说：

* 如果主键生成策略是自增（如 MySQL 的自增主键），主键值会在插入数据库后由数据库生成，然后回写到实体类的主键属性上。这是数据库在插入数据后生成主键值的情况。

* 如果主键生成策略是 UUID 或雪花算法等，在 MyBatis Plus 的插入操作中会在插入数据前生成主键值，然后将生成的主键值赋给实体类的主键属性。这是在插入数据前生成主键值的情况。

总之，MyBatis Plus 封装了主键生成的细节，使得你在插入数据后，可以方便地通过实体类的方法获取生成的主键值。主键值的生成时机取决于所使用的主键生成策略。

#### 2、查找数据库某列的最大值（SpringBoot + MybatisPlus）
> 通过 MyBatis Plus 的 @Select 注解编写了一个查询方法，在该方法中执行 SQL 查询语句来获取某列的最大值。在业务层的 YourService 类中调用了这个方法，从而可以获取到查询结果。

1、创建实体类：创建对应数据库表的实体类，确保实体类中包含需要查询的列。

2、创建 Mapper 接口：创建一个继承自 BaseMapper 的 Mapper 接口，用于定义数据库操作方法。

3、编写查询方法：在 Mapper 接口中编写一个方法，使用 MyBatis Plus 提供的 @Select 注解来实现查询。

4、调用查询方法：在业务层或控制层中调用查询方法，获取查询结果。

~~~java
// 创建实体类：
@Data
@TableName("your_table_name")
public class YourEntity {
    private Long id;
    private String columnName; // 要查询的列名
    // 其他属性...
}
~~~

~~~java
// 创建 Mapper 接口：
// @Mapper 注解通常与 MyBatis Plus 配合使用，用于标识 Mapper 接口，让 Spring Boot 自动扫描并创建 Mapper 的实现。
@Mapper
public interface YourMapper extends BaseMapper<YourEntity> {
    // 定义查询方法
    @Select("SELECT MAX(column_name) FROM your_table_name")
    Integer selectMaxValue();
}
~~~

~~~java
//调用查询方法：
@Service
public class YourService {
    private final YourMapper yourMapper;

    @Autowired
    public YourService(YourMapper yourMapper) {
        this.yourMapper = yourMapper;
    }

    public Integer getMaxValue() {
        return yourMapper.selectMaxValue();
    }
}
~~~

#### 3、查找数据库某列的最大值（SpringBoot + Mybatis配置文件实现）
> 使用 MyBatis 实现查找某列的最大值，创建了一个 Mapper 接口 YourMapper，然后在 XML 配置文件中编写了查询方法的 SQL 查询语句。在业务层的 YourService 类中调用了这个方法，从而可以获取到查询结果。

1、创建 Mapper 接口：创建一个 Mapper 接口，用于定义数据库操作方法。

2、编写查询方法：在 Mapper 接口中编写一个方法，使用 MyBatis 的 XML 配置文件来实现查询。

3、调用查询方法：在业务层或控制层中调用查询方法，获取查询结果。

~~~java
// 创建 Mapper 接口：
public interface YourMapper {
    Integer selectMaxValue();
}
~~~

~~~xml
<!-- 编写 XML 配置文件：在 resources 目录下创建 MyBatis 的 XML 配置文件（如 your-mapper.xml）： -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE mapper PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN" "http://mybatis.org/dtd/mybatis-3-mapper.dtd">
<mapper namespace="com.example.mapper.YourMapper">

    <select id="selectMaxValue" resultType="java.lang.Integer">
        SELECT MAX(column_name) FROM your_table_name
    </select>

</mapper>
~~~

~~~java
// 调用查询方法：
@Service
public class YourService {
    private final YourMapper yourMapper;

    @Autowired
    public YourService(YourMapper yourMapper) {
        this.yourMapper = yourMapper;
    }

    public Integer getMaxValue() {
        return yourMapper.selectMaxValue();
    }
}
~~~
> MyBatis Plus 是对 MyBatis 的增强，提供了很多方便的注解和方法，其中包括了 @Mapper 注解用于标识 Mapper 接口。如果使用 MyBatis Plus，可以在 Mapper 接口上使用 @Mapper 注解，让 Spring Boot 自动生成 Mapper 的实现。

#### 4、Springboot怎么知道识别Mybaits的XML配置文件
> 在 Spring Boot 中，如果使用 MyBatis 进行数据库操作，需要配置 Spring Boot 让它能够识别并加载 MyBatis 的 XML 配置文件。

1、引入依赖：首先，在 pom.xml 文件中添加 MyBatis 的依赖，以及数据库驱动的依赖。例如，如果使用 MySQL 数据库，可以添加以下依赖：

~~~xml
<dependency>
    <groupId>org.mybatis.spring.boot</groupId>
    <artifactId>mybatis-spring-boot-starter</artifactId>
    <version>2.2.0</version>
</dependency>
<dependency>
    <groupId>mysql</groupId>
    <artifactId>mysql-connector-java</artifactId>
</dependency>
~~~

2、配置数据源：在 application.properties 或 application.yml 文件中配置数据源相关信息，例如数据库 URL、用户名、密码等。
~~~properties
spring.datasource.url=jdbc:mysql://localhost:3306/your_database
spring.datasource.username=your_username
spring.datasource.password=your_password
spring.datasource.driver-class-name=com.mysql.cj.jdbc.Driver
~~~

3、配置 MyBatis XML 扫描路径：在 application.properties 或 application.yml 文件中配置 MyBatis 的 XML 配置文件扫描路径。
~~~properties
mybatis.mapper-locations=classpath:mapper/*.xml
~~~
> 这里的 classpath:mapper/*.xml 指定了 MyBatis XML 配置文件所在的路径，可以根据实际情况修改。

1、创建 Mapper 接口：创建 Mapper 接口，不需要添加任何注解。Spring Boot 会自动扫描这些接口并创建相应的实现。

2、创建 MyBatis XML 配置文件：创建 MyBatis XML 配置文件，放置在配置的扫描路径下（例如，src/main/resources/mapper 目录）。在这些配置文件中，编写 SQL 语句和映射关系。

3、使用 Mapper 接口：在需要进行数据库操作的地方，注入相应的 Mapper 接口，调用方法进行数据库操作。

#### 5、MybatisPlus 条件构造器

> `User::getName`这种语法是 Java 8 引入的 Lambda 表达式的一种写法，它用于创建一个方法引用，表示调用 User 类的 getName 方法。在 MyBatis Plus 的条件构造器中，特别是 LambdaQueryWrapper 和 LambdaUpdateWrapper，可以使用这种语法来构建类型安全的查询条件或更新条件。

* `QueryWrapper`：常规条件构造器，用于构建普通的查询条件。（常用）

* `LambdaQueryWrapper`：使用 Lambda 表达式构建查询条件，使代码更加简洁和可读。（常用）

* `UpdateWrapper`：构建更新条件。（常用）

* LambdaUpdateWrapper：使用 Lambda 表达式构建更新条件。

* AbstractWrapper：抽象的条件构造器，为其他类型的条件构造器提供了共享的方法和逻辑。

* QueryChainWrapper：一种链式查询的封装，提供了链式调用的方式来构建查询条件。

* LambdaQueryChainWrapper：基于 Lambda 表达式的链式查询封装。

* UpdateChainWrapper：链式更新封装，提供了链式调用的方式来构建更新条件。

* LambdaUpdateChainWrapper：基于 Lambda 表达式的链式更新封装。

~~~java
@Service
public class UserService {

    // 示例实体类
    // User 类包含 id、name、age、email 字段

    // 使用 QueryWrapper 进行查询
    public List<User> getUsersByAge(int age) {
        QueryWrapper<User> queryWrapper = new QueryWrapper<>();
        queryWrapper.eq("age", age);
        return userMapper.selectList(queryWrapper);
    }

    // 使用 LambdaQueryWrapper 进行查询
    public List<User> getUsersByName(String name) {
        LambdaQueryChainWrapper<User> lambdaQueryWrapper = new LambdaQueryChainWrapper<>(userMapper);
        return lambdaQueryWrapper.like(User::getName, name).list();
    }

    // 使用 UpdateWrapper 进行更新
    public int updateUserAge(String name, int newAge) {
        UpdateWrapper<User> updateWrapper = new UpdateWrapper<>();
        updateWrapper.eq("name", name).set("age", newAge);
        return userMapper.update(null, updateWrapper);
    }

    // 使用 LambdaUpdateWrapper 进行更新
    public int updateUserEmail(String name, String newEmail) {
        LambdaUpdateWrapper<User> lambdaUpdateWrapper = new LambdaUpdateWrapper<>();
        lambdaUpdateWrapper.eq(User::getName, name).set(User::getEmail, newEmail);
        return userMapper.update(null, lambdaUpdateWrapper);
    }

    // 使用 UpdateChainWrapper 进行链式更新
    public int updateUserAgeWithChain(String name, int newAge) {
        UpdateChainWrapper<User> updateChainWrapper = new UpdateChainWrapper<>(userMapper);
        return updateChainWrapper.eq("name", name).set("age", newAge).update();
    }

    // 使用 LambdaUpdateChainWrapper 进行链式更新
    public int updateUserEmailWithChain(String name, String newEmail) {
        LambdaUpdateChainWrapper<User> lambdaUpdateChainWrapper = new LambdaUpdateChainWrapper<>(userMapper);
        return lambdaUpdateChainWrapper.eq(User::getName, name).set(User::getEmail, newEmail).update();
    }
}
~~~

#### 6、跨域处理

> 跨域问题是由于浏览器的同源策略（Same-Origin Policy）导致的。

同源策略是一个浏览器安全机制，用于阻止不同源（协议、域名和端口）之间的网络请求访问和操作对方的资源。这意味着，如果一个网页的脚本尝试通过 XMLHttpRequest 或 Fetch API 发起一个跨域的请求，那么浏览器会拒绝这个请求。

**产生原因**

> 跨域产生的原因是由于前端地址与后台接口不是同源，从而导致 ajax 不能发送

非同源产生的问题

* Cookie、LocalStorage 和 IndexDB 无法获取
* DOM 无法获得
* AJAX 请求不能发送

同源条件：**协议、端口、主机**三者相同即为同源。其中只要某一个不一样则为不同源

**解决方式**

本地开发跨域

本地开发一般使用下面 3 种方式进行处理

* vite 的 proxy 进行代理
* 后台开启 cors
* 使用 nginx 转发请求

生产环境跨域

生产环境一般使用下面 2 种方式进行处理

* 后台开启 cors
* 使用 nginx 转发请求

> 后台开启 cors 不需要前端做任何改动


常见的解决跨域问题方法有以下几种：

1、JSONP（JSON with Padding）：通过动态创建<script>标签，利用<script>标签的src属性可以跨域加载资源的特性，来实现跨域请求。

2、CORS（Cross-Origin Resource Sharing）：服务器在响应中设置一些特定的HTTP头部，告诉浏览器允许来自其它源的请求访问资源。

3、反向代理：在服务器端配置一个代理服务器，通过代理服务器转发请求，从而绕过了浏览器的同源策略。

4、WebSocket：使用WebSocket协议进行通信，它不受同源策略限制，可以在不同域名和端口间实现双向通信。

#### 7、对在数据库中查询结果进行分页，可以使用Mybatis Plus提供的分页插件来实现

~~~java
@Mapper
public interface TablePropertyMapper {

    @Select("SELECT * FROM information_schema.columns WHERE table_name = #{tableName}")
    IPage<TablePropertyVO> getTableProperties(Page<TablePropertyVO> page, @Param("tableName") String tableName);

}

@Service
public class TablePropertyService {

    private final TablePropertyMapper tablePropertyMapper;

    @Autowired
    public TablePropertyService(TablePropertyMapper tablePropertyMapper) {
        this.tablePropertyMapper = tablePropertyMapper;
    }

    public IPage<TablePropertyVO> getTableProperties(int pageNum, int pageSize, String tableName) {
        Page<TablePropertyVO> page = new Page<>(pageNum, pageSize);
        return tablePropertyMapper.getTableProperties(page, tableName);
    }
}
~~~

#### 8、利用异或完成无第三方变量的两数数值交换

> 异或 (^)：无进位相加

**特性**

* 1、0^N=N	N^N=0
* 2、满足交换律和结合律

~~~java
// 无第三方变量的数值交换
// 0^N=N 和交换律结合律的应用
// 注意：交换的值不能是同一块空间，如果是会被擦除为0，即i!=j
// 交换arr[i]和arr[j]的值
piblic ststic void (int[] arr, int i, int j){
    // ==> arr[i] = arr[i].val ^ arr[j].val; arr[j] = arr[j].val;
    arr[i] = arr[i] ^ arr[j];
    
    // ==> arr[i] = arr[i].val ^ arr[j].val; arr[j] = arr[i].val ^ (arr[j].val ^ arr[j].val) ==> arr[i].val ^ 0 ==> arr[i].val;
    arr[j] = arr[i] ^ arr[j];
    
    // ==> arr[i] = arr[i].val ^ arr[j].val ^ arr[i].val ==> arr[j].val ^ 0 ==> arr[j].val; arr[j] = arr[i].val;
    arr[i] = arr[i] ^ arr[j];
}
~~~

#### 9、求中点防止溢出

> 一般来说，对于在int范围内的L和R使用 `(L+R)/2` 是有溢出风险的。

**解决方案**

> 转换为 `L+((R-L)/2)`

> 除以二可以转换为更加高效的右移运算 `L+((R-L)>>1)`

#### 10、正则表达式（?）问号为特殊字符

> 在正则表达式（Regular Expression）中，一些特殊字符需要进行额外的转义。

在使用Java的split("?") 部分涉及到正则表达式。问号（?）是正则表达式中的特殊字符，表示前一个字符出现0次或1次。因此，如果想按照字面的问号字符进行分割，需要对它进行转义。可以使用两个反斜杠对问号进行转义

> split("\\?")

#### 11、Spring Boot中，三种不同类型的参数（请求路径、请求体、请求体）一般是用不同的注解来处理的

1、请求路径参数（URI）（URL路径中的参数）: 使用@PathVariable注解来获取路径中的参数。例如：

~~~java
@GetMapping("/greet/{name}")
public String greetPath(@PathVariable String name) {
    return "Hello, " + name;
}
~~~

请求URL可能是：**http://localhost:8080/greet/John**

2、请求查询参数（Query Parameters）（URL问号后面的参数）: 使用@RequestParam注解获取查询参数。例如：

~~~java
@GetMapping("/greet")
public String greetQuery(@RequestParam(name = "name", defaultValue = "Guest") String name) {
    return "Hello, " + name;
}
~~~

**@RequestParam主要是用于处理请求查询参数，与请求头和请求体的参数是不同的。**
请求URL可能是：**http://localhost:8080/greet?name=John**

3、请求体（Body）参数（主要用于POST、PUT请求）: 使用@RequestBody注解。例如：

~~~java
@PostMapping("/greet")
public String greetBody(@RequestBody Map<String, String> body) {
    String name = body.getOrDefault("name", "Guest");
    return "Hello, " + name;
}

// 还可以直接用对象接收
// 对于请求体中的参数，你可以直接在方法参数列表中加入要绑定的Java对象。Spring 会自动将请求体中的参数绑定到这个对象上。这通常用于POST和PUT请求。
@PostMapping("/users")
public String createUser(@RequestBody User user) {
    // 处理逻辑
    return "User created: " + user.getName();
}
~~~

在这个例子中，请求体可能是一个JSON对象：{"name": "John"}
请求URL是：**http://localhost:8080/greet**

4、请求头（Headers）参数: 使用@RequestHeader注解可以直接获取请求头中的参数。例如：

~~~java
@GetMapping("/greet")
public String greetHeader(@RequestHeader(name = "User-Agent") String userAgent) {
    return "User-Agent: " + userAgent;
}
~~~

5、复杂的请求参数：如果一个请求可能包含多种参数，你还可以使用HttpServletRequest对象来手动获取

~~~java
@RequestMapping("/info")
public String info(HttpServletRequest request) {
    String id = request.getParameter("id");
    String header = request.getHeader("User-Agent");
    // 处理逻辑
    return "Info";
}
~~~

```
@RequestMapping("/greet")
public String greet(@RequestParam(name = "name", defaultValue = "Guest") String name) {
    return "Hello, " + name;
}

在Spring Boot中，@RequestMapping注解默认是可以处理所有类型的HTTP请求，包括GET、POST、PUT、DELETE等。这意味着，如果你没有明确指定请求类型，那么任何类型的请求都可以被处理。

关于@RequestParam，它用于获取查询参数，这些参数通常出现在URL的查询字符串中，与HTTP方法（GET、POST等）无关。

例如，以下两种请求都可以被上面的代码块处理：

GET请求：http://localhost:8080/greet?name=John
POST请求：http://localhost:8080/greet（在请求体或者请求头中没有关于name的信息，但是可以通过表单或者以其他方式将name=John作为查询参数发送）
如果你想明确处理特定类型的HTTP请求，可以使用@GetMapping、@PostMapping、@PutMapping、@DeleteMapping等专用的注解。

@GetMapping("/greet")
public String greetGet(@RequestParam(name = "name", defaultValue = "Guest") String name) {
    return "Hello, " + name + " from GET request";
}

@PostMapping("/greet")
public String greetPost(@RequestParam(name = "name", defaultValue = "Guest") String name) {
    return "Hello, " + name + " from POST request";
}

在这个例子中，GET和POST请求有各自对应的处理方法。

综上，@RequestParam是与HTTP方法无关的，它只与请求参数有关。而HTTP方法的处理可以通过专用的注解或者通过在@RequestMapping里指定method属性来区分。
```

#### 12、Mybatis的动态sql编写语法和案例

MyBatis 提供了多种动态 SQL 的编写方式，允许根据不同条件生成不同的 SQL 语句。

1. <if> 元素

用于进行条件判断。如果条件为 true，则会包含内部的 SQL 片段。

XML 配置示例：

~~~xml
<select id="findUsers" resultType="User">
    SELECT * FROM users
    WHERE 1=1
    <if test="username != null">
        AND username = #{username}
    </if>
    <if test="age != null">
        AND age = #{age}
    </if>
</select>
~~~

2. <choose> <when> <otherwise> 元素

类似于 Java 里面的 switch-case-default。

XML 配置示例：

~~~xml
<select id="findUser" resultType="User">
    SELECT * FROM users
    <choose>
        <when test="username != null">
            WHERE username = #{username}
        </when>
        <when test="email != null">
            WHERE email = #{email}
        </when>
        <otherwise>
            WHERE id = -1
        </otherwise>
    </choose>
</select>
~~~

3. <foreach> 元素

用于遍历集合。

XML 配置示例：

~~~xml
<select id="findUsersByIds" resultType="User">
    SELECT * FROM users
    WHERE id IN
    <foreach item="id" index="index" collection="ids" open="(" separator="," close=")">
        #{id}
    </foreach>
</select>
~~~

4. <trim> <where> <set> 元素

用于自定义 SQL 片段。

<trim>：自定义前缀和后缀。

<where>：自动处理 WHERE 子句。

<set>：用于 UPDATE 语句，自动处理字段更新。

XML 配置示例：

~~~xml
<update id="updateUser" parameterType="User">
    UPDATE users
    <set>
        <if test="username != null">
            username = #{username},
        </if>
        <if test="age != null">
            age = #{age},
        </if>
    </set>
    WHERE id = #{id}
</update>
~~~

5. <bind> 元素

用于创建一个变量，并将其绑定到一个表达式上。

XML 配置示例：

~~~xml
<select id="findUserByName" resultType="User">
    <bind name="pattern" value="'%' + username + '%'"/>
    SELECT * FROM users WHERE username LIKE #{pattern}
</select>
~~~


在 MyBatis 中，可以通过多种方式向 Mapper 方法传递参数。

1. 单一参数
如果只有一个参数，可以直接在 SQL 中使用 #{} 指定参数名称。

Java Mapper 接口：

java
Copy code
public interface UserMapper {
    User findUserById(Integer id);
}
XML 配置：

~~~xml
<select id="findUserById" resultType="User">
    SELECT * FROM users WHERE id = #{id}
</select>
~~~
2. 多个参数
如果有多个参数，可以使用 @Param 注解指定参数名。

Java Mapper 接口：

~~~java
public interface UserMapper {
    List<User> findUsers(@Param("username") String username, @Param("age") Integer age);
}
~~~

XML 配置：

~~~xml
<select id="findUsers" resultType="User">
    SELECT * FROM users
    WHERE username = #{username} AND age = #{age}
</select>
~~~

3. 使用 Map
当有很多参数或者参数是动态的时候，可以使用 Map 类型。

Java Mapper 接口：

java
Copy code
public interface UserMapper {
    List<User> findUsersByMap(Map<String, Object> params);
}
XML 配置：

~~~xml
<select id="findUsersByMap" resultType="User">
    SELECT * FROM users
    WHERE username = #{params.username} AND age = #{params.age}
</select>
~~~

4. 使用 POJO
还可以直接使用一个 POJO 对象作为参数。

Java Mapper 接口：

~~~java
public interface UserMapper {
    List<User> findUsersByUser(User user);
}
~~~

XML 配置：

~~~xml
<select id="findUsersByUser" resultType="User">
    SELECT * FROM users
    WHERE username = #{username} AND age = #{age}
</select>
~~~

5. 使用 @Param 和 POJO 组合
当需要传递单个值和 POJO 对象时，可以使用 @Param 和 POJO 的组合。

Java Mapper 接口：

~~~java
public interface UserMapper {
    List<User> findUsersByParams(@Param("type") int type, User user);
}
~~~

XML 配置：

~~~xml
<select id="findUsersByParams" resultType="User">
    SELECT * FROM users
    WHERE type = #{type} AND username = #{user.username} AND age = #{user.age}
</select>
~~~

以上几种方式根据不同的业务场景选择使用，使代码更加清晰、可维护。

