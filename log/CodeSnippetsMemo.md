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
