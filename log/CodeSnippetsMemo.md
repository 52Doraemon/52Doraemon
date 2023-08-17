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
