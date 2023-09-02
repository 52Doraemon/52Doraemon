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

**应用场景**

**“硬币找零”问题**

> “硬币找零”问题是一个经典的优化问题，主要目标是使用最少数量的硬币来凑出特定的金额。给定一组硬币的面值（每种面值可以使用任意数量的硬币）和一个总金额，问题是找到组成这个金额所需的最少硬币数量。

这个问题具有“重叠子问题”和“最优子结构”的性质，因此通常使用动态规划来解决。

