#### 0、双亲委派模型（Parent-Delegation Model）

> 双亲委派模型（Parent-Delegation Model）是 Java 类加载机制中的一个重要组成部分，主要用于解决类的唯一性问题以及安全性问题。
> 双亲委派模型是 Java 类加载器的一种工作模式，通过这种工作模式，Java 虚拟机将类文件加载到内存中，这样就保证了 Java 程序能够正常的运行起来。

**作用：**

* 唯一性：确保一个类在 JVM 运行时只有一个实例。(疑问：确保一个类在 JVM 运行时只有一个实例是什么意思，一个类不是可以new多个实例对象吗)
* 安全性：防止用户自定义的类覆盖 Java 核心库的类。

**原理：**

* 当一个类加载器收到类加载请求时，它会先将这个请求委托给父类加载器，一直递归到最顶层的启动类加载器；只有当父类加载器无法完成这个加载请求时（即在它的类路径下没有找到对应的类），子类加载器才会尝试自己去加载。

**生动形象的例子：**

假设你家里有三代人：祖父、父亲和你。有一天，你想吃一个苹果，你会先问你的父亲有没有苹果。如果父亲有，你就直接吃了；如果父亲没有，父亲会去问祖父。祖父如果有，就会给父亲，父亲再给你；如果连祖父也没有，你的父亲就会自己去买一个再给你。

在这个例子中：

* “吃苹果”相当于“加载一个类”。

* 你、你的父亲和你的祖父分别代表不同层级的类加载器。

* “有没有苹果”相当于判断各个类加载器的类路径中是否有我们需要的类。

**通过这种方式，保证了每个苹果（类）都是唯一的，并且都是从最“权威”的地方（如 JDK 自带的类库）获得的，除非真的没有，才会自己去“买”（加载）。这就是双亲委派模型的作用和优势。**

> 确保一个类在 JVM 运行时只有一个实例是什么意思，一个类不是可以new多个实例对象吗

```
确保一个类在 JVM 运行时只有一个实例，这里的“实例”指的是类的元数据（Class 对象），而不是通过 new 关键字创建的对象实例。

在 Java 中，每个被加载的类都有一个对应的 java.lang.Class 对象，用来存储这个类的元数据（如方法定义、字段定义等）。这个 Class 对象在 JVM 内存中是唯一的。也就是说，无论一个类被实例化多少次（即通过 new 创建了多少对象），它对应的 Class 对象都是单一的。

当我们说“确保一个类在 JVM 运行时只有一个实例”时，意思是这个 Class 对象在内存中是唯一的。这样做有几个好处：

节省内存：如果多个类加载器都加载了同一个类，会导致多份元数据存储在内存中，浪费内存资源。

安全性：确保系统核心库不会被随意替换，从而避免了安全问题。

类型检查和强制转换的准确性：由于 Class 对象是唯一的，类型检查和强制类型转换就变得更加可靠。

所以，当我们谈到“确保一个类只有一个实例”时，是在讲这个类的元数据（Class 对象），而不是通过这个类创建的对象实例。
```

双亲委派模型（Parent-Delegation Model）这个中文翻译问题：

```
双亲委派模型（Parent-Delegation Model）这个中文翻译确实可能不够直观或生动。这个名词更多地是一个技术术语，用以描述 Java 类加载器（Class Loader）的一个特定工作机制。在这个模型中，类加载器在尝试加载一个类之前，会首先将这个任务委托给其“父类”加载器，一直递归到最顶层的启动类加载器（Bootstrap ClassLoader）。只有当父类加载器不能完成这个任务时，子类加载器才会尝试自己去加载这个类。

如果要用更生动的语言描述这个模型，可以将其比喻为一个“逐级上报”的或者“逐级审核”的流程。例如，在一个公司内，普通员工在做出某个重要决策前会先咨询其直属上司，直属上司如果觉得自己没有权力或能力做出决定，会继续上报，一直到有权做出决策的最高层管理人员。这样做主要是为了确保决策的合规性和准确性。

这种模型在多层次、多角色的组织结构中是非常常见的，因此双亲委派模型也就可以被理解为一种“逐级审核”或“逐级批准”的机制。这种比喻或许更加贴近人们的日常经验，更容易理解。
```