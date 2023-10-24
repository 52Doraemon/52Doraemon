#### 1、动态规划算法

> 动态规划（Dynamic Programming，简称 DP）是一种用于解决最优化问题的算法技术。它广泛应用在各种计算问题中，包括但不限于序列对齐、路径查找、组合优化和资源分配等。

动态规划通常用于解决具有以下性质的问题：

* 重叠子问题（Overlapping Subproblems）： 同一个问题可以分解成一组相互重叠的子问题。

* 最优子结构（Optimal Substructure）： 问题的最优解可以从其子问题的最优解构建而来。

**动态规划基本步骤：**

1、问题分解： 将原问题分解成一系列子问题。

2、定义状态： 使用变量（通常是数组或矩阵）来表示子问题的解。

3、初始化状态： 设置边界条件，也就是最基本子问题的解。

4、状态转移： 根据子问题之间的关系，找出状态转移方程。

5、求解： 通常从最小的子问题开始，逐步解决更大的子问题，直至达到原问题。

6、回溯（可选）： 在某些情况下，我们可能需要从存储的状态中回溯以找出最优解的具体构成。

**算法模板：**

~~~java
// 一个使用一维数组进行动态规划的简单 Java 示例，该示例解决了“硬币找零”问题。
public class DynamicProgramming {
    public static int coinChange(int[] coins, int amount) {
        int[] dp = new int[amount + 1];
        Arrays.fill(dp, amount + 1);
        dp[0] = 0;
        for (int coin : coins) {
            for (int i = coin; i <= amount; i++) {
                dp[i] = Math.min(dp[i], dp[i - coin] + 1);
            }
        }
        return dp[amount] > amount ? -1 : dp[amount];
    }

    public static void main(String[] args) {
        int[] coins = {1, 2, 5};
        int amount = 11;
        System.out.println("Minimum coins needed: " + coinChange(coins, amount));
    }
}
~~~

**复杂度分析：**

动态规划的时间复杂度和空间复杂度通常取决于子问题的数量和每个子问题需要的计算量。

例如，在硬币找零问题中，时间复杂度是 O(amount * n)，其中 n 是硬币种类数；空间复杂度是 O(amount)。

在像最长公共子序列（LCS）这样的问题中，如果两个字符串的长度分别是 m 和 n，那么时间和空间复杂度通常都是 O(m * n)。

通过使用动态规划，我们通常能够**将问题从指数级复杂度降低到多项式级复杂度，从而实现有效求解。**

**动态规划的作用：**

动态规划（DP）算法正是为了解决这种问题而设计的。通过将已解决的子问题的答案存储在一个表格（通常是一个数组或二维数组）中，当相同的子问题再次出现时，我们可以直接从表格中查找答案，而无需重新计算。

这样，动态规划帮助我们避免了多次解决相同子问题的低效，从而提高了算法的运行速度。这也就是为什么 LCS 问题中存在“重叠子问题”，并且为什么动态规划是解决这个问题的有效方法。

**应用场景**

**“硬币找零”问题**

> “硬币找零”问题是一个经典的优化问题，主要目标是使用最少数量的硬币来凑出特定的金额。给定一组硬币的面值（每种面值可以使用任意数量的硬币）和一个总金额，问题是找到组成这个金额所需的最少硬币数量。

这个问题具有“重叠子问题”和“最优子结构”的性质，因此通常使用动态规划来解决。

#### 2、敏感词控制算法实现

> DFA（确定有限自动机）、Trie树（字典树）或KMP算法

1、**DFA（Deterministic Finite Automaton，确定有限自动机）**

DFA是用于识别正则或者模式的基础机器模型。它由一个有限的状态集合、一个字母表（输入符号类型）、一个转移函数、一个开始状态和一个或多个接受状态组成。DFA是唯一确定的，意味着对于每一对（状态、输入符号）都有唯一确定的转换到下一状态。

应用场景：

    字符串匹配
    词法分析
    网络协议的实现
    Trie树（字典树）

DFA算法的核心类：

~~~java
// 导入所需的类库
import java.util.HashMap;
import java.util.Map;
import java.util.Set;

// 类定义
public class DFASensitiveWordFilter {
    // 敏感词Map，用于构建DFA
    private final Map<Object, Object> sensitiveWordMap;

    // 构造函数，接受一个敏感词Set
    public DFASensitiveWordFilter(Set<String> sensitiveWords) {
        // 初始化sensitiveWordMap
        sensitiveWordMap = new HashMap<>();
        // 遍历所有的敏感词
        for (String word : sensitiveWords) {
            Map<Object, Object> currentMap = sensitiveWordMap;
            // 遍历敏感词的每一个字符
            for (char ch : word.toCharArray()) {
                Object value = currentMap.get(ch);
                // 如果这个字符没有对应的子Map，就创建一个
                if (value == null) {
                    Map<Object, Object> newMap = new HashMap<>();
                    newMap.put("isEnd", false);
                    currentMap.put(ch, newMap);
                    currentMap = newMap;
                } else {
                    // 否则，移动到该字符对应的子Map
                    currentMap = (Map<Object, Object>) value;
                }
            }
            // 设置“isEnd”为true，表示这是一个完整的敏感词
            currentMap.put("isEnd", true);
        }
    }

    // 检查一个文本中是否包含敏感词
    public boolean containsSensitiveWord(String text) {
        // 从文本的每一个字符开始检查
        for (int i = 0; i < text.length(); i++) {
            Map<Object, Object> currentMap = sensitiveWordMap;
            // 从当前字符开始，遍历之后的字符
            for (int j = i; j < text.length(); j++) {
                // 逐一检查字符是否构成敏感词
                currentMap = (Map<Object, Object>) currentMap.get(text.charAt(j));
                if (currentMap == null) {
                    // 如果没有匹配的，直接跳出
                    break;
                }
                // 如果找到一个完整的敏感词（即"isEnd"为true），则返回true
                if ((boolean) currentMap.get("isEnd")) {
                    return true;
                }
            }
        }
        // 如果没有找到任何敏感词，则返回false
        return false;
    }

    // 将文本中的敏感词替换为屏蔽字符串
    public String replaceSensitiveWords(String text) {
        StringBuilder result = new StringBuilder();
        for (int i = 0; i < text.length(); i++) {
            Map<Object, Object> currentMap = sensitiveWordMap;
            int start = i; // 敏感词开始的位置
            int length = 0; // 敏感词的长度
            boolean isEnd = false;
    
            for (int j = i; j < text.length(); j++) {
                currentMap = (Map<Object, Object>) currentMap.get(text.charAt(j));
                if (currentMap == null) {
                    break;
                }
                length++; // 敏感词的长度加1
                if ((boolean) currentMap.get("isEnd")) {
                    isEnd = true;
                    break;
                }
            }
    
            if (isEnd) { // 找到一个完整的敏感词
                result.append("***");
                i = start + length - 1; // 跳过这个敏感词
            } else {
                result.append(text.charAt(i));
            }
        }
    
        return result.toString();
    }
}
~~~

在Spring Boot Controller中引入这个敏感词过滤器：

~~~java
// 引入Spring Web的注解和类
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RestController;

// 引入Java的HashSet和Set接口，用于存储敏感词
import java.util.HashSet;
import java.util.Set;

// @RestController注解表示这是一个RESTful Web Service的Controller
@RestController
public class CommentController {
    // 创建一个final的DFASensitiveWordFilter对象，用于后续的敏感词检测
    private final DFASensitiveWordFilter sensitiveWordFilter;

    // 构造函数
    public CommentController() {
        // 创建一个HashSet对象存储敏感词，这里仅为示例，实际场景可能需要从数据库或配置文件读取
        Set<String> sensitiveWords = new HashSet<>();
        sensitiveWords.add("敏感词1");
        sensitiveWords.add("敏感词2");
        
        // 使用敏感词集合初始化DFASensitiveWordFilter对象
        this.sensitiveWordFilter = new DFASensitiveWordFilter(sensitiveWords);
    }

    // @PostMapping注解表示这个方法用于处理HTTP POST请求，路径为"/comment"
    @PostMapping("/comment")
    // 使用@RequestBody注解告诉Spring将HTTP请求体转换为Java String对象
    public String postComment(@RequestBody String comment) {
        // 使用DFASensitiveWordFilter的containsSensitiveWord方法检查评论中是否包含敏感词
        if (sensitiveWordFilter.containsSensitiveWord(comment)) {
            // 如果评论中包含敏感词，返回提示信息
            return "评论包含敏感词，请重新输入";
        }
        // 在这里，你可以添加其他业务逻辑，比如将评论保存到数据库
        // 如果一切正常，返回评论成功的消息
        return "评论成功";
    }
}
~~~

如果不希望外界通过new来创建DFASensitiveWordFilter的实例，有几种方式可以实现：

* Singleton Pattern（单例模式）: 将DFASensitiveWordFilter的构造函数设置为private，并提供一个public static方法来获取实例。

* Spring的@Component注解: 如果你使用Spring框架，可以通过@Component、@Service等注解让Spring容器管理DFASensitiveWordFilter的实例。

* Factory Pattern（工厂模式）: 使用一个工厂类来创建DFASensitiveWordFilter的实例。



2、**Trie树，也称为前缀树或字典树**

Trie树通常用于搜索具有相同前缀的字符串。一个Trie树通常保存大量的字符串，并能在O(n)的时间复杂度内查找任意字符串或前缀（n为字符串长度）。

应用场景：

    字符串搜索
    前缀匹配
    拼写检查
    KMP算法（Knuth-Morris-Pratt）

3、**KMP算法**

KMP算法是一种高效的字符串搜索（子串查找）算法，由Donald Knuth、Vaughan Pratt和James H. Morris发明。相对于简单的暴力算法，KMP算法能在O(N)时间内完成字符串匹配任务（N为文本长度）。

KMP算法预处理模式串（子串）并构建一个“部分匹配表”（也称为“失败函数”），用于在不匹配时跳过尽可能多的字符。

应用场景：

    文本搜索
    数据挖掘
    DNA序列匹配
    
这些算法各自有其优势和应用场景，根据实际问题来选择适合的算法非常重要。例如，在实现敏感词过滤时，DFA算法由于其确定性和高效性通常是首选。但如果你需要快速查找或插入大量字符串，Trie树可能是更好的选择。如果你的任务是在一个长文本中找到一个特定的子串，KMP算法可能是最适合的。

#### 3、链表

1.反转单向双向链表
时间O(N),空间O(1)
有换头head=f(head)

2.打印两个有序链表的公共部分
时间O(N),空间O(1)
两两比较，谁小谁移动，相等打印，打印完共同移动，有一个越界停

3.链表是否为回文结构

4.将单向链表按某值划分成左边小，中间相等，右边大的形式，要求调整后的，所有的数字的相对顺序和调整前一样。

5.复制含有随机指针节点的链表

面试时链表解题的方法论：
1)对于笔试，不用太在乎空间复杂度，一切为了时间复杂度。

2)对于面试，时间复杂度依然放在第一位，但是一定要找到空间最省的方法。

**重要技巧：**
1)额外数据结构记录（哈希表等）
2)快慢指针

逆序--＞栈
一半--＞快慢指针，快指针走2步，慢指针走1步，快指针走到终点时，慢指针就是终点，根据具体的需求快慢指针需要做小的定制

#### 4、树

1、树的遍历方式（先序、中序、后序）

**先序遍历：头左右**

**中序遍历：左头右**

**后序遍历：左右头**

遍历的方式：

1、递归

根据递归序，每个节点会在递归方法三次回到自己的节点，在不同位置输出就是不同的遍历方法。

2、非递归（需要用到栈结构）

> 一切的递归都可以改造为非递归

递归需要用到栈，因此用非递归实现递归方法时可以使用栈，即是模拟栈。

2、树的深度还有宽度

先序求深度

队列求宽度（俗称层序遍历）

3、搜索二叉树：对于每一颗子树来说，它的左数节点都比它小，右数节点都比它大。（一颗经典的搜索二叉树里面可以认为它是没有重复值的）

java的stack.add和stack.push

在Java中，Stack 类继承自 Vector 类，因此，Stack 对象不仅拥有栈（Stack）特有的方法，如 push 和 pop，还继承了 Vector 的方法，包括 add。

**stack.push(E item)**

push 方法用于将元素压入栈的顶部。它修改了栈的状态。push 方法遵循后入先出（LIFO, Last-In-First-Out）原则。

**stack.add(E element)**

add 方法用于将元素添加到向量（即 Stack）的尾部。它也会修改栈的状态，但这是因为它改变了 Vector，而 Stack 是 Vector 的一个子类。如果 Stack 作为栈使用，通常不推荐使用 add 方法，因为这样可能会导致代码的语义模糊。

总结：虽然 add 和 push 在 Stack 类中都会将元素添加到集合的尾部，但 push 方法更符合栈的语义和操作原则。


java的linkedlist用作队列

在Java中，LinkedList 类实现了 Queue 接口，因此你可以使用 LinkedList 的对象作为队列（Queue）来使用。

poll(E e)：方法用于从队列中移除和返回头部（队首）元素。如果队列为空，这个方法将返回 null。

push(E e)：在列表的前端添加元素。该方法使 LinkedList 也可以当作栈来使用，但如果把它作为队列使用，通常不会用到这个方法。

add(E e)：在列表（因此也在队列）的末尾添加元素。当添加成功时，返回 true。如果队列受到容量限制并且当前已满，则抛出 IllegalStateException。

offer(E e)：在队列的尾部插入一个元素。此方法与 add(E e) 类似，但当使用固定大小的队列（不适用于 LinkedList）并且队列已满时，offer 方法会返回 false，而 add 会抛出一个异常。

#### 5、HashMap 和 HashSet、LinkedHashMap 和 LinkedHashSet

在 Java 中，HashMap 和 HashSet 的实现基本上是无序的，这意味着它们存储元素的顺序并不是元素被插入的顺序。如果你需要维护插入顺序，你可以使用 LinkedHashMap 和 LinkedHashSet，这两个类在内部使用链表来维护元素的插入顺序。

**HashMap 和 HashSet**

HashMap: 它不保证映射的顺序；特别是它不保证该顺序恒久不变。

HashSet: 同样不保证 set 的迭代顺序；特别是它不保证该顺序恒久不变。

**LinkedHashMap 和 LinkedHashSet**

LinkedHashMap: 它维护了一个运行于所有条目的双重链接列表。此链接列表定义了迭代顺序，该迭代顺序可以是插入顺序或访问顺序。

LinkedHashSet: 与 LinkedHashMap 类似，它也维护了一个运行于所有条目的双重链接列表，从而定义了元素的插入顺序。

如果你需要按某种特定顺序（例如，排序顺序）来存储元素，你还可以使用如 TreeMap 或 TreeSet 的数据结构，它们实现了 SortedMap 和 SortedSet 接口，可以确保元素是按照某种特定顺序来排序的。

#### 6、红黑树

红黑树（Red-Black Tree）是一种自平衡的二叉搜索树，它具有以下特点：

节点颜色：每个节点被标记为红色或黑色。

根节点：根节点是黑色的。

叶子节点：叶子节点（NIL节点或空节点）是黑色的。

红色节点性质：红色节点的子节点必须是黑色的（也就是不能有两个连续的红色节点）。

从任意节点到其每个叶子的路径上的黑色节点数量是相同的：这个性质确保了树的平衡。

从数据结构的角度来看，红黑树是一个二叉搜索树，其每个节点包含以下信息：

键值：节点存储的关键值或数据。

颜色：节点的颜色，可以是红色或黑色。

指针：指向父节点、左子节点和右子节点的指针。

基于这些特点和数据结构，红黑树确保了以下性质：

从根节点到叶子节点的最长路径不超过最短路径的两倍，确保了树的平衡。

红黑树的查找、插入和删除操作的时间复杂度都是O(log n)，其中n是树中节点的数量。

红黑树的平衡性质使得它适合用作各种数据结构，如集合、映射和多种应用程序中的索引结构。

总之，红黑树是一种重要的自平衡二叉搜索树，通过颜色和节点链接的特定规则来确保平衡。这使得它在计算机科学中被广泛应用，尤其在实现各种高性能数据结构和算法中。

堆的性质：

堆中某个节点的值总是不大于或不小于其父节点的值；
堆总是一棵完全二叉树。
