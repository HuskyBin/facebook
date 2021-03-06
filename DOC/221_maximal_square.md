# 221. Maximal Square (medium)

##### 说明

* Given a 2D binary matrix filled with 0's and 1's, find the largest square containing only 1's and return its area.
* example:

1 0 1 0 0
1 0 **1 1** 1
1 1 **1 1** 1
1 0 0 1 0
return 4.

##### 分析

* method 1: DP
    * time: O(mn)
    * space: O(mn)
* method 2: DP
    * time O(mn)
    * space: O(max(m,n))

##### 思路

* DP
* method 1:
    * 根据`dp[i][j] = (Math.min(Math.min(dp[i-1][j], dp[i][j-1]), dp[i-1][j-1])) + 1;`比如下图的蓝色的2（这个位置`dp[i][j]`为2），因为因为左（`dp[i][j-1]`），上（`dp[i-1][j]`），左上（`dp[i-1][j-1]`）都是1（注意三个红色的圈）

![square](https://lh3.googleusercontent.com/AzOW4IgoCUektMwWVLTMPkYURxUjaabY1wUTSXf87tTbaFqv_V1dxJTxyQEmYnz6NHvDzjH5oj5YLPa4L_1pCMKmCLENGcbpmVEEcdVHcFpktC48bg-E6xLbk1Vxuk3UaC2b8YAMAS62yjOsf1ylPdhdxz29MOWszGXGWMcc3mB3QYXtjJLdezGl06LESQgqFMq7w5YUyfMXpPUuhsl_rdFg-M3VprFUlbBdOYLJZJ4Dmes9BiOtnF0sx7b-AbKilP_6MlN6f350xLNSVM-6OY7DdgJeIMiBnW5jh_qGA-xxhOXLzYv2PKmYXzic6tm2gdTKjfI1sF3I1o5B02c76dFhnjY2RLEbc4ao9HKrTpqelZhzJQA1kv8JqV8JsP-a0nt9T_XOqOGWyICrc5KBjp3ooBOybc44au8if_LQGChNHjOW6zQL1DB-BnRxSi1opAAYAh3hg0EGY8Yg-_jx-8hjOCW_iWKml4nJbFvL-KMFSOx_BffQa9yOaPhINCG7gqiXRcu_YYiCAUdBwV_O3aVwCtGbMvCNoGGR0NBZOou9tx5rKzlDi1RBgOLyg5Q=s240-w240-h216-no)

* method 2: 优化DP到O(n)
    * 注意到`dp[i][j] = (Math.min(Math.min(dp[i-1][j], dp[i][j-1]), dp[i-1][j-1])) + 1`，要更新`dp[i][j]`仅使用了三个其他数值，所以可以将2D dp变成1D：在`Math.min(...)`中用`dp[j]`与`dp[j-1]`来代表`dp[i-1][j]`与`dp[i-1][j-1]`，引入**temp1与temp2去存`dp[i][j-1]`**
    * 相比method 1，在inner loop的if条件后面，**多了一个else case**，将dp[j]，即method 1中的dp[i][j]变为0，因为原来的2D array 本身就被initialize成了0，而method 2中，相当于`dp[i][j]`一开始是有`dp[i-1][j]`的值

```java
class Solution {
    public int maximalSquare(char[][] matrix) {
        if (matrix.length == 0) return 0;
        int m = matrix.length, n = matrix[0].length, result = 0;
        int[][] dp = new int[m+1][n+1];
        for (int i = 1; i <= m ; i++) {
            for (int j = 1; j <= n; j++) {
                // check the upper left corner
                if (matrix[i-1][j-1] == '1') {
                    dp[i][j] = (Math.min(Math.min(dp[i-1][j], dp[i][j-1]), dp[i-1][j-1])) + 1;
                    result = Math.max(dp[i][j], result); // update result
                }
            }
        }
        return result * result;
    }
}
```

method 2:

```java
class Solution {
    public int maximalSquare(char[][] matrix) {
        if (matrix.length == 0) return 0;
        // 用temp1, temp2去存dp[i][j-1] 
        int m = matrix.length, n = matrix[0].length, result = 0, temp1 = 0, temp2 = 0;
        int[] dp = new int[n+1];
        for (int i = 1; i <= m ; i++) {
            temp1 = 0;
            for (int j = 1; j <= n; j++) {
                temp2 = dp[j];// 相当于存dp[i][j]
                // check the upper left corner
                if (matrix[i-1][j-1] == '1') {
                    // Math.min中的dp[j]与dp[j-1]相当于dp[i-1][j]与dp[i-1][j-1]
                    dp[j] = Math.min(Math.min(dp[j], dp[j-1]), temp1) + 1;
                    result = Math.max(dp[j], result); // update result
                } else {
                    dp[j] = 0;
                }
                temp1 = temp2; // 在下一个loop的时候，temp1相当于dp[i][j-1]
            }
        }
        return result * result;
    }
}
```

