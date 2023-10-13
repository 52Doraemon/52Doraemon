#### 0、微服务结构

-----------------------------------
```
项目结构
├─jeecg-boot-parent（父POM： 项目依赖、modules组织）
│  ├─jeecg-boot-base-core（共通模块： 工具类、config、权限、查询过滤器、注解等）
│  ├─jeecg-module-demo    示例代码
│  ├─jeecg-module-system  System系统管理目录
│  │  ├─jeecg-system-biz    System系统管理权限等功能
│  │  ├─jeecg-system-start  System单体启动项目(8080）
│  │  ├─jeecg-system-api    System系统管理模块对外api
│  │  │  ├─jeecg-system-cloud-api   System模块对外提供的微服务接口
│  │  │  ├─jeecg-system-local-api   System模块对外提供的单体接口
│  ├─jeecg-server-cloud           --微服务模块
     ├─jeecg-cloud-gateway       --微服务网关模块(9999)
     ├─jeecg-cloud-nacos       --Nacos服务模块(8848)
     ├─jeecg-system-cloud-start  --System微服务启动项目(7001)
     ├─jeecg-demo-cloud-start    --Demo微服务启动项目(7002)
     ├─jeecg-visual
        ├─jeecg-cloud-monitor       --微服务监控模块 (9111)
        ├─jeecg-cloud-xxljob        --微服务xxljob定时任务服务端 (9080)
        ├─jeecg-cloud-sentinel     --sentinel服务端 (9000)
        ├─jeecg-cloud-test           -- 微服务测试示例（各种例子）
           ├─jeecg-cloud-test-more         -- 微服务测试示例（feign、熔断降级、xxljob、分布式锁）
           ├─jeecg-cloud-test-rabbitmq     -- 微服务测试示例（rabbitmq）
           ├─jeecg-cloud-test-seata          -- 微服务测试示例（seata分布式事务）
           ├─jeecg-cloud-test-shardingsphere    -- 微服务测试示例（分库分表）
```


#### 1、Nacos启动报：Nacos Server did not start because dumpservice bean construction failure : No DataSource set

通过在网上查找资料，把数据库连接url中的：connectTimeout=1000&socketTimeout=3000更改为connectTimeout=10000&socketTimeout=30000即可启动成功，但是我遇到的不是连接等待超时的问题，由于创建的数据库是在云服务器上，一因此默认的安全机制使得该数据库智能被服务器本地访问，需要进入宝塔面板设置数据库分为权限即可访问。

#### 2、微服务的子服务结构
-----------------------------------
```
子服务结构
├──yummy-module-chat（聊天模块）
|  ├──yummy-module-chat-api
|  ├──yummy-module-chat-biz
|  ├──yummy-module-hat-start（resources文件夹及相关配置放在这里）
```
-----------------------------------

在一个微服务的子服务中，通常会包含以下三个部分及其作用：

* **API（Application Programming Interface）**： API部分负责定义和公开子服务的接口。它定义了可以通过网络或其他方式调用的方法和数据结构。API提供给其他服务或客户端使用，以便与子服务进行交互。其他服务或客户端通过调用API来请求子服务的功能。

> API的作用是提供了一种标准化的访问方式，使得其他服务和客户端可以根据预定义的接口规范与子服务进行通信。API的设计需要考虑到安全性、数据传输格式、参数校验等方面。

* **BIZ（Business Logic）**： BIZ部分是子服务的核心业务逻辑。它包含了具体的业务处理代码，负责实现子服务的功能和业务规则。BIZ部分通常对外部的请求进行处理，并进行相应的数据操作、计算、验证、处理等。

> BIZ的作用是处理业务逻辑，并产生相应的结果。它根据API部分接收到的请求进行处理，并调用其他模块或服务进行数据存取、计算、验证、消息发送等。BIZ部分需要确保数据的一致性、安全性和业务的正确性。

* **START（Subservice Startup）**： START部分是子服务的启动模块。它负责子服务的初始化和启动工作。包括读取配置文件、建立数据库连接、设置日志等。

> START的作用是执行一些子服务启动时必要的操作，确保子服务能够正确运行。START部分负责加载和初始化子服务的各个模块，设置必要的上下文和资源，以及启动服务监听网络请求或消息。

**以上三个部分共同组成了一个完整的微服务的子服务，各自的职责和作用相对独立，但又相互配合，共同实现子服务的功能和运行。**

#### 3、微服务子服务配置文件（yml）

~~~yml
# 服务器的相关设置
server:
  # 应用的监听端口号
  port: 7010
# Spring框架相关的设置
spring:
  # 指定了应用的名称
  application:
    name: jeecg-wgp
  cloud:
    # 配置Nacos作为配置中心的相关设置
    nacos:
      # config部分定义Nacos配置中心的地址(server-addr)、配置组(group)、命名空间(namespace)、用户名(username)和密码(password)等
      config:
        server-addr: '@config.server-addr@'
        group: '@config.group@'
        namespace: '@config.namespace@'
        username: '@config.username@'
        password: '@config.password@'
      # discovery部分用于将Nacos作为服务注册与发现中心的相关设置。其中的server-addr指定Nacos的地址，namespace指定命名空间，username和password指定登录Nacos的用户名和密码，group指定服务的分组。
      discovery:
        server-addr: ${spring.cloud.nacos.config.server-addr}
        namespace: '@config.namespace@'
        username: '@config.username@'
        password: '@config.password@'
        group: '@config.group@'
  # config.import部分用于导入外部配置文件，这里使用Nacos作为配置中心，并根据不同的配置文件后缀来加载对应的配置文件。
  config:
    import:
      - optional:nacos:jeecg.yaml
      - optional:nacos:jeecg-wgp-@profile.name@.yaml
      - optional:nacos:jeecg-normal-@profile.name@.yaml
~~~

#### 4、分布式任务调度平台xxl-job初使用

因为在服务中写定时任务，当服务发布多份实例时，会多次执行，可能造业务数据混乱，把定时任务执行次数分离出来，由分布式任务调度平台去控制.

**xxl-job的工作原理**

1.部署xxl-job服务端，由该服务端控制业务服务里面的定时任务执行次数和时间

2.在业务服务中引入xxl-job客户端依赖，指定xxl-job服务端的地址，相当于向服务端注册本服务的IP+PORT，这样xxl-job服务端才能调用业务服务里面的定时任务

3.在xxl-job服务端提供的Web管理后台控制任务执行时间

xxl-job需要mysql数据库存储任务配置信息，因此需要先把数据库和表数据初始化好，官网提供了脚步

