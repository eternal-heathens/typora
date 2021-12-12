##  动态规划

* 能解决的问题：1. 问题的答案依赖于规模，即问题的所有答案构成了一个数列

  							2. 大规模问题的答案可以由小规模问题的答案递推得到

* 步骤：1. 建立状态转移方程

  ![image-20200629225541682](F:\Typora数据储存\基础课程\算法.assets\image-20200629225541682.png)

  			2. 缓存并复用以往结果

  ![image-20200629225615859](F:\Typora数据储存\基础课程\算法.assets\image-20200629225615859.png)

  3. 按顺序从小往大计算

     			1. ![image-20200629225641117](F:\Typora数据储存\基础课程\算法.assets\image-20200629225641117.png)

![](F:\Typora数据储存\基础课程\算法.assets\image-20200629225805335.png)

![image-20200629225823568](F:\Typora数据储存\基础课程\算法.assets\image-20200629225823568.png)

![image-20200629225837395](F:\Typora数据储存\基础课程\算法.assets\image-20200629225837395.png)

![image-20200629225845138](F:\Typora数据储存\基础课程\算法.assets\image-20200629225845138.png)

![image-20200629225955638](F:\Typora数据储存\基础课程\算法.assets\image-20200629225955638.png)

* “*”的符号意义为前面一位元素的个数，若为0，连同前面的符号也会忽略

1. 查看是否可以用动态规划解决，匹配时可分为string的第i个字符和pattern的第j个字符匹配与string(0~(i-1))和patter(0-(j-1))匹配的与操作，似乎可以建立状态转移方程，f [i] [j] (表示 s 的前 i个字符与 p中的前 j个字符是否能够匹配)  与 s[i] 和p[j]的关系,m(i,j)为单个字符 ![[公式]](https://www.zhihu.com/equation?tex=P_i) 和 ![[公式]](https://www.zhihu.com/equation?tex=S_j) 的匹配结果。

2. 尝试分析出状态转移方程：

   1. 根据p中含有特殊匹配字符".","*"分为三种情况

      * 当p[j] = 普通字符时
      * 当p[j] = "."时
      * 当p[j] = "*"时

   2. **当p[j] = 普通字符时 **

      * 若s[i] = p[j],f[i] [j]  = f[i-1] [j-1] &m(i,j) = f[i-1] [j-1]

      **当p[j] = "."时**

      * 若s[i] = p[j],f[i] [j]  = f[i-1] [j-1] &m(i,j) = f[i-1] [j-1]

      **当p[j] = "*"时 **

      * 若s[i]!=p[j-1],f[i] [j] = f[i] [j-2] （此时“*”与其前面的字符为empty，即匹配零个前面的那一个元素）

      * 若s[i]==p[j-1]or p[j-1] == '.'

        > 1. f[i] [j] = f[i-1] [j] ,即若i前面的字符串已经和p[j]及前面的字符串匹配，又s[i]==p[j-1],p[j] = "*",即s[i]元素为可以连续重复，若重复n个，则f[i] [j] ~f[i+n] [j]  均为1
        > 2. or f[i] [j] = f [i] [j-1] ，s[i]仅匹配p[j-1]一个
        > 3. or f[i] [j] = f[i] [j-2] ，匹配0个

```
/**
 * leetcode官方代码有误
*/
class Solution {
    public boolean isMatch(String s, String p) {
        int m = s.length();
        int n = p.length();

        boolean[][] f = new boolean[m + 1][n + 1];
        f[0][0] = true;
        for (int i = 0; i <= m; ++i) {
            for (int j = 1; j <= n; ++j) {
                if (p.charAt(j - 1) == '*') {
                    f[i][j] = f[i][j - 2];
                    if(f[i][j] == true) continue;                 
                    if (matches(s, p, i, j - 1)) {
                        f[i][j] = f[i][j-1] || f[i - 1][j];
                    }
                }
                else {
                    if (matches(s, p, i, j)) {
                        f[i][j] = f[i - 1][j - 1];
                    }
                }
            }
        }
        return f[m][n];
    }

    public boolean matches(String s, String p, int i, int j) {
        if (i == 0) {
            return false;
        }
        if (p.charAt(j - 1) == '.') {
            return true;
        }
        return s.charAt(i - 1) == p.charAt(j - 1);
    }
}

```

1. 定义状态

2. 状态转移方程

3. 初始化

4. 考虑输出

5. 优化

   