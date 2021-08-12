# 1. 矩阵中的路径

[NowCoder](https://www.nowcoder.com/practice/c61c6999eecb4b8f88a98f66b273a3cc?tpId=13&tqId=11218&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**：

> 判断在一个矩阵中是否存在一条包含某字符串所有字符的路径。路径可以从矩阵中的任意一个格子开始，每一步可以在矩阵中向上下左右移动一个格子。如果一条路径经过了矩阵中的某一个格子，则该路径不能再进入该格子。

[![img](https://camo.githubusercontent.com/048b24cdea1efb377ece3a7e9f441170c7f09268/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f31646231633765612d303434332d343738622d386466392d3765333362313333366363342e706e67)](https://camo.githubusercontent.com/048b24cdea1efb377ece3a7e9f441170c7f09268/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f31646231633765612d303434332d343738622d386466392d3765333362313333366363342e706e67)

**解题思路：**

>  本问题是典型的矩阵搜索问题，可使用 **深度优先搜索（DFS）+ 剪枝** 解决。 

**算法原理**：

> **深度优先搜索：** 可以理解为暴力法遍历矩阵中所有字符串可能性。DFS 通过递归，先朝一个方向搜到底，再回溯至上个节点，沿另一个方向搜索，以此类推。
>
> **剪枝：** 在搜索中，遇到 这条路不可能和目标字符串匹配成功 的情况（例如：此矩阵元素和目标字符不同、此元素已被访问），则应立即返回，称之为 可行性剪枝 。

**算法剖析：**

> **递归参数：** 当前元素在矩阵 board 中的行列索引 i 和 j ，当前目标字符在 word 中的索引 k 。
>
> **终止条件：**
>
> 1. 返回 false ： ① 行或列索引越界 **或** ② 当前矩阵元素与目标字符不同 **或** ③ 当前矩阵元素已访问过 （③ 可合并至 ② ） ；
>
> 2. 返回 true ： 字符串 word 已全部匹配，即 k = len(word) - 1 。
>
> **递推工作：**
>
> 1. **标记当前矩阵元素：** 将 board[i][j] 值暂存于变量 tmp ，并修改为字符 '/' ，代表此元素已访问过，防止之后搜索时重复访问。
> 2. **搜索下一单元格：** 朝当前元素的 上、下、左、右 四个方向开启下层递归，使用 或 连接 （代表只需一条可行路径） ，并记录结果至 res 。
> 3. **还原当前矩阵元素：** 将 temp 暂存值还原至 boarde[ i ] [ j ]元素。
>
> **回溯返回值：** 返回 res ，代表是否搜索到目标字符串。

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        if (board == null || board.length == 0 || board[0].length == 0 || word == null) 
            return false;
        char[] words = word.toCharArray();
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[i].length; j++) {
                if (backtracking(board, i, j, words, 0)) return true;
            }
        }
        return false;
    }

    public boolean backtracking(char[][] board, int i, int j, char[] words, int k) {
        if (i >= board.length || i < 0 || j >= board[i].length || j < 0 || k >= words.length) return false;
        if (board[i][j] != words[k]) return false;
        if (k == words.length - 1) return true;
        char temp = board[i][j];
        board[i][j] = '\\';
        boolean res = backtracking(board, i-1, j, words, k+1)
                   || backtracking(board, i+1, j, words, k+1)
                   || backtracking(board, i, j-1, words, k+1)
                   || backtracking(board, i, j+1, words, k+1);
        board[i][j] = temp;
        return res;
    }
}
```

这道题大家都知道是DFS的题，关键是怎么可以快速并且正确的写出，是本题解讨论的重点。
首先解释一下递归函数。
递归函数：就是当前处理的问题是什么，并且下一次在规模减小的情况下处理相同的问题。
比如此题：当前处理的问题是：判断字符串str[0 ... len]是否在mat中匹配，显然下一次递归处理的问题是:如果str[0]已经匹配，则判断字符串str[1 ... len]是否在mat中匹配。

这里先给出一个我认为比较清晰的DFS模板：

```c++
dfs(){
    // 第一步，检查下标是否满足条件
    // 第二步：检查是否被访问过，或者是否满足当前匹配条件
    // 第三步：检查是否满足返回结果条件
    // 第四步：都没有返回，说明应该进行下一步递归
    // 标记
    dfs(下一次)
    // 回溯
}  
main() {
    for (对所有可能情况) {
        dfs()
    }
}
```

上面每步的顺序都不能颠倒。

所以，对于这道题来说，首先dfs()的参数是什么，返回值是什么。c++
可以像这样：

```c++
// i, j 表示mat中的位置， pos表示当前正在匹配的字符串str的下标
// 成功返回整个字符串str, 则返回true, 否则返回false
bool dfs(int i, int j, int pos, char *str);
```

```java
public class Solution {
    public boolean hasPath(char[] matrix, int rows, int cols, char[] str) {
        if (matrix == null || rows < 0 || cols < 0 || str == null || str.length <= 0) 
            return false;
        char[][] matrixs = new char[rows][cols];
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                matrixs[i][j] = matrix[(i * cols) + j];
            }
        }
        for (int i = 0; i < rows; i++) {
            for (int j =0; j< cols; j++) {
                if (backtracking(matrixs, i, j, str, 0)) return true;
            }
        }
        return false;
    }
    
    public boolean backtracking(char[][] matrixs, int i, int j, char[] str, int k) {
        if (i < 0 || i >= matrixs.length || j < 0 || j >= matrixs[i].length || k >= str.length) 
            return false;
        if (str[k] != matrixs[i][j]) return false;
        if (k == str.length - 1) return true;
        char temp = matrixs[i][j];
        matrixs[i][j] = '\\';
        boolean res = backtracking(matrixs, i, j+1, str, k+1)
                   || backtracking(matrixs, i, j-1, str, k+1)
                   || backtracking(matrixs, i+1, j, str, k+1)
                   || backtracking(matrixs, i-1, j, str, k+1);
        matrixs[i][j] = temp;
        return res;
    }
}
```

# 2. 机器人的运动范围

[NowCoder](https://www.nowcoder.com/practice/6e5207314b5241fb83f2329e89fdecc8?tpId=13&tqId=11219&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**：

地上有一个 m 行和 n 列的方格。一个机器人从坐标 (0, 0) 的格子开始移动，每一次只能向左右上下四个方向移动一格，但是不能进入行坐标和列坐标的数位之和大于 k 的格子。

例如，当 k 为 18 时，机器人能够进入方格 (35,37)，因为 3+5+3+7=18。但是，它不能进入方格 (35,38)，因为 3+5+3+8=19。请问该机器人能够达到多少个格子？

**解题思路**：

> 使用深度优先搜索（Depth First Search，DFS）方法进行求解。回溯是深度优先搜索的一种特例，它在一次搜索过程中需要设置一些本次搜索过程的局部状态，并在本次搜索结束之后清除状态。而普通的深度优先搜索并不需要使用这些局部状态，虽然还是有可能设置一些全局状态。 

```java
dfs(){

    // 第一步，检查下标

    // 第二步：检查是否被访问过，或者是否满足当前匹配条件

    // 第三步：检查是否满足返回结果条件

    // 第四步：都没有返回，说明应该进行下一步递归
    // 标记
    dfs(下一次)
    // 回溯
}  
int main() {
    dfs(0, 0);
}
```

```java
public class Solution {
    private int res = 0;
    public int movingCount(int threshold, int rows, int cols) {
        if (threshold <= 0 || rows < 0 || cols < 0) return 0;
        boolean[][] marked = new boolean[rows][cols];
        int[][] digitSum = initDigitSum(rows, cols);
        dfs(threshold, marked, digitSum, 0, 0);
        return res;
    }
    public void dfs(int threshold, boolean[][] marked, int[][] digitSum, int row, int col) {
        // 检查下标
        if (row < 0 || row >= digitSum.length || col < 0 || col >= digitSum[row].length) return;
        // 检查是否被访问过，或者 检查是否满足返回结果条件
        if (marked[row][col] || digitSum[row][col] > threshold) return;
        // 代码走到这里，说明当前坐标符合条件
        res++;
        marked[row][col] = true; // 标记
        // 进行下一步递归
        dfs(threshold, marked, digitSum, row, col+1);
        dfs(threshold, marked, digitSum, row, col-1);
        dfs(threshold, marked, digitSum, row+1, col);
        dfs(threshold, marked, digitSum, row-1, col);
    }
    public int[][] initDigitSum(int rows, int cols) {
        int[] digitSumOne = new int[Math.max(rows, cols)];
        for (int i = 0; i < digitSumOne.length; i++) {
            int n = i;
            while(n > 0) {
                digitSumOne[i] += n % 10;
                n /= 10;
            }
        }
        int[][] digitSum = new int[rows][cols];
        for (int i = 0; i < rows; i++) {
            for (int j = 0; j < cols; j++) {
                digitSum[i][j] = digitSumOne[i] + digitSumOne[j];
            }
        }
        return digitSum;
    }
}
```

# 3. 排列

\46. Permutations (Medium)

[Leetcode](https://leetcode.com/problems/permutations/description/) / [力扣](https://leetcode-cn.com/problems/permutations/description/)

```
[1,2,3] have the following permutations:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

```java
class Solution {
    public List<List<Integer>> permute(int[] nums) {
        List<List<Integer>> permutes = new ArrayList<>();
        List<Integer> permuteList = new ArrayList<>();
        if (nums == null || nums.length == 0) return permutes;
        boolean[] marked = new boolean[nums.length];
        dfs(permutes, permuteList, nums, marked);
        return permutes;
    }
    
    public void dfs(List<List<Integer>> permutes, List<Integer> permuteList, int[] nums, boolean[] marked) {
        if (permuteList.size() == nums.length) {
            permutes.add(new ArrayList<>(permuteList));
            return;
        }
        for (int i = 0; i < nums.length; i++) {
            if (marked[i]) continue;
            marked[i] = true;
            permuteList.add(nums[i]);
            dfs(permutes, permuteList, nums, marked);
            permuteList.remove(permuteList.size() - 1);
            marked[i] = false;
        }
    }
}
```

# 4. 字符串的排列

[NowCoder](https://www.nowcoder.com/practice/fe6b651b66ae47d7acce78ffdd9a96c7?tpId=13&tqId=11180&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 输入一个字符串，按字典序打印出该字符串中字符的所有排列。例如输入字符串 abc，则打印出由字符 a, b, c 所能排列出来的所有字符串 abc, acb, bac, bca, cab 和 cba。

**解题思路**

```java
class Solution {
    public String[] permutation(String s) {
        if (s == null || s.length() == 0) return new String[0];
        char[] chars  = s.toCharArray();
        Arrays.sort(chars); // 通过排序来去除重复排列
        boolean[] marked = new boolean[chars.length];
        StringBuffer sb = new StringBuffer();
        ArrayList<String> res = new ArrayList<>();
        dfs(res, sb, chars, marked);
        String[] str = new String[res.size()];
        return res.toArray(str);
    }

    private void dfs(ArrayList<String> res, StringBuffer sb, char[] chars, boolean[] marked) {
        if (sb.length() == chars.length) {
            res.add(sb.toString());
            return;
        }
        for (int i = 0; i < chars.length; i++) {
            if (marked[i]) continue;
            if (i != 0 && chars[i] == chars[i - 1] && marked[i-1]) continue; // 保证不重复
            marked[i] = true;
            sb.append(chars[i]);
            dfs(res, sb, chars, marked);
            sb.deleteCharAt(sb.length()-1);
            marked[i] = false;
        }
    }
}
```

# 5. 二叉树中和为某一值的路径

[NowCoder](https://www.nowcoder.com/practice/b736e784e3e34731af99065031301bca?tpId=13&tqId=11177&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**：

> 输入一颗二叉树和一个整数，打印出二叉树中结点值的和为输入整数的所有路径。路径定义为从树的根结点开始往下一直到叶结点所经过的结点形成一条路径。
>
> 下图的二叉树有两条和为 22 的路径：10, 5, 7 和 10, 12
>
> [![img](https://camo.githubusercontent.com/0b93fd53d2d8f26cd1b46983a8d09b66562814cd/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f65643737623065362d333864392d346133342d383434662d3732346633666661326331322e6a7067)](https://camo.githubusercontent.com/0b93fd53d2d8f26cd1b46983a8d09b66562814cd/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f65643737623065362d333864392d346133342d383434662d3732346633666661326331322e6a7067)

**解题思路**

```java
import java.util.ArrayList;
import java.util.Queue;
import java.util.LinkedList;
public class Solution {
    public ArrayList<ArrayList<Integer>> FindPath(TreeNode root,int target) {
        ArrayList<ArrayList<Integer>> res = new ArrayList<>();
        dfs(res, new ArrayList<Integer>(), root, target);
        return res;
    }
    
    public void dfs(ArrayList<ArrayList<Integer>> res, ArrayList<Integer> list, TreeNode root, int target) {
        if (root == null) return;
        if (root.val > target) return;
        list.add(root.val);
        target -= root.val;
        // root为叶子节点，且target==0,则该路径可以；
        if (root.left == null && root.right == null && target == 0) {
            res.add(new ArrayList<Integer>(list));
        }
        // root不为叶子节点，且target>0
        dfs(res, list, root.left, target);
        dfs(res, list, root.right, target);

        list.remove(list.size()-1);
    }
}
```

