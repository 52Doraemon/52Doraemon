#### 0、Tomcat每次接收到Http协议报文时的执行流程

**1. 建立连接**

当Tomcat服务器启动时，会初始化一组线程（通常在一个线程池中），监听特定的TCP端口。这通常是由org.apache.coyote.AbstractProtocol的实现类（如Http11NioProtocol）负责的。

**2. 读取请求**

当一个HTTP请求到达，一个空闲的工作线程（通常从线程池中获取）将被分配来处理这个请求。这一部分通常由org.apache.coyote.Request对象负责表示。

**3. 解析请求**

接下来，Tomcat开始解析HTTP请求。它首先解析请求行（请求方法、URL、HTTP版本）和请求头。这通常是由org.apache.coyote.http11.Http11InputBuffer来完成的。

~~~java
// 在Http11Processor的process方法中，进行请求头的解析
RequestInfo rp = request.getRequestProcessor();
rp.setStage(org.apache.coyote.Constants.STAGE_PARSE);
inputBuffer.parseRequestLine();
~~~

**4. 映射到Servlet**

一旦请求被解析，Tomcat将开始将请求映射到合适的Servlet。这是由org.apache.catalina.core.StandardWrapper和org.apache.catalina.core.StandardContext等类负责的。

**5. 调用Servlet**

找到合适的Servlet后，Tomcat将**创建HttpServletRequest和HttpServletResponse对象**，并调用Servlet的service()方法。

~~~java
// 在org.apache.catalina.core.ApplicationFilterChain的internalDoFilter方法中
if (servlet != null) {
    if (Globals.IS_SECURITY_ENABLED) {
        // ...
        PrivilegedAction<Void> pa = new PrivilegedAction<Void>() {
            @Override
            public Void run() {
                servlet.service(request, response);
                return null;
            }
        };
        AccessController.doPrivileged(pa);
    } else {
        servlet.service(request, response);
    }
}
~~~

**6. 响应处理**

Servlet处理完成后，生成的响应将通过HttpServletResponse对象返回。Tomcat将这些信息转换成原始的HTTP响应格式并写回到客户端。

**7. 结束**

最后，所有的资源都会被释放，包括输入输出缓冲区、请求和响应对象等。线程返回到线程池，等待处理下一个请求。
