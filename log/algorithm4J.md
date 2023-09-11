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
