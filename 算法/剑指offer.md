# 3. 数组中重复的数字
**题目链接**

[牛客网](https://www.nowcoder.com/practice/623a5ac0ea5b4e5f95552655361ae0a8?tpId=13&tqId=11203&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)



**题目描述**

在一个长度为 n 的数组里的所有数字都在 0 到 n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字是重复的，也不知道每个数字重复几次。请找出数组中任意一个重复的数字。

```java
Input:
{2, 3, 1, 0, 2, 5}

Output:
2
```



**解法一：**

利用哈希表来解决这个问题，从头到尾扫描这个数组，遇到一个数，先用O(1)的时间判断该数是否再哈希表中，如果在，就说明有重复数字，返回；如果没有，就将该数放入哈希表中，继续扫描下一个元素。

```java
public boolean duplicate(int numbers[],int length,int[] duplication) {
    if (numbers == null || length <= 0) return false;

    HashMap<Integer, Integer> map = new HashMap<>();
    for(int i = 0; i < length; i++) {
        if (map.containsKey(numbers[i]) == true) {
            duplication[0] = numbers[i];
            return true;
        } else {
            map.put(numbers[i], i);
        }
    }
    return false;
}
```



**解法二：**

要求时间复杂度 O(N)，空间复杂度O(1)。因此不能使用排序的方法，也不能使用额外的标记数组。 

**对于这种数组元素在[0,n-1]范围的问题，可以将值为i的元素调整到第 i 个位置上进行求解。** 

本题要求找出重复的数字，因此在调整的过程中，如果第 i 位置上已经有一个值为 i 的元素，就可以知道 i 值重复。
**详细过程：** 从头到尾依次扫描这个数组中的每个数字，如果扫描到第 i 个位置的数字时（设该数字为 m ）：

1. 比较 m 和 i 是否相等，如果相等，则接着扫描下一个元素；
2. 如果 m 和 i 不相等，则比较 m 是否和第 m 个位置上的元素相等，如果相等，则说明有重复的数字，即为 m；
3. 如果 m 和第 m 个位置上的元素不相等，则将第 i 个位置上的数字（即 m）和第 m 个位置上的数字相交换，将 m 换到属于它的位置，接着再重复比较；
4. 每一次扫描就将一个元素放到它该属的位置，扫描到最后一个元素就相当于将数组按照0 ~ n-1的顺序排序了（如果数组中没有重复元素）


```java
public boolean duplicate(int numbers[],int length,int[] duplication) {
    if (numbers == null || length <= 0) return false;
    for (int i = 0; i < length; i++) {
        int m = numbers[i];
        if (m == i) {
            continue;
        } else if (m == numbers[m]) {
            duplication[0] = m;
            return true;
        } else {
            numbers[i] = numbers[m];
            numbers[m] = m;
        }
    }
    return false;
}
```



该方法也是按照：将值为 i 的元素调整到第 i 个位置上进行求解 的思路：（但需要O(n)的空间复杂度）

- 创建一个长度为length的辅助数组，然后逐一把原数组的每个数字复制到辅助数组中，例如，原数组中元素值为m，就将其复制到辅助数组中第m个位置。这样很容易发现是哪个数字重复。

```java
//boolean只占一位，所以还是比较省的
public boolean duplicate(int numbers[], int length, int[] duplication) {
    boolean[] k = new boolean[length];
    for (int i = 0; i < k.length; i++) {
        if (k[numbers[i]] == true) {
            duplication[0] = numbers[i];
            return true;
        }
        k[numbers[i]] = true;
    }
    return false;
}
```





# 4. 二维数组中的查找

**题目链接**

[牛客网](https://www.nowcoder.com/practice/abc3fe2ce8e146608e868a70efebf62e?tpId=13&tqId=11154&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

给定一个二维数组，其每一行从左到右递增排序，从上到下也是递增排序。给定一个数，判断这个数是否在该二维数组中。

```
Consider the following matrix:
[
  [1,   4,  7, 11, 15],
  [2,   5,  8, 12, 19],
  [3,   6,  9, 16, 22],
  [10, 13, 14, 17, 24],
  [18, 21, 23, 26, 30]
]

Given target = 5, return true.
Given target = 20, return false.
```



**解题思路**

在该二维数组中的每个数：比它大的在右边或下边，比它小的在左边或者上边。

因此，从**右上角**开始查找，就可以根据 target 和当前元素的大小关系来缩小查找区间：

1. 当右上角第一个元素等于target时，就返回；
2. 如果当前元素大于target，则说明target在当前元素的右边，则需剔除当前元素所在列；
3. 如果当前元素小于target，则说明target在当前元素的下边，则需剔除当前元素所在行。

也可以从**左下角**开始查找。

```java
public boolean findNumberIn2DArray(int[][] matrix, int target) {
    // matrix[0].length 必须写在matrix.length后面，因为如果矩阵没有元素，则matrix[0].length会报错
    if (matrix == null || matrix.length <= 0 || matrix[0].length <= 0) return false;
    int rows = matrix.length;
    int cols = matrix[0].length;
    int r = 0, c = cols-1;
    while (r <= rows-1 && c >= 0) {
        if (matrix[r][c] == target) return true;
        else if (matrix[r][c] < target) r++;
        else c--;
    }
    return false;
}
```





# 5. 替换空格

**题目链接**

[牛客网](https://www.nowcoder.com/practice/4060ac7e3e404ad1a894ef3e17650423?tpId=13&tqId=11155&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

将一个字符串中的空格替换成 "%20"。

```
Input:
"A B"

Output:
"A%20B"
```



**思路一：**空间换时间

```java
class Solution {
    public String replaceSpace(String s) {
        int length = s.length();
        char[] array = new char[length*3];
        int size = 0;
        for (int i = 0; i < length; i++) {
            char c = s.charAt(i);
            if (c == ' ') {
                array[size++] = '%';
                array[size++] = '2';
                array[size++] = '0';
            } else {
                array[size++] = c;
            }
        }
        String newStr = new String(array, 0, size);
        return newStr;
    }
}

class Solution {
    public String replaceSpace(String s) {
        StringBuilder res = new StringBuilder();
        for(Character c: s.toCharArray()) {
            if (c == ' ') res.append("%20");
            else res.append(c);
        }
        return res.toString();
    }
}
```

**思路二：**

我们先遍历一次字符串，这样就能够统计出字符串中空格的个数，这样我们就可以由此计算出替换之后的字符串的长度。每替换一个空格，长度增加2。

>  在字符串尾部填充任意字符，使得字符串的长度等于替换之后的长度。因为一个空格要替换成三个字符（%20），因此当遍历到一个空格时，需要在尾部填充两个任意字符。 

令 P1 指向字符串原来的末尾位置，P2 指向字符串现在的末尾位置。P1 和 P2 从后向前遍历，当 P1 遍历到一个空格时，就需要令 P2 指向的位置依次填充 02%（注意是逆序的），否则就填充上 P1 指向字符的值。 

我们从字符串的后面开始复制和替换，因为**从后向前遍**是为了在改变 P2 所指向的内容时，不会影响到 P1 遍历原来字符串的内容。 也可以**减少移动次数**

```java
public class Solution {
    public String replaceSpace(StringBuffer str) {
    	int P1 = str.length() - 1;
        for (int i = 0; i <= P1; i++) {
            if (str.charAt(i) == ' ') {
                str.append(" ");
                str.append(" ");
            }
        }
        int P2 = str.length() - 1;
        while(P1 >= 0 && P2 > P1) {
            char c = str.charAt(P1--);
            if (c != ' ') {
                str.setCharAt(P2--, c);
            } else {
                str.setCharAt(P2--, '0');
                str.setCharAt(P2--, '2');
                str.setCharAt(P2--, '%');
            }
        }
        return str.toString();
    }
}
```

**注意：如果从前往后遍历需重复移动数字（或字符）多次，那么我们可以考虑从后往前遍历，这样可以减少移动的次数，从而提高效率。**

# 5.1 合并两个排序数组

**题目链接**

[Leetcode](https://leetcode-cn.com/problems/sorted-merge-lcci/)

**题目描述**

> 给定两个排序后的数组 A 和 B，其中 A 的末端有足够的缓冲空间容纳 B。 编写一个方法，将 B 合并入 A 并排序。
>
> 初始化 A 和 B 的元素数量分别为 m 和 n。
>

```
输入:
A = [1,2,3,0,0,0], m = 3
B = [2,5,6],       n = 3

输出: [1,2,2,3,5,6]

说明：A.length == n + m
```

```java
class Solution {
    public void merge(int[] A, int m, int[] B, int n) {
        int tail = m + n - 1;
        int p = m - 1;
        int q = n - 1;
        while(p >= 0 && q >= 0) {
            if (A[p] >= B[q]) {
                A[tail--] = A[p--];
            } else {
                A[tail--] = B[q--];
            }
        }
        if (p < 0) {
            while(q >= 0) {
                A[tail--] = B[q--];
            }
        }
    }
}
```

# 6. 从尾到头打印链表

**题目链接**

[牛客网](https://www.nowcoder.com/practice/d0267f7f55b3412ba93bd35cfa8e8035?tpId=13&tqId=11156&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 从尾到头反过来打印出每个结点的值。

**1. 使用递归：**

>  要逆序打印链表 1->2->3（3,2,1)，可以先逆序打印链表 2->3(3,2)，最后再打印第一个节点 1。而链表 2->3 可以看成一个新的链表，要逆序打印该链表可以继续使用求解函数，也就是在求解函数中调用自己，这就是递归函数。 

```java
import java.util.ArrayList;
public class Solution {
    public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
        if (listNode == null) return new ArrayList<Integer>(){};
        ArrayList<Integer> res = printListFromTailToHead(listNode.next);
        res.add(listNode.val);
        return res;
    }
}

class Solution {
    public int[] reversePrint(ListNode head) {
        if (head == null) return new int[]{};
        ArrayList<Integer> list = recur(head);
        int[] res = new int[list.size()];
        for(int i = 0; i < list.size(); i++) {
            res[i] = list.get(i);
        }
        return res;
    }
    public ArrayList<Integer> recur(ListNode head) {
        if (head == null) return new ArrayList<>(){};
        ArrayList<Integer> res = recur(head.next);
        res.add(head.val);
        return res;
    }
}
```

**2. 使用栈：**

>  栈具有后进先出的特点，在遍历链表时将值按顺序放入栈中，最后出栈的顺序即为逆序。 

```java
import java.util.ArrayList;
import java.util.Stack;
public class Solution {
    public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
        ArrayList<Integer> res = new ArrayList<>();
        if (listNode == null) return res;
        Stack<ListNode> stack = new Stack<>();
        while(listNode != null) {
            stack.push(listNode);
            listNode = listNode.next;
        }
        
        while(!stack.isEmpty()) {
            ListNode node = stack.pop();
            res.add(node.val);
        }
        return res;
    }
}
```

**3. 头插法：**

> 头插法顾名思义是将节点插入到头部：在遍历原始链表时，将当前节点插入新链表的头部，使其成为第一个节点。
>
> 使用头插法可以得到一个逆序的链表。
>
> 头结点和第一个节点的区别：
>
> - 头结点是在头插法中使用的一个额外节点，这个节点不存储值；
> - 第一个节点就是链表的第一个真正存储值的节点。

```java
/**
链表的操作需要维护后继关系，例如在某个节点 node1 之后插入一个节点 node2，我们可以通过修改后继关系来实现：
    node3 = node1.next;
    node2.next = node3;
    node1.next = node2;
*/

public ArrayList<Integer> printListFromTailToHead(ListNode listNode) {
    // 头插法构建逆序链表
    ListNode head = new ListNode(-1);
    while (listNode != null) {
        ListNode memo = listNode.next;
        listNode.next = head.next;
        head.next = listNode;
        listNode = memo;
    }
    // 构建 ArrayList
    ArrayList<Integer> ret = new ArrayList<>();
    head = head.next;
    while (head != null) {
        ret.add(head.val);
        head = head.next;
    }
    return ret;
}
```

# 7. 重建二叉树

[NowCoder](https://www.nowcoder.com/practice/8a19cbe657394eeaac2f6ea9b0f6fcf6?tpId=13&tqId=11157&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述：**

> 根据二叉树的前序遍历和中序遍历的结果，重建出该二叉树。假设输入的前序遍历和中序遍历的结果中都不含重复的数字。

前序遍历的第一个值为根节点的值，使用这个值将中序遍历结果分成两部分，左部分为树的左子树中序遍历结果，右部分为树的右子树中序遍历的结果。

```java
import java.util.HashMap;
public class Solution {
    public TreeNode reConstructBinaryTree(int [] pre,int [] in) {
        if (pre == null || in == null) return null;
        HashMap<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < in.length; i++) {
            map.put(in[i], i);
        }
        return buildTree(pre, 0, pre.length-1, in, 0, in.length-1, map);
    }
    public TreeNode buildTree(int[] pre, int pF, int pE, int[] in, int iF, int iE, HashMap<Integer, Integer> map) {
        if (pF > pE) return null;
        int rootVal = pre[pF];
        int rootIndex = map.get(rootVal);
        TreeNode root = new TreeNode(rootVal);
        int len = rootIndex - iF;
        root.left = buildTree(pre, pF+1, pF+len, in, iF, rootIndex-1, map);
        root.right = buildTree(pre, pF+len+1, pE, in, rootIndex+1, iE, map);
        return root;
    }
}

// leetcode
class Solution {
    public TreeNode buildTree(int[] preorder, int[] inorder) {
        if (preorder == null || preorder.length == 0) return null;
        HashMap<Integer, Integer> map = new HashMap<>();
        int length = inorder.length;
        for(int i = 0; i < length; i++) {
            map.put(inorder[i], i);
        }
        TreeNode root = buildTree(preorder, 0, length-1, inorder, 0, length-1, map);
        return root;
    }

    public TreeNode buildTree(int[] preorder, int preStart, int preEnd, 
                              int[] inorder, int inStart, int inEnd, 
                              HashMap<Integer,Integer> map) {
        if (preStart > preEnd) return null;
        int rootVal = preorder[preStart];
        TreeNode root = new TreeNode(rootVal);
        if (preStart == preEnd) {   
            return root;
        } else {
            int rootIndex = map.get(rootVal);
            int len = rootIndex - inStart;
            TreeNode leftSubTree = buildTree(preorder, preStart+1, preStart+len, inorder, inStart, rootIndex-1, map);
            TreeNode rightSubTree = buildTree(preorder, preStart+len+1, preEnd, inorder, rootIndex+1, inEnd, map);
            root.left = leftSubTree;
            root.right = rightSubTree;
            return root;
        }
    }
}
```

# 8. 二叉树的下一个结点

[NowCoder](https://www.nowcoder.com/practice/9023a0c988684a53960365b889ceaf5e?tpId=13&tqId=11210&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述：**

> 给定一个二叉树和其中的一个结点，请找出中序遍历顺序的下一个结点并且返回。注意，树中的结点不仅包含左右子结点，同时包含指向父结点的指针。

```java
public class TreeLinkNode {
    int val;
    TreeLinkNode left = null;
    TreeLinkNode right = null;
    TreeLinkNode next = null;
    TreeLinkNode(int val) {
        this.val = val;
    }
}
```

**解题思路：**

一个树的结点分为以下几种情况：

1. 有无左子树
2. 有无右子树
3. 自身是否是左子树
4. 自身是否是右子树

分三种情况：

1. 如果一个节点它有右子树，则它的下一个节点就是它的右子树的最左边的节点；
2. 如果一个节点它没有右子树，且其本身是左子树，则它的下一个节点就是它的父节点；
3. 如果一个节点它没有右子树，且其本身是右子树，则沿着父节点的指针一直向上遍历，直到找到一个是它父节点的左子节点的节点（该节点是左节点的节点）。

```java
public class Solution {
    public TreeLinkNode GetNext(TreeLinkNode pNode) {
        if (pNode.right != null) {
            TreeLinkNode node = pNode.right;
            while(node.left != null) node = node.left;
            return node;
        } else if (pNode.next != null){
            if (pNode.next.left == pNode) return pNode.next;
            if (pNode.next.right == pNode) {
                while(pNode.next != null && pNode.next.right == pNode) {
                    pNode = pNode.next;
                }
                return pNode.next;
            }
        }
        return null;
    }
}

public class Solution {
    public TreeLinkNode GetNext(TreeLinkNode pNode) {
        if (pNode.right != null) {
            TreeLinkNode node = pNode.right;
            while(node.left != null) node = node.left;
            return node;
        } else if (pNode.next != null){
            TreeLinkNode pCur = pNode;
            TreeLinkNode pParent = pNode.next;
            while(pParent != null && pCur == pParent.right) {
                pCur = pParent;
                pParent = pParent.next;
            }
            return pParent;

        }
        return null;
    }
}
```

**解法二：** 如果一个节点的右子树不为空，那么该节点的下一个节点是右子树的最左节点；  否则，向上找第一个左链接指向的树包含该节点的祖先节点。 

```java
public TreeLinkNode GetNext(TreeLinkNode pNode) {
    if (pNode.right != null) {
        TreeLinkNode node = pNode.right;
        while (node.left != null)
            node = node.left;
        return node;
    } else {
        while (pNode.next != null) {
            TreeLinkNode parent = pNode.next;
            if (parent.left == pNode)
                return parent;
            pNode = pNode.next;
        }
    }
    return null;
}
```

# 9. 用两个栈实现队列

[NowCoder](https://www.nowcoder.com/practice/54275ddae22f475981afa2244dd448c6?tpId=13&tqId=11158&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述：**

用两个栈来实现一个队列，完成队列的 Push 和 Pop 操作。

**解题思路：**

stack 1 用来处理push操作，stack 2 用来处理pop操作。

一个元素入队列，将其输入到 stack 1 中；

如果一个元素出队，需查看stack 2是否为空，如果不为空，则元素出栈；如果为空，需将stack 1中元素全部加载到stack 2中。

注意：stack 2不为空时，是不能将stack 1中元素加载到stack 2中；stack 1元素必须一次性全部加载到stack 2中。

```java
import java.util.Stack;

public class Solution {
    Stack<Integer> stack1 = new Stack<Integer>();
    Stack<Integer> stack2 = new Stack<Integer>();
    
    public void push(int node) {
        stack1.push(node);
    }
    
    public int pop() {
        if (!stack2.isEmpty()) return stack2.pop();
        else if (!stack1.isEmpty()){
            while(!stack1.isEmpty()){
                int node = stack1.pop();
                stack2.push(node);
            }
            return stack2.pop();
        } else {
            return -1;
        }
    }
}

Stack<Integer> in = new Stack<Integer>();
Stack<Integer> out = new Stack<Integer>();

public void push(int node) {
    in.push(node);
}

public int pop() throws Exception {
    if (out.isEmpty())
        while (!in.isEmpty())
            out.push(in.pop());

    if (out.isEmpty())
        throw new Exception("queue is empty");

    return out.pop();
}
```

# 10.1 斐波那契数列

[NowCoder](https://www.nowcoder.com/practice/c6c7742f5ba7442aada113136ddea0c3?tpId=13&tqId=11160&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述：**

> 求斐波那契数列的第 n 项，n <= 39。

[![img](https://camo.githubusercontent.com/32da1c76f658c155356fdd26a8165928a8be1e1f/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f34356265393538372d363036392d346162372d623961632d3834306462316135333734342e6a7067)](https://camo.githubusercontent.com/32da1c76f658c155356fdd26a8165928a8be1e1f/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f34356265393538372d363036392d346162372d623961632d3834306462316135333734342e6a7067)

**解题思路：**

递归：

```java
public class Solution {
    public int Fibonacci(int n) {
        if (n == 0) return 0;
        if (n == 1 || n == 2) return 1;
        return Fibonacci(n-1) + Fibonacci(n-2);
    }
}
```

动态规划：

> 递归是将一个问题划分成多个子问题求解，动态规划也是如此，但是动态规划会把子问题的解缓存起来，从而避免重复求解子问题。 

```java
public int Fibonacci(int n) {
    if (n <= 1) return n;
    int[] fib = new int[n + 1];
    fib[1] = 1;
    for (int i = 2; i <= n; i++)
        fib[i] = fib[i - 1] + fib[i - 2];
    return fib[n];
}

// 考虑到第 i 项只与第 i-1 和第 i-2 项有关，因此只需要存储前两项的值就能求解第 i 项，从而将空间复杂度由 O(N) 降低为 O(1)。
public int Fibonacci(int n) {
    if (n <= 1) return n;
    int pre2 = 0, pre1 = 1;
    int fib = 0;
    for (int i = 2; i <= n; i++) {
        fib = pre2 + pre1;
        pre2 = pre1;
        pre1 = fib;
    }
    return fib;
}

// 由于待求解的 n 小于 40，因此可以将前 40 项的结果先进行计算，之后就能以 O(1) 时间复杂度得到第 n 项的值。
public class Solution {
    private int[] fib = new int[40];
    public Solution() {
        fib[1] = 1;
        for (int i = 2; i < fib.length; i++)
            fib[i] = fib[i - 1] + fib[i - 2];
    }
    public int Fibonacci(int n) {
        return fib[n];
    }
}
```

# 10.2 矩形覆盖

[NowCoder](https://www.nowcoder.com/practice/72a5a919508a4251859fb2cfb987a0e6?tpId=13&tqId=11163&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述：**

> 我们可以用 2×1 的小矩形横着或者竖着去覆盖更大的矩形。请问用 n 个 2×1 的小矩形无重叠地覆盖一个 2×n 的大矩形，总共有多少种方法？

**解题思路**：

> 当 n 为 1 时，只有一种覆盖方法 ；
>
> 当 n 为 2 时，有两种覆盖方法 ；
>
> 要覆盖 2×n 的大矩形，可以先覆盖 2×1 的矩形，再覆盖 2×(n-1) 的矩形；或者先覆盖 2×2 的矩形，再覆盖 2×(n-2) 的矩形。而覆盖 2×(n-1) 和 2×(n-2) 的矩形可以看成子问题。该问题的递推公式如下： 

 ![img](https://camo.githubusercontent.com/ff88bb236b5fcb87ba55f6f612f52e5f350ee585/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f35303863366535322d396639332d343465642d623662392d6536393035306531343830372e6a7067) 

```java
public class Solution {
    public int RectCover(int target) {
        if (target <= 2) return target;
        int[] fib = new int[target+1];
        fib[1] = 1;
        fib[2] = 2;
        for (int i = 3; i <= target; i++) {
            fib[i] = fib[i-1] + fib[i-2];
        }
        return fib[target];
    }
}
```

# 10.3 跳台阶

[NowCoder](https://www.nowcoder.com/practice/8c82a5b80378478f9484d87d1c5f12a4?tpId=13&tqId=11161&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**：

> 一只青蛙一次可以跳上 1 级台阶，也可以跳上 2 级。求该青蛙跳上一个 n 级的台阶总共有多少种跳法。

**解题思路**：

> 当 n = 1 时，只有一种跳法；
>
> 当 n = 2 时，有两种跳法；
>
> 跳 n 阶台阶，可以先跳 1 阶台阶，再跳 n-1 阶台阶；或者先跳 2 阶台阶，再跳 n-2 阶台阶。而 n-1 和 n-2 阶台阶的跳法可以看成子问题，该问题的递推公式为： 

 ![img](https://camo.githubusercontent.com/ff88bb236b5fcb87ba55f6f612f52e5f350ee585/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f35303863366535322d396639332d343465642d623662392d6536393035306531343830372e6a7067) 

```java
public int JumpFloor(int n) {
    if (n <= 2) return n;
    int pre2 = 1, pre1 = 2;
    int result = 0;
    for (int i = 2; i < n; i++) {
        result = pre2 + pre1;
        pre2 = pre1;
        pre1 = result;
    }
    return result;
}
```

# 10.4 变态跳台阶

[NowCoder](https://www.nowcoder.com/practice/22243d016f6b47f2a6928b4313c85387?tpId=13&tqId=11162&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**：

> 一只青蛙一次可以跳上 1 级台阶，也可以跳上 2 级... 它也可以跳上 n 级。求该青蛙跳上一个 n 级的台阶总共有多少种跳法。

[![img](https://camo.githubusercontent.com/e18e8bcb0832de108c6322ee6c1b5b3cd5a76292/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f63643431316139342d333738362d346339342d396530382d6632383332306530313064352e706e67)](https://camo.githubusercontent.com/e18e8bcb0832de108c6322ee6c1b5b3cd5a76292/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f63643431316139342d333738362d346339342d396530382d6632383332306530313064352e706e67)

**解题思路**：

> 这里的 f(n) 代表的是n个台阶的跳法数。
>
> - n = 1时，只有1种跳法，f(1) = 1；
>
> - n = 2时，这里同样按照上面简单跳台阶的分析方式：
>
>   当第一步跳1阶时，那么剩下的1阶只用1种跳法，即f(1)；
>
>   当第一步跳2阶时，那就不剩下阶梯了，也即是f(0)种跳法，这里f(0)=1，代表一步跳到最高层。
>
>   得到：f(2) = f(1) + f(0)
>
> - n = 3时，分析同上：
>   当第一步跳1阶时，那么剩下的2阶只用f(2)种跳法，即f(2)；
>
>   当第一步跳2阶时，那剩下1个阶梯，只有f(1)种跳法，当第一步跳3阶时，那剩下0个阶梯，这种一步跳法即是f(0)。
>
>   得到f(3)=f(2)+f(1)+f(0)
>
> - n = n-1时，会得到：f(n-1) = f(0) + f(1) + f(2) + f(3) + … + f(n-2)
>
> - n = n时，会得到：f(n) = f(0) + f(1) + f(2) + f(3) + … + f(n-2) + f(n-1)
>
> -  综上：f(n) = f(0) + f(1) + f(2) + f(3) + … + f(n-2) + f(n-1) = f(n-1) + f(n-1)= 2 * f(n-1) 

```java
public class Solution {
    public int JumpFloorII(int target) {
        if (target <= 2) return target;
        int[] fib = new int[target+1];
        fib[1] = 1;
        for (int i = 2; i <= target; i++) {
            fib[i] = 2 * fib[i-1];
        }
        return fib[target];
    }
}
```

**动态规划**：

跳上 n-1 级台阶，可以从 n-2 级跳 1 级上去，也可以从 n-3 级跳 2 级上去...，那么

```
f(n-1) = f(n-2) + f(n-3) + ... + f(0)
```

同样，跳上 n 级台阶，可以从 n-1 级跳 1 级上去，也可以从 n-2 级跳 2 级上去... ，那么

```
f(n) = f(n-1) + f(n-2) + ... + f(0)
```

综上可得

```
f(n) - f(n-1) = f(n-1)
```

即

```
f(n) = 2*f(n-1)
```

所以 f(n) 是一个等比数列

```java
public int JumpFloorII(int target) {
    return (int) Math.pow(2, target - 1);
}
```

```java
public int JumpFloorII(int target) {
    int[] dp = new int[target];
    Arrays.fill(dp, 1);
    for (int i = 1; i < target; i++)
        for (int j = 0; j < i; j++)
            dp[i] += dp[j];
    return dp[target - 1];
}
```

# 11. 旋转数组的最小数字

[NowCoder](https://www.nowcoder.com/practice/9f3231a991af4f55b95579b44b7a01ba?tpId=13&tqId=11159&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**：

> 把一个数组最开始的若干个元素搬到数组的末尾，我们称之为数组的旋转。输入一个非递减排序的数组的一个旋转，输出旋转数组的最小元素。

[![img](https://camo.githubusercontent.com/a2df593632421741daf6bd74f8250cb04237b3df/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f30303338323034632d346238612d343261352d393231642d3038306636363734663938392e706e67)](https://camo.githubusercontent.com/a2df593632421741daf6bd74f8250cb04237b3df/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f30303338323034632d346238612d343261352d393231642d3038306636363734663938392e706e67)

**解题思路**：

**暴力方法**：

直接遍历一遍数组，即可找到最小值。但是本题的附加条件就没有用上。肯定不是面试官所期望的答案。

**二分查找**：

这种二分查找难就难在，arr[mid] 跟谁比.
我们的目的是：当进行一次比较时，一定能够确定答案在mid的某一侧。一次比较为 arr[mid]跟谁比的问题。
一般的比较原则有：

- 如果有目标值target，那么直接让arr[mid] 和 target 比较即可。 
- 如果没有目标值，一般可以考虑 **端点** 

这里我们把target 看作是**右端点**，来进行分析，那就要分析以下三种情况，看是否可以达到上述的目标。

1. 情况1，

   ```
   arr[mid] > target：4 5 6 1 2 3
   ```

   - arr[mid] 为 6， target为右端点 3， `arr[mid] > target`, 说明[first ... mid] 都是 >= target 的，因为原始数组是非递减，所以可以确定答案为 [mid+1...last]区间,所以 `first = mid + 1` 

2. 情况2，

   ```
   arr[mid] < target:5 6 1 2 3 4
   ```

   - arr[mid] 为 1， target为右端点 4， `arr[mid] < target`, 说明答案肯定不在[mid+1...last]，但是arr[mid] 有可能是答案,所以答案在[first, mid]区间，所以`last = mid`; 

3. 情况3，

   ```
   arr[mid] == target:
   ```

   - 如果是 1 0 1 1 1，arr[mid] = target = 1, 显然答案在左边 
   - 如果是 1 1 1 0 1，arr[mid] = target = 1, 显然答案在右边
     所以这种情况，不能确定答案在左边还是右边，那么就让last = last - 1;慢慢缩少区间，同时也不会错过答案。 

```java
import java.util.ArrayList;
public class Solution {
    public int minNumberInRotateArray(int [] array) {
        if (array == null || array.length == 0) return 0;
        int left = 0, right = array.length-1;
        while(left < right) {
            if (array[left] < array[right]) return array[left]; // 提前退出，此时数组没有旋转
            int mid = left + (right - left) / 2;
            if (array[mid] < array[right]) right = mid; // array[mid] 有可能是答案
            else if (array[right] < array[mid]) left = mid + 1;
            else right--;
        }
        return array[left];
    }
}
```

**误区**：那我们肯定在想，能不能把左端点看成target， 答案是不能。

**原因：**

- 情况1 ：1 2 3 4 5 ， arr[mid] = 3. target = 1, arr[mid] > target, 答案在mid 的左侧

- 情况2 ：3 4 5 1 2 ， arr[mid] = 5, target = 3, arr[mid] > target, 答案却在mid 的右侧

所以不能把左端点当做target

# 12. 矩阵中的路径

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

# 13. 机器人的运动范围

[NowCoder](https://www.nowcoder.com/practice/6e5207314b5241fb83f2329e89fdecc8?tpId=13&tqId=11219&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**：

地上有一个 m 行和 n 列的方格。一个机器人从坐标 (0, 0) 的格子开始移动，每一次只能向左右上下四个方向移动一格，但是不能进入行坐标和列坐标的数位之和大于 k 的格子。

例如，当 k 为 18 时，机器人能够进入方格 (35,37)，因为 3+5+3+7=18。但是，它不能进入方格 (35,38)，因为 3+5+3+8=19。请问该机器人能够达到多少个格子？

**解题思路**：

> 使用深度优先搜索（Depth First Search，DFS）方法进行求解。回溯是深度优先搜索的一种特例，它在一次搜索过程中需要设置一些本次搜索过程的局部状态，并在本次搜索结束之后清除状态。而普通的深度优先搜索并不需要使用这些局部状态，虽然还是有可能设置一些全局状态。 

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
        if (row < 0 || row >= digitSum.length || col < 0 || col >= digitSum[row].length) return;
        if (marked[row][col] || digitSum[row][col] > threshold) return;
        res++;
        marked[row][col] = true;
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

# 14. 剪绳子

[NowCoder]( https://www.nowcoder.com/practice/57d85990ba5b440ab888fc72b0751bf8?tpId=13&&tqId=33257&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking )

**题目描述**:

把一根绳子剪成多段，并且使得每段的长度乘积最大。

```java
n = 2
return 1 (2 = 1 + 1)

n = 10
return 36 (10 = 3 + 3 + 4)
```

**解题思路**: [leetcode](https://leetcode-cn.com/problems/integer-break/solution/bao-li-sou-suo-ji-yi-hua-sou-suo-dong-tai-gui-hua-/ )

> 首先，这题很容易想到递归：对于一个长度为 n 的绳子，设 f(n) 为长度为 n 的绳子间断后的最大乘积，那么有：
>
> $$
> f(n) = max\{i*f(n-i), i*(n-i)\},i=1,2,...,n-1
> $$
> i * (n - i) 表示剪成两段，i * f(n - i) 表示将 n- i 的这段继续剪。
>

1. **暴力解法**：

```java
public class Solution {
    public int cutRope(int target) {
        if (target < 2) return 0;
        if (target == 2) return 1;
        int res = -1;
        for (int i = 1; i < target; i++) {
            res = Math.max(res, Math.max(i*cutRope(target-i), i*(target-i)));
        }
        return res;
    }
}

public class Solution {
    public int cutRope(int target) {
        return cutRopeHelpr(target);
    }
    public int cutRopeHelpr(int n) {
        if (n < 2) return 0;
        if (target == 2) return 1;
        int res = -1;
        for (int i = 1; i < n; i++) {
            res = Math.max(res, Math.max(i*cutRopeHelpr(n-i), i*(n-i)));
        }
        return res;
    }
}
```

2. **备忘录方法**：

```java
public class Solution {
    private int[]memory;
    public int cutRope(int target) {
        memory = new int[target + 1];
        memory[2] = 1;
        return cutRopeHelpr(target);
    }
    public int cutRopeHelpr(int n) {
        if (n < 2) return 0;
        // memory的初始值为0，如果它不为0，说明已经计算过了，直接返回即可
        if (memory[n] != 0) return memory[n];
        int res = -1;
        for (int i = 1; i < n; i++) {
            res = Math.max(res, Math.max(i*cutRopeHelpr(n-i), i*(n-i)));
        }
        // 将每次计算的结果保存到备忘录数组中
        memory[n] = res;
        return res;
    }
}
```

3. **动态规划**：

   对于这样的一个**递归**代码，我们更习惯转化为**递推**，将**自顶向下**的思路转换为**自底向上**，这也是记忆化搜索和DP之间的区别所在。

```java
public class Solution {
    public int cutRope(int n) {
        int[] dp = new int[n + 1];
        dp[2] = 1;
        for (int i = 3; i <= n; i++)
            for (int j = 1; j < i; j++)
                // res = Math.max(res, Math.max(i*cutRopeHelpr(n-i), i*(n-i)));
                dp[i] = Math.max(dp[i], Math.max(j * (i - j), dp[j] * (i - j)));
        return dp[n];
    }
}

// f(n) = max{f(i) * f(n-i)}, i = 1,2,...,n-1
public class Solution {
    public int cutRope(int n) {
        if (n < 2) return 0;
        if (n == 2) return 1;
        if (n == 3) return 2;
        int[] dp = new int[n + 1];
        dp[0] = 0;
        dp[1] = 1;
        dp[2] = 2;
        dp[3] = 3;
        
        int max;
        for (int i = 4; i <= n; i++) {
            max = 0;
            for (int j = 1; j <= i/2; j++) {
                max = Math.max(max, dp[j] * dp[i - j]);
            }
            dp[i] = max;
        }
        return dp[n];
    }
}
```

# 15. 二进制中 1 的个数

[NowCoder](https://www.nowcoder.com/practice/8ee967e43c2c4ec193b040ea7fbb10b8?tpId=13&tqId=11164&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**：

> 输入一个整数，输出该数二进制表示中 1 的个数。

**方法一：逐位判断**

> 根据 与运算 定义，设二进制数字 n ，则有：
>
> > ​	若 n \& 1 = 0，则 n 二进制 最右一位 为 0 ；
> > ​	若 n \& 1 = 1，则 n 二进制 最右一位 为 1 。
>
> 根据以上特点，考虑以下 循环判断 ：
>
> > 判断 n 最右一位是否为 1 ，根据结果计数。
> > 将 n 右移一位（本题要求把数字 n 看作无符号数，因此使用 无符号右移 操作）。
>
> 算法流程：
>
> > 1. 初始化数量统计变量 res = 0。
> > 2. 循环逐位判断： 当 n = 0 时跳出。
> >    - res += n & 1 ： 若 n \& 1 = 1，则统计数 res 加一。
> >    - n >>= 1 ： 将二进制数字 n 无符号右移一位（ Java 中无符号右移为 ">>>" ） 。
> > 3. 返回统计数量 resres 。

```java
public class Solution {
    // you need to treat n as an unsigned value
    public int hammingWeight(int n) {
        int cnt = 0;
        while (n != 0) {
            if ((n & 1) == 1) cnt++;
            n >>>= 1;
        }
        return cnt;
    }
}
```

**方法二：巧用n&(n-1)**：该位运算去除 n 的位级表示中最低的那一位。

> (n−1) ： 二进制数字 n 最右边的 1 变成 0 ，此 1 右边的 0 都变成 1 。
> n \& (n - 1) ： 二进制数字 n 最右边的 1 变成 0 ，其余不变。

```java
n       : 10110100
n-1     : 10110011
n&(n-1) : 10110000
```

时间复杂度：O(M)，其中 M 表示 1 的个数。

```java
public int NumberOf1(int n) {
    int cnt = 0;
    while (n != 0) {
        cnt++;
        n &= (n - 1);
    }
    return cnt;
}
```

**Integer.bitCount()**

```java
public int NumberOf1(int n) {
    return Integer.bitCount(n);
}
```

# 16. 数值的整数次方

[NowCoder](https://www.nowcoder.com/practice/1a834e5e3e1a4b7ba251417554e07c00?tpId=13&tqId=11165&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**：

> 给定一个 double 类型的浮点数 base 和 int 类型的整数 exponent，求 base 的 exponent 次方。

**解题思路**：

**方法一：暴力方法**

> 很显然就是n个b相乘。循环n次。 
>
> **注意：**需考虑输入出错的情况：**底数为0，指数为负；或 底数为0，指数为0**

```java
public class Solution {
    boolean g_InvalidInput = false; // 全局变量，当为true时，标志着 输入出错
    public double Power(double base, int exponent) {
        g_InvalidInput = false;
        
        // 考虑底数为0且指数为负数的情况，此时0为倒数，不可以；或底数为0且指数为0，这种情况数学上没有意义；
        if (base == 0.0 && exponent <= 0) {
            g_InvalidInput = true; // 输入出错
            return 0.0;
        }
        
        double res = 1.0;
        for (int i = 1; i <= Math.abs(exponent); i++) { // for循环可以改成二分法
            res *= base;
        }
        
        // 考虑底数不为0时，指数为负数的情况
        if (exponent < 0) return 1/res;
        else return res;
  }
}
```

**方法二：二分法**

> 下面的讨论中 x 代表 base，n 代表 exponent。
>
> [![img](https://camo.githubusercontent.com/3f2ff3f276b598aef7ebd1b2d95b502520147dca/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f34386231643435392d383833322d346539322d393338612d3732386161653733303733392e6a7067)](https://camo.githubusercontent.com/3f2ff3f276b598aef7ebd1b2d95b502520147dca/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f34386231643435392d383833322d346539322d393338612d3732386161653733303733392e6a7067)
>
> 因为 (x*x)n/2 可以通过递归求解，并且每次递归 n 都减小一半，因此整个算法的时间复杂度为 O(logN)。

```java
public class Solution {
    boolean g_InvalidInput = false; // 全局变量，当为true时，标志着 输入出错
    public double Power(double base, int exponent) {
        g_InvalidInput = false;
        
        // 考虑底数为0且指数为负数的情况，此时0为倒数，不可以；或底数为0且指数为0，这种情况数学上没有意义；
        if (base == 0.0 && exponent <= 0) {
            g_InvalidInput = true; // 输入出错
            return 0.0;
        }
        
        double res = 1.0;
        for (int i = 1; i <= Math.abs(exponent) / 2; i++) {
            res *= (base * base);
        }
        if (Math.abs(exponent) % 2 != 0) res *= base;
        
        // 考虑底数不为0时，指数为负数的情况
        if (exponent < 0) return 1/res;
        else return res;
  }
}
//——————————————————————————————————————————————————————————————————————————————————————————————————
public class Solution {
    boolean g_InvalidInput = false; // 全局变量，当为true时，标志着 输入出错
    public double Power(double base, int exponent) {
        g_InvalidInput = false;
        
        // 考虑底数为0且指数为负数的情况，此时0为倒数，不可以；或底数为0且指数为0，这种情况数学上没有意义；
        if (base == 0.0 && exponent <= 0) {
            g_InvalidInput = true; // 输入出错
            return 0.0;
        }
        
        double res = PowerWithUnsignedExponent(base, Math.abs(exponent));
            
        // 考虑底数不为0时，指数为负数的情况
        if (exponent < 0) return 1/res;
        else return res;
  }
    
    public double PowerWithUnsignedExponent_1(double base, int absExponent) {
        double res = 1.0;
        for (int i = 1; i <= absExponent / 2; i++) {
            res *= (base * base);
        }
        if (Math.abs(absExponent) % 2 != 0) res *= base;
        return res;
    }
    
    public double PowerWithUnsignedExponent_2(double base, int absExponent) {
        if (absExponent == 0) return 1.0;
        if (absExponent == 1) return base;
        double res = PowerWithUnsignedExponent_2(base, absExponent>>1); // 用 右移运算符 代替 除2
        res *= res;
        if ((absExponent&1) == 1) res *= base; // 用 位与运算符 代替 求余运算符%
        return res;
    }
}
```

# 17. 打印从 1 到最大的 n 位数-------????

**题目描述**：

> 输入数字 n，按顺序打印出从 1 到最大的 n 位十进制数。比如输入 3，则打印出 1、2、3 一直到最大的 3 位数即 999。

**解题思路**：

> 由于 n 可能会非常大，因此不能直接用 int 表示数字，而是用 char 数组进行存储。
>
> 使用回溯法得到所有的数。

```java
class Solution {
    public void print1ToMaxOfNDigits(int n) {
        if (n <= 0) return;
        char[] number = new char[n];
        print1ToMaxOfNDigits(number, 0);
    }
    private void print1ToMaxOfNDigits(char[] number, int digit) {
    	if (digit == number.length) {
            printNumber(number);
            return;
        }
        for (int i = 0; i < 10; i++) {
            number[digit] = (char)(i + '0'); // 将'0'转为int类型为48，然后将(48+i)转换为char类型
            print1ToMaxOfNDigits(number, digit + 1);
        }
    }
    private printNumber(char[] number) {
        int index = 0;
        while(index < number.length && number[index] == '0') 
            index++;
        while(index < number.length) 
            System.out.print(number[index++]);
        System.out.println();
    }
}
```



# 18.1 删除链表节点

[删除链表的节点](https://leetcode-cn.com/problems/shan-chu-lian-biao-de-jie-dian-lcof/)

> 给定单向链表的头指针和一个要删除的节点的值，定义一个函数删除该节点。
>
> 返回删除后的链表的头节点。
>
> **说明：**题目保证链表中节点的值互不相同
>
> 删除节点需考虑：**是不是要删除的是头结点**。

**示例 1:**

```java
输入: head = [4,5,1,9], val = 5
输出: [4,1,9]
解释: 给定你链表中值为 5 的第二个节点，那么在调用了你的函数之后，该链表应变为 4 -> 1 -> 9.
```

**示例 2:**

```java
输入: head = [4,5,1,9], val = 1
输出: [4,5,9]
解释: 给定你链表中值为 1 的第三个节点，那么在调用了你的函数之后，该链表应变为 4 -> 5 -> 9.
```

```java
/**
 * Definition for singly-linked list.
 * public class ListNode {
 *     int val;
 *     ListNode next;
 *     ListNode(int x) { val = x; }
 * }
 */
class Solution {
    public ListNode deleteNode(ListNode head, int val) {
        if (head == null) return null;
        if (head.val == val) return head.next;
        ListNode pre = head;
        ListNode cur = head.next;
        while(cur != null) {
            if (cur.val == val) {
                pre.next = cur.next;
                break;
            }
            else {
                pre = cur;
                cur = cur.next;
            }
        }
        return head;
    }
}
// ---------------------------------------------------------------------
class Solution {
    public ListNode deleteNode(ListNode head, int val) {
        if (head == null) return null;
        if (head.val == val) return head.next;
        ListNode pre = head;
        ListNode cur = head.next;
        while(cur != null && cur.val != val) {
            pre = cur;
            cur = cur.next;
        }
        if (cur != null) pre.next = cur.next;
        return head;
    }
}
```

# 18.2在 O(1) 时间内删除链表节点

**解题思路**：

① 如果该节点不是尾节点，那么可以直接将下一个节点的值赋给该节点，然后令该节点指向下下个节点，再删除下一个节点，时间复杂度为 O(1)。

[![img](https://camo.githubusercontent.com/6be78c747b267d39ccfe3288ccc74c30c21fbd1c/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f31313736663965312d333434322d343830382d613437612d3736666261656131623830362e706e67)](https://camo.githubusercontent.com/6be78c747b267d39ccfe3288ccc74c30c21fbd1c/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f31313736663965312d333434322d343830382d613437612d3736666261656131623830362e706e67)

② 否则，就需要先遍历链表，找到节点的前一个节点，然后让前一个节点指向 null，时间复杂度为 O(N)。

[![img](https://camo.githubusercontent.com/b7e8f97594b4b6c781acb7c9f2f820ec76507139/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f34626638643062612d333666302d343539652d383361302d6631353237386135613135372e706e67)](https://camo.githubusercontent.com/b7e8f97594b4b6c781acb7c9f2f820ec76507139/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f34626638643062612d333666302d343539652d383361302d6631353237386135613135372e706e67)

综上，如果进行 N 次操作，那么大约需要操作节点的次数为 N-1+N=2N-1，其中 N-1 表示 N-1 个不是尾节点的每个节点以 O(1) 的时间复杂度操作节点的总次数，N 表示 1 个尾节点以 O(N) 的时间复杂度操作节点的总次数。(2N-1)/N ~ 2，因此该算法的平均时间复杂度为 O(1)。

```java
class Solution {
    public ListNode deleteNode(ListNode head, ListNode tobeDelete) {
        if (head == null || tobeDelete == null || head == tobeDelete) return null;
        if (tobeDelete.next != null) { // 要删除节点不是尾节点
            tobeDelete.val = tobeDelete.next.val;
            tobeDelete.next = tobeDelete.next.next;
        } else { // 要删除节点是尾节点，且链表不只一个节点
            ListNode cur = head;
            while(cur.next != tobeDelete) cur = cur.next;
            cur.next = null;
        }
        return head;
    }
}
```

# 18.3 删除链表中重复的结点

[NowCoder](https://www.nowcoder.com/practice/fc533c45b73a41b0b44ccba763f866ef?tpId=13&tqId=11209&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**:

> 在一个排序的链表中，存在重复的结点，请删除该链表中重复的结点，重复的结点不保留，返回链表头指针。 例如，链表1->2->3->3->4->4->5 处理后为 1->2->5 
>
> [![img](https://camo.githubusercontent.com/dfb9e66fc5f91b2b1c94744a3957c48a67a95ea8/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f31376533303164662d353265382d343838362d623539332d3834316131366431336534342e706e67)](https://camo.githubusercontent.com/dfb9e66fc5f91b2b1c94744a3957c48a67a95ea8/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f31376533303164662d353265382d343838362d623539332d3834316131366431336534342e706e67)

**解题描述**:

```java
public class Solution {
    public ListNode deleteDuplication(ListNode pHead) {
        if (pHead == null || pHead.next == null) return pHead;
        ListNode dummy = new ListNode(-1);
        dummy.next = pHead;
        ListNode pre = dummy;
        ListNode cur = pHead.next;
        while(cur != null) {
            if (pre.next.val == cur.val) {
                while(cur != null && pre.next.val == cur.val) cur = cur.next;
                if (cur == null) pre.next = null;
                else {
                    pre.next = cur;
                    cur = cur.next;
                }
            } else {
                pre = pre.next;
                cur = cur.next;
            }
        }
        return dummy.next;
    }
}
// -------------------------------------------------------------------
class Solution {
    public ListNode deleteDuplicates(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        ListNode pre = dummy;
        ListNode cur = head;
        while(cur.next != null) {
            if (cur.val == cur.next.val) {
                while (cur.next != null && cur.val == cur.next.val) cur = cur.next;
                if (cur.next == null) pre.next = null;
                else {
                    cur = cur.next; 
                    pre.next = cur;
                }
            } else {
                pre = cur;
                cur = cur.next;
            }
             
        }
        return dummy.next;
    }
}
```



# 21.1 调整数组顺序使奇数位于偶数前面

[LeetCode](https://leetcode-cn.com/problems/diao-zheng-shu-zu-shun-xu-shi-qi-shu-wei-yu-ou-shu-qian-mian-lcof/)

**题目描述**：

> 输入一个整数数组，实现一个函数来调整该数组中数字的顺序，使得所有奇数位于数组的前半部分，所有偶数位于数组的后半部分。 
>
> [![img](https://camo.githubusercontent.com/d503b95e3bb580c9fcee2808fb975d2a08a0f6a3/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f64303361326566612d656631392d346339362d393765382d6666363164663830363164332e706e67)](https://camo.githubusercontent.com/d503b95e3bb580c9fcee2808fb975d2a08a0f6a3/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f64303361326566612d656631392d346339362d393765382d6666363164663830363164332e706e67)

**解题思路**：

```java
// 简单粗暴，申请一个临时数组
class Solution {
    public int[] exchange(int[] nums) {
        if (nums == null || nums.length == 0) return nums;
        int[] res = new int[nums.length];
        int left = 0, right = nums.length - 1;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] % 2 == 1) res[left++] = nums[i];
            else res[right--] = nums[i];
        }
        return res;
    }
}
// --------------------------
class Solution {
    public int[] exchange(int[] nums) {
        if (nums == null || nums.length == 0) return nums;
        int n = 0;
        for (int i = 0; i < nums.length; i++) {
            if (nums[i] % 2 == 1) n++;
        }
        int k = n;
        for (int i = 0; i < n; i++) {
            if (nums[i] % 2 == 0) {
                int temp = nums[k];
                nums[k] = nums[i];
                nums[i] = temp;
                i--;
                k++;
            }
        }
        return nums;
    }
}
// ----------------------快速排序思想-------------------------------
class Solution {
    public int[] exchange(int[] nums) {
        int i = 0, j = nums.length - 1, tmp;
        while(i < j) {
            while(i < j && (nums[i] & 1) == 1) i++;
            while(i < j && (nums[j] & 1) == 0) j--;
            tmp = nums[i];
            nums[i] = nums[j];
            nums[j] = tmp;
        }
        return nums;
    }
}
```

# 19. 正则表达式匹配

[NowCoder](https://www.nowcoder.com/practice/45327ae22b7b413ea21df13ee7d6429c?tpId=13&tqId=11205&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

请实现一个函数用来匹配包括 '.' 和 '*' 的正则表达式。模式中的字符 '.' 表示任意一个字符，而 '*' 表示它前面的字符可以出现任意次（包含 0 次）。

在本题中，匹配是指字符串的所有字符匹配整个模式。例如，字符串 "aaa" 与模式 "a.a" 和 "ab*ac*a" 匹配，但是与 "aa.a" 和 "ab*a" 均不匹配。

**解题思路**

应该注意到，'.' 是用来当做一个任意字符，而 '*' 是用来重复前面的字符。这两个的作用不同，不能把 '.' 的作用和 '*' 进行类比，从而把它当成重复前面字符一次。

```java
bool isMatch(string text, string pattern) {
    if (text.size() != pattern.size()) 
        return false;
    for (int j = 0; j < pattern.size(); j++) {
        if (pattern[j] != text[j])
            return false;
    }
    return true;
}
```



# 20. 表示数值的字符串

[NowCoder](https://www.nowcoder.com/practice/6f8c901d091949a5837e24bb82a731f2?tpId=13&tqId=11206&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

```
true

"+100"
"5e2"
"-123"
"3.1416"
"-1E-16"
false

"12e"
"1a3.14"
"1.2.3"
"+-5"
"12e+4.3"
```

**解题思路**



# 21.2 调整数组顺序使奇数位于偶数前面

[NowCoder](https://www.nowcoder.com/practice/beb5aa231adc45b2a5dcc5b62c93f593?tpId=13&tqId=11166&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**：

> 需要保证奇数和奇数，偶数和偶数之间的相对位置不变，这和书本不太一样。
>
> [![img](https://camo.githubusercontent.com/d503b95e3bb580c9fcee2808fb975d2a08a0f6a3/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f64303361326566612d656631392d346339362d393765382d6666363164663830363164332e706e67)](https://camo.githubusercontent.com/d503b95e3bb580c9fcee2808fb975d2a08a0f6a3/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f64303361326566612d656631392d346339362d393765382d6666363164663830363164332e706e67)

**解题思路**：

```java
// 冒泡排序思想
public class Solution {
    public void reOrderArray(int [] array) {
        if (array == null || array.length == 0) return;
        int N = array.length;
        for (int i = 0; i < N; i++) {
            for (int j = 0; j < N-1-i; j++) {
                if ((array[j] % 2 == 0) && (array[j+1] % 2 == 1)){
                    int temp = array[j];
                    array[j] = array[j+1];
                    array[j+1] = temp;
                }
            }
        }
    }
}
// 插入排序思想
public class Solution {
    public void reOrderArray(int [] array) {
        // 相对位置不变，稳定性
        // 插入排序的思想
        int N = array.length;
        int k = 0; // 记录已经摆好位置的奇数的个数
        for (int i = 0; i < N; i++) {
            if (array[i] % 2 == 1) {
                int j = i;
                int temp = array[j];
                while (j > k) { // j >= k+1
                    array[j] = array[j-1];
                    j--;
                }
                array[j] = temp;
                k++;
            }
        }
    }
}
```

# 22. 链表中倒数第 K 个结点

[NowCoder](https://www.nowcoder.com/practice/529d3ae5a407492994ad2a246518148a?tpId=13&tqId=11167&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**解题思路**:

> 设链表的长度为 N。设置两个指针 P1 和 P2，先让 P1 移动 K 个节点，则还有 N - K 个节点可以移动。此时让 P1 和 P2 同时移动，可以知道当 P1 移动到链表结尾时，P2 移动到第 N - K 个节点处，该位置就是倒数第 K 个节点。
>
> [![img](https://camo.githubusercontent.com/49af285aed35e105765bf383b672a2263131fe07/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f36623530346631662d626637362d346161622d613134362d6139633761353863323032392e706e67)](https://camo.githubusercontent.com/49af285aed35e105765bf383b672a2263131fe07/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f36623530346631662d626637362d346161622d613134362d6139633761353863323032392e706e67)

```java
public class Solution {
    public ListNode FindKthToTail(ListNode head,int k) {
        if (head == null || k <= 0) return null;
        ListNode cur = head;
        int count = 0;
        while(cur != null && count < k) {
            count++;
            cur = cur.next;
        }
        // 如果循环是由于 cur==null 退出的，则此时count表示链表长度
        if (count != k) return null;
        // 如果循环是由于 count==k 退出的，则此时cur指向从头结点开始第 k+1 个节点
        ListNode p = head; // cur与p之间间隔 k+1
        while(cur != null) { // 退出循环，cur==null，p指向倒数第 k 个节点
            p = p.next;
            cur = cur.next;
        }
        return p;
    }
}

public ListNode FindKthToTail(ListNode head, int k) {
    if (head == null)
        return null;
    ListNode P1 = head;
    while (P1 != null && k-- > 0)
        P1 = P1.next;
    if (k > 0)
        return null;
    ListNode P2 = head;
    while (P1 != null) {
        P1 = P1.next;
        P2 = P2.next;
    }
    return P2;
}
```

# 22.1 链表的中间结点

[LeetCode](https://leetcode-cn.com/problems/middle-of-the-linked-list/)

> 给定一个带有头结点 `head` 的非空单链表，返回链表的中间结点。
> 如果有两个中间结点，则返回第二个中间结点

```java
class Solution {
    public ListNode middleNode(ListNode head) {
        ListNode slow = head, fast = head;
        while (fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
        }
        return slow;
    }
}

class Solution {
    public ListNode middleNode(ListNode head) {
        if (head == null || head.next == null) return head;
        if (head.next.next == null) return head.next;
        ListNode p = head;
        ListNode q = head;
        while(q.next != null && q.next.next != null) {
            p = p.next;
            q = q.next.next;
        } // 如果链表个数为奇数，p指向中点；如果为偶数，p指向中间两个节点中的左边节点
        if (q.next != null) p = p.next;
        return p;
    }
}
```

# 23. 链表中环的入口结点

[NowCoder](https://www.nowcoder.com/practice/253d2c59ec3e4bc68da16833f79a38e4?tpId=13&tqId=11208&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 一个链表中包含环，请找出该链表的环的入口结点。要求不能使用额外的空间。

**解题思路**

> 使用双指针，一个指针 fast 每次移动两个节点，一个指针 slow 每次移动一个节点。因为存在环，所以两个指针必定相遇在环中的某个节点上。假设相遇点在下图的 z1 位置，此时 fast 移动的节点数为 x+2y+z，slow 为 x+y，由于 fast 速度比 slow 快一倍，因此 x+2y+z=2(x+y)，得到 x=z。
>
> 在相遇点，slow 要到环的入口点还需要移动 z 个节点，如果让 fast 重新从头开始移动，并且速度变为每次移动一个节点，那么它到环入口点还需要移动 x 个节点。在上面已经推导出 x=z，因此 fast 和 slow 将在环入口点相遇。
>
> [![img](https://camo.githubusercontent.com/7dc81248ade9b8fe68010abd9b04ffe8290d9e5c/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f62623766633138322d393863322d343836302d386561332d3633306532376135663239662e706e67)](https://camo.githubusercontent.com/7dc81248ade9b8fe68010abd9b04ffe8290d9e5c/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f62623766633138322d393863322d343836302d386561332d3633306532376135663239662e706e67)

```java
public class Solution {
    public ListNode detectCycle(ListNode head) {
        if (head == null) return null;
        ListNode fast = head;
        ListNode slow = head;
        while(fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            if (slow == fast) {
                break;
            }
        }
        // 通过这种方式判断是否有环不行：可能根本没有进入循环，此时slow == fast == head；
        // if (slow == fast) return null; 
        if (fast == null || fast.next == null) return null;
        // slow从head开始，fast从相遇点开始，一步一步走再次相遇即为环入口
        slow = head;
        while (slow != fast) {
            slow = slow.next;
            fast = fast.next;
        }
        return slow;
    }
}


public class Solution {
    public ListNode detectCycle(ListNode head) {
        if (head == null) return null;
        ListNode fast = head, slow = head;
        while(fast != null && fast.next != null) {
            slow = slow.next;
            fast = fast.next.next;
            if (slow == fast) {
                break;
            }
        }
        if (fast == null || fast.next == null) return null;
        
        // 计算环中节点的个数
        int count = 1; // 注意：这里初始化为 1
        while(slow.next != fast) {
            count++;
            slow = slow.next;
        }
        
        ListNode cur = head, pre = head;
        while(cur != null && count-- > 0) cur = cur.next;
        while(cur != pre) {
            pre = pre.next;
            cur = cur.next;
        }
        return pre;
    }
}
```

# 24. 反转链表

[NowCoder](https://www.nowcoder.com/practice/75e878df47f24fdc9dc3e400ec6058ca?tpId=13&tqId=11168&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**解题思路**：

1. 递归

```java
public class Solution {
    public ListNode ReverseList(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode newNode = ReverseList(head.next);
        head.next.next = head;
        head.next = null;
        return newNode;
    }
}
```

2. 头插法

```java
public class Solution {
    public ListNode ReverseList(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode dummy = new ListNode(-1);
        ListNode cur = head;
        while (cur != null) {
            ListNode temp = cur.next;
            cur.next = dummy.next;
            dummy.next = cur;
            cur = temp;
        }
        return dummy.next;
    }
}
```

3. 迭代：用一个 pre 节点保存向前指针，temp 保存向后的临时指针

# 25. 合并两个排序的链表

[NowCoder](https://www.nowcoder.com/practice/d8b6b4358f774294a89de2a6ac4d9337?tpId=13&tqId=11169&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

[![img](https://camo.githubusercontent.com/ba39d4dcf184c4ffb67aacda5bdf27df3f932439/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f63303934643262632d656337352d343434622d616637372d6433363964666236623362342e706e67)](https://camo.githubusercontent.com/ba39d4dcf184c4ffb67aacda5bdf27df3f932439/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f63303934643262632d656337352d343434622d616637372d6433363964666236623362342e706e67)

**解题思路**:

1. 迭代：

```java
class Solution {
    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null && l2 == null) return null;
        if (l1 == null) return l2;
        if (l2 == null) return l1;
        ListNode dumpy = new ListNode(-1);
        ListNode cur = dumpy;
        while (l1 != null && l2 != null) {
            if (l1.val <= l2.val) {
                cur.next = l1;
                l1 = l1.next;
            } else {
                cur.next = l2;
                l2 = l2.next;
            }
            cur = cur.next;
        }
        if (l1 == null) cur.next = l2;
        if (l2 == null) cur.next = l1;
        return dumpy.next;
    }
}
```

2. 递归：

```java
public class Solution {
    public ListNode Merge(ListNode list1,ListNode list2) {
        if (list1 == null && list2 == null) return null; // 该语句 可要可不要
        if (list1 == null) return list2;
        if (list2 == null) return list1;
        if (list1.val <= list2.val) {
            list1.next = Merge(list1.next, list2);
            return list1;
        } else {
            list2.next = Merge(list1, list2.next);
            return list2;
        }
    }
}
```

# 26. 树的子结构

[NowCoder](https://www.nowcoder.com/practice/6e196c44c7004d15b1610b9afca8bd88?tpId=13&tqId=11170&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**：

>  输入两棵二叉树A，B，判断B是不是A的子结构。（ps：我们约定空树不是任意一个树的子结构） 
>
> [![img](https://camo.githubusercontent.com/39fb0af445ad5ebc98d64ae464d8271cbf3926b6/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f38346135623135612d383663352d346438652d393433392d6439666435613436393961312e6a7067)](https://camo.githubusercontent.com/39fb0af445ad5ebc98d64ae464d8271cbf3926b6/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f38346135623135612d383663352d346438652d393433392d6439666435613436393961312e6a7067)

**解题思路**：

> 分两种情况：
>
> 1. 在 树A 中查找与 树B 根节点值相等的节点 R（树的遍历）：HasSubtree
> 2. 判断 树A 中以 R 为根节点的子树是不是和 树B 具有相同的结构：isSubtreeWithRoot

```java
public class Solution {
    public boolean HasSubtree(TreeNode root1,TreeNode root2) {
        if (root1 == null || root2 == null) return false;
        boolean res = false;
        if (root1.val == root2.val) res = isSubtreeWithRoot(root1, root2);
        if (!res) res = HasSubtree(root1.left, root2);
        if (!res) res = HasSubtree(root1.right, root2);
        return res;
    }
    
    public boolean isSubtreeWithRoot(TreeNode root1, TreeNode root2) {
        if (root2 == null) return true;
        if (root1 == null) return false;
        if (root1.val == root2.val) 
            return isSubtreeWithRoot(root1.left, root2.left) 
            	&& isSubtreeWithRoot(root1.right, root2.right);
        else 
            return false;
    }
}

// --------------------------------------------------------------------
public class Solution {
    public boolean HasSubtree(TreeNode root1,TreeNode root2) {
        if (root1 == null || root2 == null) return false;
        return isSubtreeWithRoot(root1, root2) 
            || HasSubtree(root1.left, root2) 
            || HasSubtree(root1.right, root2);
    }
    
    public boolean isSubtreeWithRoot(TreeNode root1, TreeNode root2) {
        if (root2 == null) return true;
        if (root1 == null) return false;
        if (root1.val != root2.val) return false;
        return isSubtreeWithRoot(root1.left, root2.left) 
            && isSubtreeWithRoot(root1.right, root2.right);
    }
}
```

# 27. 二叉树的镜像

[NowCoder](https://www.nowcoder.com/practice/564f4c26aa584921bc75623e48ca3011?tpId=13&tqId=11171&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

>  操作给定的二叉树，将其变换为源二叉树的镜像。 
>
> ```
> 二叉树的镜像定义：源二叉树 
>     	    8
>     	   /  \
>     	  6   10
>     	 / \  / \
>     	5  7 9 11
>     	镜像二叉树
>     	    8
>     	   /  \
>     	  10   6
>     	 / \  / \
>     	11 9 7  5
> ```

**解题思路**

> 遍历树的同时交换**非叶节点**的左、右子节点:
>
> - **先序遍历**这棵树的每个节点，如果遍历到的节点有子节点，就交换它的两个子节点。
> - 当交换完所有**非叶节点**的左右子节点之后，就得到了树的镜像

```java
public class Solution {
    public void Mirror(TreeNode root) {
        if (root == null) return;
        if (root.left == null && root.right == null) return;
        TreeNode temp = root.left;
        root.left = root.right;
        root.right = temp;
        Mirror(root.right);
        Mirror(root.left);
    }
}
// ------------------------------------------------------------
public class Solution {
    public void Mirror(TreeNode root) {
        if (root == null) return;
        // if (root.left == null && root.right == null) return; // 可有可无
        swap(root);
        Mirror(root.right);
        Mirror(root.left);
    }
    
    public void swap(TreeNode root) {
        TreeNode temp = root.left;
        root.left = root.right;
        root.right = temp;
    }
}


// 迭代法
public TreeNode invertTree(TreeNode root) {
    if (root == null) return null;
    Queue<TreeNode> queue = new LinkedList<TreeNode>();
    queue.add(root);
    while (!queue.isEmpty()) {
        TreeNode current = queue.poll();
        TreeNode temp = current.left;
        current.left = current.right;
        current.right = temp;
        if (current.left != null) queue.add(current.left);
        if (current.right != null) queue.add(current.right);
    }
    return root;
}
```

# 28 对称的二叉树

[NowCoder](https://www.nowcoder.com/practice/ff05d44dfdb04e1d83bdbdab320efbcb?tpId=13&tqId=11211&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**：

> 请实现一个函数，用来判断一棵二叉树是不是对称的。注意，如果一个二叉树同此二叉树的镜像是同样的，定义其为对称的。 

**解题思路**：

> - 首先判断根节点是否存在，如果不存在，则为false；存在就继续下一步
> - 接下来可以看成判断两颗树A、B（根节点的左右子树）是否是镜像的：
>   - A树与B树的根节点都不存在，true
>   - A树与B树的根节点只存在一个，false
>   - A树与B树的根节点值不相等，false
>   - 判断A树的左子节点与B树的右子节点是否相等、A树的右子节点与B树的左子节点是否相等。

```java
public class Solution {
    boolean isSymmetrical(TreeNode pRoot) {
        if (pRoot == null) return true;
        // if (pRoot.left == null && pRoot.right == null) return true;
        // if (pRoot.left == null || pRoot.right == null) return false;
        return isSymmetrical(pRoot, pRoot);
    }
    
    boolean isSymmetrical(TreeNode root1, TreeNode root2) {
        if (root1 == null && root2 == null) return true;
        if (root1 == null || root2 == null) return false;
        if (root1.val != root2.val) return false;
        else 
            return isSymmetrical(root1.left, root2.right) && isSymmetrical(root1.right, root2.left);
    }
}
// ----------------------------------------
public class Solution {
    boolean isSymmetrical(TreeNode pRoot) {
        if (pRoot == null) return true;
        // if (pRoot.left == null && pRoot.right == null) return true;
        // if (pRoot.left == null || pRoot.right == null) return false;
        return isSymmetrical(pRoot.left, pRoot.right);
    }
    
    boolean isSymmetrical(TreeNode root1, TreeNode root2) {
        if (root1 == null && root2 == null) return true;
        if (root1 == null || root2 == null) return false;
        if (root1.val != root2.val) return false;
        else 
            return isSymmetrical(root1.left, root2.right) && isSymmetrical(root1.right, root2.left);
    }
}

// 迭代法
class Solution {
    public boolean isSymmetric(TreeNode root) {
        if (root == null) return true;
        return isSymmetric(root.left, root.right);
    }

    public boolean isSymmetric(TreeNode root1, TreeNode root2) {
        if (root1 == null && root2 == null) return true;
        if (root1 == null || root2 == null) return false;
        Queue<TreeNode> p = new LinkedList<>();
        Queue<TreeNode> q = new LinkedList<>();
        p.offer(root1);
        q.offer(root2);
        while(!p.isEmpty()) {
            TreeNode u = p.poll();
            TreeNode v = q.poll();
            if (u == null && v == null) continue;
            if (u == null || v == null) return false;
            if (u.val != v.val) return false;
            p.offer(u.left);
            p.offer(u.right);
            q.offer(v.right);
            q.offer(v.left);
        }
        return true;
    }
}
```

# 29. 顺时针打印矩阵

[NowCoder](https://www.nowcoder.com/practice/9b4c81a02cd34f76be2659fa0d54342a?tpId=13&tqId=11172&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 下图的矩阵顺时针打印结果为：1, 2, 3, 4, 8, 12, 16, 15, 14, 13, 9, 5, 6, 7, 11, 10
>
> [![img](https://camo.githubusercontent.com/b9c5aa06c3174feef17b576968631c5ddd7feae0/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f34383531373232372d333234632d343636342d626432362d6132643263666665326266652e706e67)](https://camo.githubusercontent.com/b9c5aa06c3174feef17b576968631c5ddd7feae0/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f34383531373232372d333234632d343636342d626432362d6132643263666665326266652e706e67)

**解题思路**

> 简单来说，就是不断地收缩矩阵的边界。定义四个变量代表范围，up、down、left、right ：
>
> 1. 向右走存入整行的值，当存入后，该行再也不会被遍历，代表上边界的 up 加一，同时判断是否和代表下边界的 down 交错 
> 2. 向下走存入整列的值，当存入后，该列再也不会被遍历，代表右边界的 right 减一，同时判断是否和代表左边界的 left 交错 
> 3. 向左走存入整行的值，当存入后，该行再也不会被遍历，代表下边界的 down 减一，同时判断是否和代表上边界的 up 交错 
> 4. 向上走存入整列的值，当存入后，该列再也不会被遍历，代表左边界的 left 加一，同时判断是否和代表右边界的 right 交错

```java
import java.util.ArrayList;
public class Solution {
    public ArrayList<Integer> printMatrix(int [][] matrix) {
        ArrayList<Integer> res = new ArrayList<>();
        if(matrix == null || matrix.length == 0 || matrix[0].length == 0) return res;
        int r1 = 0, r2 = matrix.length-1, c1 = 0, c2 = matrix[0].length-1;
        while(true){
            for(int c = c1; c <= c2; c++){ // 最上面一行
                res.add(matrix[r1][c]);
            }
            r1++; // 向下逼近
            if(r1 > r2) break; // 判断是否越界
            for(int r = r1; r <= r2; r++){ // 最右边一行
                res.add(matrix[r][c2]);
            }
            c2--; // 向左逼近
            if(c1 > c2) break; // 判断是否越界
            for(int c = c2; c >= c1; c--){ // 最下面一行
                res.add(matrix[r2][c]);
            }
            r2--; // 向上逼近
            if(r1 > r2) break; // 判断是否越界
            // 最左边一行
            for(int r = r2; r >= r1; r--){
                res.add(matrix[r][c1]);
            }
            c1++; // 向右逼近
            if(c1 > c2) break; // 判断是否越界
        }
        return res;
    }
}
// ————————————————————————————————————————————————————————————————————————————————————
import java.util.ArrayList;
public class Solution {
    public ArrayList<Integer> printMatrix(int [][] matrix) {
        ArrayList<Integer> res = new ArrayList<>();
        if (matrix == null || matrix.length == 0 || matrix[0].length == 0) 
            return res;
        int r1 = 0, r2 = matrix.length - 1, c1 = 0, c2 = matrix[0].length - 1;
        while(r1 <= r2 && c1 <= c2){
            for (int c = c1; c <= c2; c++) {
                res.add(matrix[r1][c]);
            }
            for (int r = r1+1; r <= r2; r++) {
                res.add(matrix[r][c2]);
            }
            if (r1 != r2) { // 这里需要注意：如果没有该语句判断，则r1 == r2时，会重复打印
                for (int c = c2-1; c >= c1; c--) {
                    res.add(matrix[r2][c]);
                }
            }
            if (c1 != c2) { // 这里需要注意：如果没有该语句判断，则c1 == c2时，会重复打印
                for (int r = r2-1; r > r1; r--) {
                    res.add(matrix[r][c1]);
                }
            }
            r1++;
            r2--;
            c1++;
            c2--;
        }
        return res;
    }
}
```

# 30. 包含 min 函数的栈

[NowCoder](https://www.nowcoder.com/practice/4c776177d2c04c2494f2555c9fcc1e49?tpId=13&tqId=11173&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**：

> 定义栈的数据结构，请在该类型中实现一个能够得到栈最小元素的 min 函数。

**解题思路**：

```java
class MinStack {
    Stack<Integer> stackData;
    Stack<Integer> stackMin;
    /** initialize your data structure here. */
    public MinStack() {
        stackData = new Stack<>();
        stackMin = new Stack<>();
    }
    public void push(int x) {
        if (stackData.isEmpty()) {
            stackData.push(x);
            stackMin.push(x);
        } else {
            int top = stackMin.peek();
            if (top >= x) stackMin.push(x);
            else stackMin.push(top);
            stackData.push(x);
        }
    }
    public void pop() {
        if (stackData.isEmpty()) throw new RuntimeException("Stack is empty!!!");
        stackData.pop();
        stackMin.pop();
    }
    public int top() {
        if (stackData.isEmpty()) throw new RuntimeException("Stack is empty!!!");
        return stackData.peek();
    }
    public int min() {
        if (stackMin.isEmpty()) throw new RuntimeException("Stack is empty!!!");
        return stackMin.peek();
    }
}
```

# 31. 栈的压入、弹出序列

[NowCoder](https://www.nowcoder.com/practice/d77d11405cc7470d82554cb392585106?tpId=13&tqId=11174&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 输入两个整数序列，第一个序列表示栈的压入顺序，请判断第二个序列是否为该栈的弹出顺序。假设压入栈的所有数字均不相等。
>
> 例如序列 1,2,3,4,5 是某栈的压入顺序，序列 4,5,3,2,1 是该压栈序列对应的一个弹出序列，但 4,3,5,1,2 就不可能是该压栈序列的弹出序列。

**解题思路**

> 使用一个栈来模拟压入弹出操作。

```java
import java.util.ArrayList;
import java.util.Stack;
public class Solution {
    public boolean IsPopOrder(int [] pushA,int [] popA) {
        // if (pushA == null || popA == null || pushA.length == 0 || popA.length == 0) return false;
        Stack<Integer> stack = new Stack<>();
        int pushIndex = 0;
        int popIndex = 0;
        while (pushIndex < pushA.length) {
            stack.push(pushA[pushIndex++]);
            while (!stack.isEmpty() && stack.peek() == popA[popIndex]) {
                popIndex++;
                stack.pop();
            }
        }
        return stack.isEmpty();
    }
}
```



# 32.1 从上往下打印二叉树

[NowCoder](https://www.nowcoder.com/practice/7fe2212963db4790b57431d9ed259701?tpId=13&tqId=11175&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 从上往下打印出二叉树的每个节点，同层节点从左至右打印。
>
> 例如，以下二叉树层次遍历的结果为：1,2,3,4,5,6,7

[![img](https://camo.githubusercontent.com/a8c297632c6ff6d95f160affb9890e8433e68851/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f64356538333863662d643861322d343961662d393064662d3162326137313465653637362e6a7067)](https://camo.githubusercontent.com/a8c297632c6ff6d95f160affb9890e8433e68851/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f64356538333863662d643861322d343961662d393064662d3162326137313465653637362e6a7067)

**解题思路**

> 使用队列来进行层次遍历。
>
> 不需要使用两个队列分别存储当前层的节点和下一层的节点，因为在开始遍历一层的节点时，当前队列中的节点数就是当前层的节点数，只要控制遍历这么多节点数，就能保证这次遍历的都是当前层的节点。

```java
import java.util.LinkedList;
public class Solution {
    public ArrayList<Integer> PrintFromTopToBottom(TreeNode root) {
        ArrayList<Integer> res = new ArrayList<>();
        if (root == null) return res;
        LinkedList<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        while(!queue.isEmpty()) {
            TreeNode node = queue.poll();
            res.add(node.val);
            if (node.left != null) queue.offer(node.left);
            if (node.right != null) queue.offer(node.right);
        }
        return res;
    }
}
```

# 32.2 把二叉树打印成多行

[NowCoder](https://www.nowcoder.com/practice/445c44d982d04483b04a54f298796288?tpId=13&tqId=11213&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 和上题几乎一样。

**解题思路**

```java
import java.util.LinkedList;
import java.util.Queue;
public class Solution {
    ArrayList<ArrayList<Integer> > Print(TreeNode pRoot) {
        ArrayList<ArrayList<Integer>> res = new ArrayList<>();
        if (pRoot == null) return res;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(pRoot);
        while(!queue.isEmpty()) {
            ArrayList<Integer> list = new ArrayList<>();
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode node = queue.poll();
                list.add(node.val);
                if (node.left != null) queue.offer(node.left);
                if (node.right != null) queue.offer(node.right);
            }
            res.add(list);
        }
        return res;
    }
}
```

# 32.3 按之字形顺序打印二叉树

[NowCoder](https://www.nowcoder.com/practice/91b69814117f4e8097390d107d2efbe0?tpId=13&tqId=11212&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 请实现一个函数按照之字形打印二叉树，即第一行按照从左到右的顺序打印，第二层按照从右至左的顺序打印，第三行按照从左到右的顺序打印，其他行以此类推。

**解题思路**

```java
import java.util.LinkedList;
import java.util.Queue;
import java.util.Collections;
public class Solution {
    ArrayList<ArrayList<Integer> > Print(TreeNode pRoot) {
        ArrayList<ArrayList<Integer>> res = new ArrayList<>();
        if (pRoot == null) return res;
        Queue<TreeNode> queue = new LinkedList<>();
        queue.offer(pRoot);
        int level = 0;
        while(!queue.isEmpty()) {
            ArrayList<Integer> list = new ArrayList<>();
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode node = queue.poll();
                list.add(node.val);
                if (node.left != null) queue.offer(node.left);
                if (node.right != null) queue.offer(node.right);
            }
            if (level++ % 2 == 1) Collections.reverse(list);
            res.add(list);
        }
        return res;
    }
}
```

```java
public ArrayList<Integer> PrintFromTopToBottom(TreeNode root) {
    Queue<TreeNode> queue = new LinkedList<>();
    ArrayList<Integer> ret = new ArrayList<>();
    queue.add(root);
    while (!queue.isEmpty()) {
        int cnt = queue.size();
        while (cnt-- > 0) {
            TreeNode t = queue.poll();
            if (t == null) continue;
            ret.add(t.val);
            queue.add(t.left);
            queue.add(t.right);
        }
    }
    return ret;
}

public ArrayList<ArrayList<Integer>> Print(TreeNode pRoot) {
    ArrayList<ArrayList<Integer>> ret = new ArrayList<>();
    Queue<TreeNode> queue = new LinkedList<>();
    queue.add(pRoot);
    while (!queue.isEmpty()) {
        ArrayList<Integer> list = new ArrayList<>();
        int cnt = queue.size();
        while (cnt-- > 0) {
            TreeNode node = queue.poll();
            if (node == null) continue;
            list.add(node.val);
            queue.add(node.left);
            queue.add(node.right);
        }
        if (list.size() != 0) ret.add(list);
    }
    return ret;
}

public ArrayList<ArrayList<Integer>> Print(TreeNode pRoot) {
    ArrayList<ArrayList<Integer>> ret = new ArrayList<>();
    Queue<TreeNode> queue = new LinkedList<>();
    queue.add(pRoot);
    boolean reverse = false;
    while (!queue.isEmpty()) {
        ArrayList<Integer> list = new ArrayList<>();
        int cnt = queue.size();
        while (cnt-- > 0) {
            TreeNode node = queue.poll();
            if (node == null) continue;
            list.add(node.val);
            queue.add(node.left);
            queue.add(node.right);
        }
        if (reverse) Collections.reverse(list);
        reverse = !reverse;
        if (list.size() != 0) ret.add(list);
    }
    return ret;
}
```

# 33. 二叉搜索树的后序遍历序列

[NowCoder](https://www.nowcoder.com/practice/a861533d45854474ac791d90e447bafd?tpId=13&tqId=11176&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 输入一个整数数组，判断该数组是不是某二叉搜索树的后序遍历的结果。假设输入的数组的任意两个数字都互不相同。
>
> 例如，下图是后序遍历序列 1,3,2 所对应的二叉搜索树。
>
> [![img](https://camo.githubusercontent.com/5a814ee1e9fc706600dcd24e47e42f2546f37db3/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f31333435346661312d323361382d343537382d393636332d3262313361366166353634612e6a7067)](https://camo.githubusercontent.com/5a814ee1e9fc706600dcd24e47e42f2546f37db3/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f31333435346661312d323361382d343537382d393636332d3262313361366166353634612e6a7067)

**解题思路**

```java
import java.util.Arrays;
public class Solution {
    public boolean VerifySquenceOfBST(int [] sequence) {
        if (sequence == null || sequence.length == 0) return false;
        return verify(sequence, 0, sequence.length-1);
    }
    
    private boolean verify(int[] sequence, int first, int last) {
        if (last - first <= 1) return true;
        int root = sequence[last];
        int currIndex = first;
        // 在二叉搜索树中左子树节点的值均小于根节点的值
        while(currIndex < last && sequence[currIndex] <= root) {
            currIndex++;
        }
        // 在二叉搜索树中右子树节点的值均大于根节点的值
        for (int i = currIndex; i < last; i++) {
            if (sequence[i] < root) return false;
        }
        // 判断左右子树是不是二叉搜索树
        return verify(sequence, first, currIndex-1) && verify(sequence, currIndex, last-1);
    }
}
```

# 34. 二叉树中和为某一值的路径

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
        // if (root.val > target) return; // 加了可能反而有错
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

// ---------------------------------------------
class Solution {
    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        // ArrayList<ArrayList<Integer>> res = new ArrayList<>(); // 不行，报错
        List<List<Integer>> res = new ArrayList<>(); // 此处注意
        dfs(res, new ArrayList<Integer>(), root, sum);
        return res;
    }

    public void dfs(List<List<Integer>> res, ArrayList<Integer> list, TreeNode root, int target) {
        if (root == null) return;
        // if (root.val < target) return; // 当节点值与target为负，该语句不行
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

# 35. 复杂链表的复制

[NowCoder](https://www.nowcoder.com/practice/f836b2c43afc4b35ad6adc41ec941dba?tpId=13&tqId=11178&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 输入一个复杂链表（每个节点中有节点值，以及两个指针，一个指向下一个节点，另一个特殊指针指向任意一个节点），返回结果为复制后复杂链表的 head。
>
> ```java
> public class RandomListNode {
>     int label;
>     RandomListNode next = null;
>     RandomListNode random = null;
> 
>     RandomListNode(int label) {
>         this.label = label;
>     }
> }
> ```
>
> [![img](https://camo.githubusercontent.com/55fd47439da7d3f04cd8400b5391f6d2d9241126/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f36366130313935332d353330332d343362312d383634362d3063373762383235653938302e706e67)](https://camo.githubusercontent.com/55fd47439da7d3f04cd8400b5391f6d2d9241126/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f36366130313935332d353330332d343362312d383634362d3063373762383235653938302e706e67)

**解题思路**

> 第一步，在每个节点的后面插入复制的节点。
>
> [![img](https://camo.githubusercontent.com/4c10528d868cd8f9ed7637ce914ed8a30e28b5e3/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f64666435643366382d363733632d343836622d386563662d6432303832313037623637622e706e67)](https://camo.githubusercontent.com/4c10528d868cd8f9ed7637ce914ed8a30e28b5e3/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f64666435643366382d363733632d343836622d386563662d6432303832313037623637622e706e67)
>
> 第二步，对复制节点的 random 链接进行赋值。
>
> [![img](https://camo.githubusercontent.com/b8ada1e0c81faefdbd5049e480ecb291c15a4bf4/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f63616662666562382d376466652d346330612d613363392d3735306565623832343036382e706e67)](https://camo.githubusercontent.com/b8ada1e0c81faefdbd5049e480ecb291c15a4bf4/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f63616662666562382d376466652d346330612d613363392d3735306565623832343036382e706e67)
>
> 第三步，拆分。
>
> [![img](https://camo.githubusercontent.com/5b02e0b318fa116dcc8a474feecadb514c070e45/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f65313531623564662d353339302d343336352d623636652d6231333063643235336331322e706e67)](https://camo.githubusercontent.com/5b02e0b318fa116dcc8a474feecadb514c070e45/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f65313531623564662d353339302d343336352d623636652d6231333063643235336331322e706e67)

```java
class Solution {
    public Node copyRandomList(Node head) {
        if (head == null) return null;
        // 插入新节点：将每个新节点插入到每个旧节点之后
        Node cur = head;
        while(cur != null) {
            Node clone = new Node(cur.val);
            clone.next = cur.next;
            cur.next = clone;
            cur = clone.next;
        }
		// 建立 random 链接
        cur = head;
        while(cur != null) {
            Node clone = cur.next;
            if (cur.random != null) {
                clone.random = cur.random.next;
            }
            cur = clone.next;
        }
		// 拆分
        cur = head;
        Node pClone = head.next;
        while(cur.next != null) {
            Node temp = cur.next;
            cur.next = cur.next.next;
            cur = temp;
        }
        return pClone;
    }
}
```

# 36. 二叉搜索树与双向链表

[NowCoder](https://www.nowcoder.com/practice/947f6eb80d944a84850b0538bf0ec3a5?tpId=13&tqId=11179&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 输入一棵二叉搜索树，将该二叉搜索树转换成一个排序的双向链表。要求不能创建任何新的结点，只能调整树中结点指针的指向。
>
> [![img](https://camo.githubusercontent.com/44d63822cebf803e2655cf5557187d547fd78b1d/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f30356130386632652d393931342d346137372d393265662d6165626561656366346636362e6a7067)](https://camo.githubusercontent.com/44d63822cebf803e2655cf5557187d547fd78b1d/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f30356130386632652d393931342d346137372d393265662d6165626561656366346636362e6a7067)

**解题思路**

```java
public class Solution {
    private TreeNode pre; // pre为当前节点的前驱节点
    private TreeNode head; // head为头结点
    public TreeNode Convert(TreeNode pRootOfTree) {
        if (pRootOfTree == null) return null;
        inorder(pRootOfTree);
        return head;
    }
    
    public void inorder(TreeNode root) {
        if (root == null) return;
        inorder(root.left); // 排序左子树
        // pre为当前节点root的前驱，则root.left = pre
        root.left = pre;
        if (pre != null) { // 如果root的前驱节点不为null
            pre.right = root;
        } else head = root; // 如果root的前驱节点为null，则说明头结点head为当前节点
        pre = root; // root的右子树的前驱节点为root，所以 pre=root;
        inorder(root.right); // 排序右子树
    }
}
```

# 37. 序列化二叉树

[NowCoder](https://www.nowcoder.com/practice/cf7e25aa97c04cc1a68c8f040e71fb84?tpId=13&tqId=11214&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 请实现两个函数，分别用来序列化和反序列化二叉树。

**解题思路**

```java
public class Codec {
    private String deserializeStr;
    // Encodes a tree to a single string.
    public String serialize(TreeNode root) {
        if (root == null) return new String("#");
        return root.val + " " + serialize(root.left) + " " + serialize(root.right);
    }
    // Decodes your encoded data to tree.
    public TreeNode deserialize(String data) {
        deserializeStr = data;
        return deserialize();
    }
    private TreeNode deserialize() {
        if (deserializeStr == null) return null;
        int index = deserializeStr.indexOf(" ");
        /*String nodeVal;
        if (index == -1) {
            nodeVal = deserializeStr;
            deserializeStr = "";
        }
        else {
            nodeVal = deserializeStr.substring(0, index);
            deserializeStr = deserializeStr.substring(index+1);
        }*/
        String nodeVal = index == -1 ? deserializeStr : deserializeStr.substring(0, index);
    	deserializeStr = index == -1 ? "" : deserializeStr.substring(index + 1);
        if (nodeVal.equals("#")) return null;
        int val = Integer.valueOf(nodeVal);
        TreeNode node = new TreeNode(val);
        node.left = deserialize();
        node.right = deserialize();
        return node;
    }
}
```

# 38. 字符串的排列

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

# 39. 数组中出现次数超过一半的数字

[NowCoder](https://www.nowcoder.com/practice/e8a1b01a2df14cb2b228b30ee6a92163?tpId=13&tqId=11181&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 数组中有一个数字出现的次数超过数组长度的一半，请找出这个数字。例如输入一个长度为9的数组{1,2,3,2,2,2,5,4,2}。由于数字2在数组中出现了5次，超过数组长度的一半，因此输出2。如果不存在则输出0。

**解题思路**

1. 如果数组中一定有一个数字出现的次数超过数组长度的一半，则数组排序后的中间位置一定有该数字，返回即可。

```java
class Solution {
    public int majorityElement(int[] nums) {
        if (nums == null || nums.length == 0) return -1;
        int length = nums.length;
        Arrays.sort(nums);
        return nums[length/2];
    }
}
```

2. 我们在遍历数组的时候保留两个值：一个是数组中的一个数字；另一个是次数。当我们遍历到下一个数字的时候，如果下一个数字和我们之前保存的数字相同，则次数加 1；如果不同，则次数减 1。如果次数为 0，则我们需要保存下一个数字，并把次数设为 1。由于我们要找的数字出现的次数比其他所有数字出现的次数之和还要多，那么我们要找的数字肯定是最后一次将次数设为 1时对应的数字。

```java
class Solution {
    public int majorityElement(int[] nums) {
        // if (nums == null || nums.length == 0) return -1;
        int majority = nums[0];
        for (int i = 1, count = 1; i < nums.length; i++) {
            if (nums[i] != majority) count--;
            else count++;
            if (count == 0){
                majority = nums[i];
                count = 1;
            }
        }
        // 如果题目中已经说明：数组中一定存在一个数字，其出现次数大于数组长度一半，则可直接返回
        return majority;
    }
    
    public int MoreThanHalfNum_Solution(int[] nums) {
        if (nums == null || nums.length == 0) return -1;
        int majority = nums[0];
        for (int i = 1, cnt = 1; i < nums.length; i++) {
            // cnt = nums[i] == majority ? cnt + 1 : cnt - 1;
            if (nums[i] != majority) cnt--;
            else cnt++;
            if (cnt == 0) {
                majority = nums[i];
                cnt = 1;
            }
        }
        // 如果题目中没有说明：数组中一定存在一个数字，其出现次数大于数组长度一半，则需判断是否有该数字。
        int cnt = 0;
        for (int val : nums)
            if (val == majority)
                cnt++;
        return cnt > nums.length / 2 ? majority : -1;
    }
}
```

3. 使用 HashMap

```java
class Solution {
    public int majorityElement(int[] nums) {
        if (nums == null || nums.length == 0) return -1;
        HashMap<Integer, Integer> hashmap = new HashMap<>();
        for (int i = 0; i< nums.length; i++) {
            if (hashmap.containsKey(nums[i])) {
                // 这里不能直接map.get(nums[i])++;
                hashmap.put(nums[i], hashmap.get(nums[i])+1);
            } else {
                hashmap.put(nums[i], 1);
            }
            // 注意：这里if不能放在第一个if中，否则会在数组长度为1时出错，无法返回正确的nums[i]的值
            // 这里i>=length，之所以带等号，也是为了满足长度为1的情况，因为i从0开始
            // 按照题目要求，必须众数次数超过长度的一半，则有第一个判断条件，相当于剪枝，当然下面的第一个判断条件也可以不加
            if (i >= nums.length / 2 && hashmap.get(nums[i]) > nums.length / 2) {
                return nums[i];
            }
        }
        return -1;
    }
}
```

# 40. 最小的 K 个数

[NowCoder](https://www.nowcoder.com/practice/6a296eb82cf844ca8539b57c23e6e9bf?tpId=13&tqId=11182&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 输入n个整数，找出其中最小的K个数。例如输入4,5,1,6,2,7,3,8这8个数字，则最小的4个数字是1,2,3,4,。

**解题思路**

**选择排序的思想**

- 首先创建一个大小为 k 的数据容器来存储最小的 k 个数字，接下来每次从输入的 n 个整数中读入一个数；

- 如果容器中已有的数字少于 k 个，则直接把这次读入的整数放入容器中；

- 如果容器中已有 k 个数字，即容器已满，则不能添加数字，只能替换已有数字：找出容器中已有 k 个数字的最大值，然后将其与当前待插入的数字进行比较，如果大，则替换，如果不大，则不替换，继续遍历下一个数字。

该容器可以为数组，也可以为**最大堆**。

```java
class Solution {
    public int[] getLeastNumbers(int[] arr, int k) {
        if (arr == null || arr.length == 0 || k <= 0 || k > arr.length) return new int[0];
        int[] res = new int[k];
        for (int i = 0, j = 0; i < arr.length; i++) {
            if (j < k) res[j++] = arr[i];
            else {
                int indexOfMax = getindexOfMax(res);
                if (res[indexOfMax] > arr[i]) res[indexOfMax] = arr[i];
            }
        }
        return res;
    }
    private int getindexOfMax(int[] nums) {
        if (nums.length == 1) return 0;
        int indexOfMax = 0;
        for (int i = 1; i < nums.length; i++) {
            if (nums[indexOfMax] < nums[i]) indexOfMax = i;
        }
        return indexOfMax;
    }
}
```

**快速选择**

- 复杂度：O(N) + O(1)
- 只有当允许修改数组元素时才可以使用

快速排序的 partition() 方法，会返回一个整数 j 使得 a[l..j-1] 小于等于 a[j]，且 a[j+1..h] 大于等于 a[j]，此时 a[j] 就是数组的第 j 大元素。可以利用这个特性找出数组的第 K 个元素，这种找第 K 个元素的算法称为快速选择算法。

```java
public ArrayList<Integer> GetLeastNumbers_Solution(int[] nums, int k) {
    ArrayList<Integer> ret = new ArrayList<>();
    if (k > nums.length || k <= 0)
        return ret;
    findKthSmallest(nums, k - 1);
    /* findKthSmallest 会改变数组，使得前 k 个数都是最小的 k 个数 */
    for (int i = 0; i < k; i++)
        ret.add(nums[i]);
    return ret;
}

public void findKthSmallest(int[] nums, int k) {
    int l = 0, h = nums.length - 1;
    while (l < h) {
        int j = partition(nums, l, h);
        if (j == k)
            break;
        if (j > k)
            h = j - 1;
        else
            l = j + 1;
    }
}

private int partition(int[] nums, int l, int h) {
    int p = nums[l];     /* 切分元素 */
    int i = l, j = h + 1;
    while (true) {
        while (i != h && nums[++i] < p) ;
        while (j != l && nums[--j] > p) ;
        if (i >= j)
            break;
        swap(nums, i, j);
    }
    swap(nums, l, j);
    return j;
}

private void swap(int[] nums, int i, int j) {
    int t = nums[i];
    nums[i] = nums[j];
    nums[j] = t;
}
```

大小为 K **的最小堆**

- 复杂度：O(NlogK) + O(K)
- 特别适合处理海量数据

应该使用大顶堆来维护最小堆，而不能直接创建一个小顶堆并设置一个大小，企图让小顶堆中的元素都是最小元素。

维护一个大小为 K 的最小堆过程如下：在添加一个元素之后，如果大顶堆的大小大于 K，那么需要将大顶堆的堆顶元素去除。

```java
public ArrayList<Integer> GetLeastNumbers_Solution(int[] nums, int k) {
    if (k > nums.length || k <= 0)
        return new ArrayList<>();
    PriorityQueue<Integer> maxHeap = new PriorityQueue<>((o1, o2) -> o2 - o1);
    for (int num : nums) {
        maxHeap.add(num);
        if (maxHeap.size() > k)
            maxHeap.poll();
    }
    return new ArrayList<>(maxHeap);
}
```

# 41.1 数据流中的中位数

[NowCoder](https://www.nowcoder.com/practice/9be0172896bd43948f8a32fb954e1be1?tpId=13&tqId=11216&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 如何得到一个数据流中的中位数？如果从数据流中读出奇数个数值，那么中位数就是所有数值排序之后位于中间的数值。如果从数据流中读出偶数个数值，那么中位数就是所有数值排序之后中间两个数的平均值。
>
>  我们使用 Insert() 方法读取数据流，使用 GetMedian() 方法获取当前读取数据的中位数。 

**解题思路**

```java
class MedianFinder {
    private PriorityQueue<Integer> left; /* 大顶堆，存储左半边元素 */
    private PriorityQueue<Integer> right; /* 小顶堆，存储右半边元素，并且右半边元素都大于左半边 */
    private int N; /* 当前数据流读入的元素个数 */
    /** initialize your data structure here. */
    public MedianFinder() {
        N = 0;
        left = new PriorityQueue<>((o1, o2) -> o2 - o1); // 最大堆
        right = new PriorityQueue<>((o1, o2) -> o1 - o2); // 最小堆
    }
    
    public void addNum(int num) {
        if (N % 2 == 0) { // 维持左右两个容器中元素个数的平衡
            /* N 为偶数的情况下插入到右半边。
             * 因为右半边元素都要大于左半边，但是新插入的元素不一定比左半边元素来的大，
             * 因此需要先将元素插入左半边，然后利用左半边为大顶堆的特点，取出堆顶元素即为最大元素，此时插入右半边 */
            left.add(num); // 将num插入到左边容器中，就不能保证左边容器中所有元素值都小于右边容器了
            right.add(left.poll()); // 将左边容器的最大值取出，插入到右边容器中
        } else {
            right.add(num); // 将num插入到右边容器中，就不能保证右边容器中所有元素值都大于左边容器了
            left.add(right.poll()); // 将右边容器的最小值取出，插入到左边容器中
        }
        N++;
    }
    
    public double findMedian() {
        if (N % 2 == 0) {
            return (left.peek() + right.peek()) / 2.0;
        } else {
            return right.peek() * 1.0;
        }
    }
}
```

# 41.2 字符流中第一个不重复的字符

[NowCoder](https://www.nowcoder.com/practice/00de97733b8e4f97a3fb5c680ee10720?tpId=13&tqId=11207&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 请实现一个函数用来找出字符流中第一个只出现一次的字符。例如，当从字符流中只读出前两个字符 "go" 时，第一个只出现一次的字符是 "g"。当从该字符流中读出前六个字符“google" 时，第一个只出现一次的字符是 "l"。

**解题思路**

```java
import java.util.Map;
import java.util.LinkedHashMap;
public class Solution {
    private Map<Character, Boolean> map = new LinkedHashMap<>();
    //Insert one char from stringstream
    public void Insert(char ch) {
        if (!map.containsKey(ch)) map.put(ch, true);
        else map.put(ch, false);
    }
  //return the first appearence once char in current stringstream
    public char FirstAppearingOnce() {
        if (map.isEmpty()) return '#';
        for (Map.Entry<Character, Boolean> m: map.entrySet()) {
            if (m.getValue()) return m.getKey();
        }
        return '#';
    }
}
```

# 42. 连续子数组的最大和

[NowCoder](https://www.nowcoder.com/practice/459bd355da1549fa8a49e350bf3df484?tpId=13&tqId=11183&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> {6, -3, -2, 7, -15, 1, 2, 2}，连续子数组的最大和为 8（从第 0 个开始，到第 3 个为止）。

**解题思路**

```java
class Solution {
    public int maxSubArray(int[] nums) {
        if (nums == null || nums.length == 0) return -1;
        int res = Integer.MIN_VALUE;
        int sum = 0;
        for (int i = 0; i < nums.length; i++) {
            if (sum <= 0) sum = nums[i];
            else sum += nums[i];
            if (sum > res) res = sum;
        }
        return res;
    }
}
```

# 43. 从 1 到 n 整数中 1 出现的次数？？？？

[NowCoder](https://www.nowcoder.com/practice/bd7f978302044eee894445e244c7eee6?tpId=13&tqId=11184&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 求出1~13的整数中1出现的次数,并算出100~1300的整数中1出现的次数？为此他特别数了一下1~13中包含1的数字有1、10、11、12、13因此共出现6次,但是对于后面问题他就没辙了。ACMer希望你们帮帮他,并把问题更加普遍化,可以很快的求出任意非负整数区间中1出现的次数（从1 到 n 中1出现的次数）。 

**解题思路**

```java

```

# 44. 数字序列中的某一位数字？？？

> 找规律

**题目描述**

> 数字以 0123456789101112131415... 的格式序列化到一个字符串中，求这个字符串的第 index 位。

**解题思路**

```
class Solution {
    public int findNthDigit(int n) {
        if (n < 0) return -1;
        int digits = 1;
        while(true) {
            int amount = getAmountOfPlace(digits); // digits位数字个数
            int totalAmount = amount * digits; // 这些数字共有totalAmount位
            if (n < totalAmount)
                return getDigitAtIndex(n, digits);
            n -= totalAmount;
            digits++;
        }
    }

    /*
        digits位数字有多少个
        如果digits=2，则有10，11，12，~， 99，共有90个
    */
    public int getAmountOfPlace(int digits) {
        if (digits == 1) {
            return 10;
        }
        return (int)Math.pow(10, digits - 1) * 9;
    }

    /*
        digits位数的起始数字
        如果digits=2，则为起始位置为10；如果为3，则为100

    */
    private int getBeginNumberOfPlace(int digits) {
        if (digits == 1)
            return 0;
        return (int) Math.pow(10, digits - 1);
    }

    /*
        在 digits 位数组成的字符串中，第 index 个数
    */
    private int getDigitAtIndex(int index, int digits) {
        int beginNumber = getBeginNumberOfPlace(digits);
        int shiftNumber = index / digits;
        String number = beginNumber + shiftNumber + "";
        int count = index % digits;
        return number.charAt(count) - '0';
    }
}
```

# 45. 把数组排成最小的数

[NowCoder](https://www.nowcoder.com/practice/8fecd3f8ba334add803bf2a06af1b993?tpId=13&tqId=11185&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

> 找规律、大数问题

**题目描述**

> 输入一个正整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。例如输入数组 {3，32，321}，则打印出这三个数字能排成的最小数字为 321323。

**解题思路**

```java
// 使用内置排序
class Solution {
    public String minNumber(int[] nums) {
        if (nums == null || nums.length == 0) return "";
        int n = nums.length;
        String[] strs = new String[n];
        for (int i = 0; i < n; i++) {
            strs[i] = nums[i] + ""; // strs[i] = String.valueOf(nums[i]);
        }
        Arrays.sort(strs, (s1, s2) -> (s1 + s2).compareTo(s2 + s1));
        /*
        for (int i = 1; i < n; i++) {
            strs[0] += strs[i];
        }
        return strs[0];
        */
        StringBuffer sb = new StringBuffer();
        for (String str: strs) {
            sb.append(str);
        }
        return sb.toString();
    }
}

// 使用快速排序
class Solution {
    public String minNumber(int[] nums) {
        if (nums == null || nums.length == 0) return "";
        int n = nums.length;
        String[] strs = new String[n];
        for (int i = 0; i < n; i++) {
            strs[i] = nums[i] + ""; // strs[i] = String.valueOf(nums[i]);
        }
        fastSort(strs, 0, n-1);
        StringBuffer sb = new StringBuffer();
        for (String str: strs) {
            sb.append(str);
        }
        return sb.toString();
    }
    
    private void fastSort(String[] strs, int left, int right) {
        if (left >= right) return;
        int l = left, r = right;
        while(l < r) { // 排序方式：strs[l] ~ strs[left] ~ strs[r]
            // 如果strs[r]与strs[left]拼接 大于 strs[left]与strs[r]拼接，则将strs[r]不动
            while((strs[r]+strs[left]).compareTo(strs[left]+strs[r]) >= 0 && l < r) r--;
            // 如果strs[l]与strs[left]拼接 小于 strs[left]与strs[l]拼接，则将strs[l]不动
            while((strs[l]+strs[left]).compareTo(strs[left]+strs[l]) <= 0 && l < r) l++;
            String temp = strs[l];
            strs[l] = strs[r];
            strs[r] = temp;
        }
        String temp = strs[l];
        strs[l] = strs[left];
        strs[left] = temp;
        fastSort(strs, left, l-1);
        fastSort(strs, l+1, right);
    }
}
```

# 46. 把数字翻译成字符串

[Leetcode](https://leetcode.com/problems/decode-ways/description/)

**题目描述**

> 给定一个数字，按照如下规则翻译成字符串：1 翻译成“a”，2 翻译成“b”... 26 翻译成“z”。一个数字有多种翻译可能，例如 12258 一共有 5 种，分别是 abbeh，lbeh，aveh，abyh，lyh。实现一个函数，用来计算一个数字有多少种不同的翻译方法。

**解题思路**

```java
class Solution {
    public int translateNum(int num) {
        String str = String.valueOf(num);
        return translateNum(str);
    }

    public int translateNum(String str) {
        if (str == "" || str.length() == 0) return 1;
        if (str.length() == 1) return 1;
        String str2 = str.substring(0, 2);
        // if (Integer.parseInt(str2) >= 10 && Integer.parseInt(str2) <= 25) {
        if (str2.compareTo("10") >= 0 && str2.compareTo("25") <= 0) {
            return translateNum(str.substring(1, str.length())) + 
                    translateNum(str.substring(2, str.length()));
        } else return translateNum(str.substring(1, str.length()));
    }
}
```

# 47. 礼物的最大价值

[NowCoder](https://www.nowcoder.com/questionTerminal/72a99e28381a407991f2c96d8cb238ab)

**题目描述**

> 在一个 m*n 的棋盘的每一个格都放有一个礼物，每个礼物都有一定价值（大于 0）。从左上角开始拿礼物，每次向右或向下移动一格，直到右下角结束。给定一个棋盘，求拿到礼物的最大价值。例如，对于如下棋盘

```
1    10   3    8
12   2    9    6
5    7    4    11
3    7    16   5
```

> 礼物的最大价值为 1+12+5+7+7+16+5=53。

**解题思路**

![1595295781757](assets\1595295781757.png)

![1595295858553](assets\1595295858553.png)

```java
class Solution {
    public int maxValue(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) return 0;
        int m = grid.length, n = grid[0].length;
        int[][] dp = new int[m][n];
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (i == 0 && j == 0) dp[i][j] = grid[i][j]; // dp[0][0] = grid[0][0];
                // dp[0][j] = dp[0][j-1] + grid[0][j];
                else if (i == 0 && j != 0) dp[i][j] = dp[i][j-1] + grid[i][j]; 
                // dp[i][0] = dp[i-1][0] + grid[i][0];
                else if (i != 0 && j == 0) dp[i][j] = dp[i-1][j] + grid[i][j];
                else if (i != 0 && j != 0) dp[i][j] = Math.max(dp[i-1][j], dp[i][j-1]) + grid[i][j];
            }
        }
        return dp[m-1][n-1];
    }
}

// 空间复杂度优化
class Solution {
    public int maxValue(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) return 0;
        int m = grid.length, n = grid[0].length;
        for (int i = 0; i < m; i++) {
            for (int j = 0; j < n; j++) {
                if (i == 0 && j == 0) continue; // dp[0][0] = grid[0][0];
                // dp[0][j] = dp[0][j-1] + grid[0][j];
                else if (i == 0 && j != 0) grid[i][j] = grid[i][j-1] + grid[i][j]; 
                // dp[i][0] = dp[i-1][0] + grid[i][0];
                else if (i != 0 && j == 0) grid[i][j] += grid[i-1][j];
                else if (i != 0 && j != 0) grid[i][j] += Math.max(grid[i-1][j], grid[i][j-1]);
            }
        }
        return grid[m-1][n-1];
    }
}

// 当 grid 矩阵很大时， i = 0 或 j = 0 的情况仅占极少数，相当循环每轮都冗余了一次判断。因此，可先初始化矩阵第一行和第一列，再开始遍历递推。
class Solution {
    public int maxValue(int[][] grid) {
        if (grid == null || grid.length == 0 || grid[0].length == 0) return 0;
        int m = grid.length, n = grid[0].length;
        for (int i = 1; i < m; i++) {
            grid[i][0] += grid[i-1][0];
        }
        for (int j = 1; j < n; j++) {
            grid[0][j] += grid[0][j-1]; 
        }
        for (int i = 1; i < m; i++) {
            for (int j = 1; j < n; j++) {
                grid[i][j] += Math.max(grid[i-1][j], grid[i][j-1]);
            }
        }
        return grid[m-1][n-1];
    }
}
```

# 48. 最长不含重复字符的子字符串

**题目描述**

> 输入一个字符串（只包含 a~z 的字符），求其最长不含重复字符的子字符串的长度。例如对于 arabcacfr，最长不含重复字符的子字符串为 acfr，长度为 4。

**解题思路**

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if (s == null || s.length() == 0) return 0;
        int[] dp = new int[s.length()]; // dp[i]为以第i个字符为结尾的不包含重复字符的子字符串的最长长度
        char[] chars = s.toCharArray();
        Map<Character, Integer> map = new HashMap<>();
        dp[0] = 1;
        map.put(chars[0], 0);
        for (int i = 1; i < chars.length; i++) {
            if (!map.containsKey(chars[i])){ // 如果之前没有字符chars[i]
                map.put(chars[i], i);
                dp[i] = dp[i-1] + 1;
            } 
            else if ((i - map.get(chars[i])) <= dp[i-1]) {// 如果有字符chars[i]，则判断其与dp[i-1]的距离
                // 如果小于等于，说明该字符在dp[i-1]对应的最长字符串中
                dp[i] = i - map.get(chars[i]);
                map.put(chars[i], i);
            } else if ((i - map.get(chars[i])) > dp[i-1]) {
                // 如果大于，说明该字符不在dp[i-1]对应的最长字符串中
                dp[i] = dp[i-1] + 1;
                map.put(chars[i], i);
            }
        }
        int res = dp[0];
        for (int i = 1; i < dp.length; i++) {
            if (res < dp[i]) res = dp[i];
        }
        return res;
    }
}

class Solution {
    public int lengthOfLongestSubstring(String s) {
        if (s == null || s.length() == 0) return 0;
        int[] dp = new int[s.length()];
        char[] chars = s.toCharArray();
        Map<Character, Integer> map = new HashMap<>();
        dp[0] = 1;
        map.put(chars[0], 0);
        int res = dp[0];
        for (int i = 1; i < chars.length; i++) {
            if (!map.containsKey(chars[i]) || (i - map.get(chars[i])) > dp[i-1]){
                dp[i] = dp[i-1] + 1;
            } else if ((i - map.get(chars[i])) <= dp[i-1]) {
                dp[i] = i - map.get(chars[i]);
            }
            if (res < dp[i]) res = dp[i];
            map.put(chars[i], i);
        }
        return res;
    }
}

class Solution {
    public int lengthOfLongestSubstring(String s) {
        Map<Character, Integer> dic = new HashMap<>();
        int res = 0, tmp = 0;
        for(int j = 0; j < s.length(); j++) {
            int i = dic.getOrDefault(s.charAt(j), -1); // 获取索引 i
            dic.put(s.charAt(j), j); // 更新哈希表
            tmp = tmp < j - i ? tmp + 1 : j - i; // dp[j - 1] -> dp[j]
            res = Math.max(res, tmp); // max(dp[j - 1], dp[j])
        }
        return res;
    }
}
```

# 49. 丑数

[NowCoder](https://www.nowcoder.com/practice/6aa9e04fc3794f68acf8778237ba065b?tpId=13&tqId=11186&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**:

> 把只包含因子 2、3 和 5 的数称作丑数（Ugly Number）。例如 6、8 都是丑数，但 14 不是，因为它包含因子 7。习惯上我们把 1 当做是第一个丑数。求按从小到大的顺序的第 N 个丑数。

**解题思路**:

![1595383010484](assets\1595383010484.png)

![1595383037844](assets\1595383037844.png)

```java
// 暴力法
class Solution {
    public int nthUglyNumber(int n) {
        if (n <= 0) return 0;
        int number = 0;
        int count = 0;
        while(count < n) {
            ++number;
            if (isUgly(number)) count++;
        }
        return number;
    }

    private boolean isUgly(int number) {
        while(number % 2 == 0) number /= 2;
        while(number % 3 == 0) number /= 3;
        while(number % 5 == 0) number /= 5;
        if (number == 1) return true;
        else return false;
    }
}
// 动态规划
class Solution {
    public int nthUglyNumber(int n) {
        if (n <= 0) return 0;
        int a = 0, b = 0, c = 0;
        int[] dp = new int[n];
        dp[0] = 1;
        for (int i = 1; i < n; i++) {
            int next2 = dp[a] * 2, next3 = dp[b] * 3, next5 = dp[c] * 5;
            dp[i] = Math.min(next2, Math.min(next3, next5));
            if (dp[i] == next2) a++;
            if (dp[i] == next3) b++;
            if (dp[i] == next5) c++;
        }
        return dp[n-1];
    }
}
```

# 50. 第一个只出现一次的字符位置

[NowCoder](https://www.nowcoder.com/practice/1c82e8cf713b4bbeb2a5b31cf5b0417c?tpId=13&tqId=11187&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 在一个字符串中找到第一个只出现一次的字符，并返回它的位置。

```
Input: abacc
Output: b
```

**解题思路**

```java
// 哈希表 HashMap
class Solution {
    public char firstUniqChar(String s) {
        if (s == "") return ' ';
        char[] chars = s.toCharArray();
        Map<Character, Boolean> map = new HashMap<>();
        for (int i = 0; i < chars.length; i++) {
            if (map.containsKey(chars[i])) map.put(chars[i], false);
            else map.put(chars[i], true);
        }
        // 因为HashMap是无序的，所以通过字符数组来循环遍历
        for (int i = 0; i < chars.length; i++) {
            if (map.get(chars[i])) return chars[i];
        }
        return ' ';
    }
}
// 有序哈希表 LinkedHashMap
class Solution {
    public char firstUniqChar(String s) {
        if (s == "") return ' ';
        char[] chars = s.toCharArray();
        Map<Character, Boolean> map = new LinkedHashMap<>();
        for (int i = 0; i < chars.length; i++) {
            if (map.containsKey(chars[i])) map.put(chars[i], false);
            else map.put(chars[i], true);
        }
        // LinkedHahMap是有序的，所以可以通过哈希表来遍历（插入顺序）
        for (Map.Entry<Character, Boolean> m: map.entrySet()) {
            if (m.getValue()) return m.getKey();
        }
        return ' ';
    }
}
```

最直观的解法是使用 HashMap 对出现次数进行统计，但是考虑到要统计的字符范围有限，因此可以使用整型数组代替 HashMap，从而将空间复杂度由 O(N) 降低为 O(1)。

在Java中字符（char）是一个长度为16的数据类型，因为该题是英文字母，所以我们总共考虑256（长度为8）种可能。于是我们创建一个长度为256的数组，每个字母根据其ASCII码值作为数组的下标对应数组的一个数字，而数组中存储的是每个字符出现的次数。这样我们就创建了一个大小为256、以字符ASCII码为键值的哈希表了

```java
public int FirstNotRepeatingChar(String str) {
    int[] cnts = new int[256];
    for (int i = 0; i < str.length(); i++)
        cnts[str.charAt(i)]++;
    for (int i = 0; i < str.length(); i++)
        if (cnts[str.charAt(i)] == 1)
            return i;
    return -1;
}
```

以上实现的空间复杂度还不是最优的。考虑到只需要找到只出现一次的字符，那么需要统计的次数信息只有 0,1,更大，使用两个比特位就能存储这些信息。

```java
public int FirstNotRepeatingChar2(String str) {
    BitSet bs1 = new BitSet(256);
    BitSet bs2 = new BitSet(256);
    for (char c : str.toCharArray()) {
        if (!bs1.get(c) && !bs2.get(c))
            bs1.set(c);     // 0 0 -> 0 1
        else if (bs1.get(c) && !bs2.get(c))
            bs2.set(c);     // 0 1 -> 1 1
    }
    for (int i = 0; i < str.length(); i++) {
        char c = str.charAt(i);
        if (bs1.get(c) && !bs2.get(c))  // 0 1
            return i;
    }
    return -1;
}
```

# 51. 数组中的逆序对-----？

[NowCoder](https://www.nowcoder.com/practice/96bd6684e04a44eb80e6a68efc0ec6c5?tpId=13&tqId=11188&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 在数组中的两个数字，如果前面一个数字大于后面的数字，则这两个数字组成一个逆序对。输入一个数组，求出这个数组中的逆序对的总数。

**解题思路**

# 52. 两个链表的第一个公共结点

[NowCoder](https://www.nowcoder.com/practice/6ab1d9a29e88450685099d45c9e31e46?tpId=13&tqId=11189&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

[![img](https://camo.githubusercontent.com/ef618368202e8593d1ed0ccf6aa2dda337bbce34/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f35663163623939392d636239612d346636632d613061662d6439303337373239356162382e706e67)](https://camo.githubusercontent.com/ef618368202e8593d1ed0ccf6aa2dda337bbce34/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f35663163623939392d636239612d346636632d613061662d6439303337373239356162382e706e67)

**解题思路**

**统计长度法：**考虑有公共节点的两个链表的特点：其结构看起来像一个倒下的Y，从第一个公共节点开始，之后它们所有的节点都是重合的，不可能再出现分叉。只是Y两端不重合的部分可能长度不同，我们可以采取以下方法来获得公共节点：首先遍历两个链表得到它们的长度,就能知道哪个链表比较长,以及长的链表比短的链表多几个节点。在第二次遍历的时候,在较长的链表上先走若干步,接着同时在两个链表上遍历,找到的第一个相同的节点就是它们的第一个公共节点。

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if (headA == null || headB == null) return null;
        ListNode curA = headA, curB = headB;
        int lenA = 0, lenB = 0;
        while(curA != null && ++lenA > 0) curA = curA.next;
        while(curB != null && ++lenB > 0) curB = curB.next;
        ListNode longList = headA, shortList = headB;
        int difLen = lenA - lenB;
        if (lenA < lenB) {
            longList = headB;
            shortList = headA;
            difLen = lenB - lenA;
        }
        for (int i = 0; i < difLen; i++) longList = longList.next;
        while(longList != shortList) {
            longList = longList.next;
            shortList = shortList.next;
        }
        return longList;
    }
}
```

**相遇法：**两链表构成的Y形结构的长度时固定的，即两个分叉部分(可能长度不一)+一个重合部分；(不存在重合节点时，则后面重合部分的长度不存在)
对于node1，其先走headA对应的分叉和重合部分(若有的话)，即全部的链表A的长度，之后从headB开始，走剩下的那个分叉。
对于node2,其先走headB对应的分叉和重合部分(若有的话)，即全部的链表B的长度，之后从headA开始，走剩下的那个分叉。
由于Y的长度时固定的，两指针又是同时开始，步速一致，若存在重合节点，两者最终一定可以汇合。

```java
public class Solution {
    public ListNode getIntersectionNode(ListNode headA, ListNode headB) {
        if (headA == null || headB == null) return null;
        ListNode curA = headA, curB = headB;
        while(curA != curB) {
// 这里while的判断条件汇总了Y的所有情况
// 1.Y退化为\ /，即不存在公共节点，此时最终离开循环时,node1=node2=null,两链表A，B长度相同时，node1,node2只要分别遍历完自己的那条链表就行; 两链表长度不同时,node1先遍历A链表,再遍历B链表,node2先遍历B链表,再遍历A链表,	由于A链表+B链表长度固定，等价于node1,node2分别遍历一条长度为A+B的链表，最终一起指向null，循环结束；
// 2.Y两分叉长度一致时，即A B链表长度相同且存在公共节点，此时，不等node1走完A链表(node2走完B链表)即可获得公共节点;
// 3.Y两分叉不同且存在公共节点时，此时即为最开始分析时的思路，node1,node2分别走完Y的所有长度，并在节点处相遇。
            if (curA == null) curA = headB;
            else curA = curA.next;
            if (curB == null) curB = headA;
            else curB = curB.next;
        }
        return curA;
    }
```

# 53. 数字在排序数组中出现的次数

[NowCoder](https://www.nowcoder.com/practice/70610bf967994b22bb1c26f9ae901fa2?tpId=13&tqId=11190&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

```
Input:
nums = 1, 2, 3, 3, 3, 3, 4, 6
K = 3

Output:
4
```

**解题思路**:

```java
// 搜索左边边界
class Solution {
    public int search(int[] nums, int target) {
        if (nums == null || nums.length == 0) return 0;
        int left = binarySearch(nums, target);
        int rigth = binarySearch(nums, target+1);
        // if (left == nums.length || nums[left] != target) return 0;
        // else return rigth - left;
        return rigth - left;
    }

    public int binarySearch(int[] nums, int k) {
        int left = 0, right = nums.length;
        while(left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] >= k) right = mid;
            else if (nums[mid] < k) left = mid + 1;
        }
        return left;
    }
}

class Solution {
    public int search(int[] nums, int target) {
        if (nums == null || nums.length == 0) return 0;
        int left = binarySearch(nums, target);
        int rigth = binarySearch(nums, target+1);
        return rigth - left;
    }
    public int binarySearch(int[] nums, int k) {
        int left = 0, right = nums.length - 1;
        while(left <= right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] >= k) right = mid - 1;
            else if (nums[mid] < k) left = mid + 1;
        }
        return left;
    }
}

// 搜索右边边界
class Solution {
    public int search(int[] nums, int target) {
        if (nums == null || nums.length == 0) return 0;
        return binarySearch(nums, target) - binarySearch(nums, target-1);
    }
    public int binarySearch(int[] nums, int tar) {
        int i = 0, j = nums.length - 1;
        while(i <= j) {
            int m = (i + j) / 2;
            if(nums[m] <= tar) i = m + 1;
            else j = m - 1;
        }
        return i;
    } 
}
    
class Solution {
    public int search(int[] nums, int target) {
        if (nums == null || nums.length == 0) return 0;
        return binarySearch(nums, target) - binarySearch(nums, target-1);
    }
    public int binarySearch(int[] nums, int tar) {
        int i = 0, j = nums.length;
        while(i < j) {
            int m = (i + j) / 2;
            if(nums[m] <= tar) i = m + 1;
            else j = m;
        }
        return i - 1;
    } 
}
```

# 53.1. 0～n-1中缺失的数字

 [leetcode](https://leetcode-cn.com/problems/que-shi-de-shu-zi-lcof/)

**题目描述**：

> 一个长度为n-1的递增排序数组中的所有数字都是唯一的，并且每个数字都在范围0～n-1之内。在范围0～n-1内的n个数字中有且只有一个数字不在该数组中，请找出这个数字。

**解题思路**：

```java
class Solution {
    public int missingNumber(int[] nums) {
        if (nums == null || nums.length == 0) return -1;
        int left = 0, right = nums.length;
        while(left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] == mid) left = mid + 1;
            else if (nums[mid] > mid) right = mid;
        }
        return left;
    }
}

class Solution {
    public int missingNumber(int[] nums) {
        int left = 0, right = nums.length;
        while(left < right) {
            int mid = left + (right - left) / 2;
            if (nums[mid] != mid) {
                if (mid == 0 || nums[mid-1] == mid-1) return mid;
                right = mid;
            } else if (nums[mid] == mid) left = mid + 1;
        }
        return left;
    }
}
```

# 54. 二叉查找树的第 K 大结点

[NowCoder](https://www.nowcoder.com/practice/ef068f602dde4d28aab2b210e859150a?tpId=13&tqId=11215&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述：**

>  给定一棵二叉搜索树，请找出其中第k大的节点。 

**解题思路**

> 利用二叉查找树中序遍历有序的特点。

```java
class Solution {
    int num = 0, res = -1;
    public int kthLargest(TreeNode root, int k) {
        if (root == null || k <= 0) return -1;
        kthNode(root, k);
        return res;
    }

    public void kthNode(TreeNode root, int k) {
        if (root == null) return;
        kthNode(root.right, k);
        num++;
        if (num == k) {
            res = root.val;
            return;
        }
        kthNode(root.left, k);
    }
}
```



# 55.1 二叉树的深度

[NowCoder](https://www.nowcoder.com/practice/435fb86331474282a3499955f0a41e8b?tpId=13&tqId=11191&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 从根结点到叶结点依次经过的结点（含根、叶结点）形成树的一条路径，最长路径的长度为树的深度。
>
> [![img](https://camo.githubusercontent.com/dafe5b552c856ed0ab7d9ffb85e7db6eb85bbe35/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f62613335353130312d346139332d346337312d393466622d3164613833363339373237622e6a7067)](https://camo.githubusercontent.com/dafe5b552c856ed0ab7d9ffb85e7db6eb85bbe35/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f62613335353130312d346139332d346337312d393466622d3164613833363339373237622e6a7067)

**解题思路**

```java
// 递归
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) return 0;
        return Math.max(maxDepth(root.left), maxDepth(root.right)) + 1;
    }
}

// 层次遍历
class Solution {
    public int maxDepth(TreeNode root) {
        if (root == null) return 0;
        LinkedList<TreeNode> queue = new LinkedList<>();
        queue.offer(root);
        int res = 0;
        while(!queue.isEmpty()) {
            int size = queue.size();
            for (int i = 0; i < size; i++) {
                TreeNode node = queue.poll();
                if (node.left != null) queue.offer(node.left);
                if (node.right != null) queue.offer(node.right);
            }
            res++;
        }
        return res;
    }
}
```

# 55.2 平衡二叉树

[NowCoder](https://www.nowcoder.com/practice/8b3b95850edb4115918ecebdf1b4d222?tpId=13&tqId=11192&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 平衡二叉树左右子树高度差不超过 1。
>
> [![img](https://camo.githubusercontent.com/e290b71c96cdce17ad8172b1ed612467c27268ca/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f61663164313136362d363361662d343762362d396161332d3262663262643337626430332e6a7067)](https://camo.githubusercontent.com/e290b71c96cdce17ad8172b1ed612467c27268ca/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f61663164313136362d363361662d343762362d396161332d3262663262643337626430332e6a7067)

**解题思路**

```java
class Solution {
    public boolean isBalanced(TreeNode root) {
        if (root == null) return true;
        int left = getHight(root.left);
        int right = getHight(root.right);
        if (Math.abs(left - right) > 1) return false;
        else return isBalanced(root.left) && isBalanced(root.right);
    }

    private int getHight(TreeNode root) {
        if (root == null) return 0;
        return Math.max(getHight(root.left), getHight(root.right)) + 1;
    }
}
```

# 56. 数组中只出现一次的数字

[NowCoder](https://www.nowcoder.com/practice/e02fdb54d7524710a7d664d082bb7811?tpId=13&tqId=11193&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 一个整型数组里除了两个数字之外，其他的数字都出现了两次，找出这两个数。

**解题思路**

> 两个不相等的元素在位级表示上必定会有一位存在不同，将数组的所有元素异或得到的结果为不存在重复的两个元素异或的结果。
>
> sum &= -sum 得到出 sum 最右侧不为 0 的位，也就是不存在重复的两个元素在位级表示上最右侧不同的那一位，利用这一位就可以将两个元素区分开来。

```java
class Solution {
    public int[] singleNumbers(int[] nums) {
        int sum = nums[0];
        for (int i = 1; i < nums.length; i++) {
            sum ^= nums[i];
        }
        
        int first = 1;
        while ((sum & first) == 0) first <<= 1;
        
        int one = 0, two = 0;
        for (int i = 0; i < nums.length; i++) {
            /*
            if ((nums[i] & first) == 1) one ^= nums[i]; // 这里的判断是错误的
            else if ((nums[i] & first) == 0) two ^= nums[i];
            */
            if ((nums[i] & first) == 0) one ^= nums[i];
            else two ^= nums[i];
        }
        return new int[]{one, two};
    }
}

class Solution {
    public int[] singleNumbers(int[] nums) {
        int sum = 0;
       	for (int num: nums) {
            sum ^= num;
        }
        
        sum &= -sum;
        
        int one = 0, two = 0;
        for (int num: nums) {
            if ((num & sum) == 0) one ^= num;
            else two ^= num;
        }
        return new int[]{one, two};
    }
}
```

# 56.1 数组中数字出现的次数 II

[leetcode](https://leetcode-cn.com/problems/shu-zu-zhong-shu-zi-chu-xian-de-ci-shu-ii-lcof/)

**题目描述**：

> 在一个数组 `nums` 中除一个数字只出现一次之外，其他数字都出现了三次。请找出那个只出现一次的数字。

**示例 1：**

```
输入：nums = [3,4,3,3]
输出：4
```

**示例 2：**

```
输入：nums = [9,1,7,9,7,9,7]
输出：1
```

**解题思路**

```java
class Solution {
    public int singleNumber(int[] nums) {
        if (nums == null || nums.length == 0) throw new RuntimeException("无效输入数组！");
        int[] bitSum = new int[32]; // java int类型有32位，其中首位为符号位
        for (int num: nums) {
            int bitMask = 1;
            for (int i = 31; i >= 0; i--) { //bitSum[0]为符号位
                if ((num & bitMask) != 0) // 这里判断条件也可以写为(num&bitMask)==bitMask,而不是==1
                    bitSum[i]++;
                bitMask <<= 1; // 左移没有无符号、带符号的区别，都是在右侧补0
            }
        }
        int res = 0;
        for (int i = 0; i < 32; i++) { // 这种做法使得本算法同样适用于负数的情况
            res <<= 1; 
            res += bitSum[i] % 3; // 这两步顺序不能变，否则最后一步会多左移一次
        }
        return res;
    }
}
```

# 57.1 和为 S 的两个数字

[NowCoder](https://www.nowcoder.com/practice/390da4f7a00f44bea7c2f3d19491311b?tpId=13&tqId=11195&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 输入一个递增排序的数组和一个数字 S，在数组中查找两个数，使得他们的和正好是 S。如果有多对数字的和等于 S，输出两个数的乘积最小的。

**解题思路**

使用双指针，一个指针指向元素较小的值，一个指针指向元素较大的值。指向较小元素的指针从头向尾遍历，指向较大元素的指针从尾向头遍历。

- 如果两个指针指向元素的和 sum == target，那么得到要求的结果；
- 如果 sum > target，移动较大的元素，使 sum 变小一些；
- 如果 sum < target，移动较小的元素，使 sum 变大一些。

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        int i = 0, j = nums.length - 1;
        while(i < j) {
            int cur = nums[i] + nums[j];
            if (cur == target) return new int[]{nums[i], nums[j]};
            else if (cur < target) i++;
            else j--; 
        }
        return new int[]{};
    }
}
```

# 57.2 和为 S 的连续正数序列

[NowCoder](https://www.nowcoder.com/practice/c451a3fd84b64cb19485dad758a55ebe?tpId=13&tqId=11194&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 输出所有和为 S 的连续正数序列。
>
> 例如和为 100 的连续序列有：

```
[9, 10, 11, 12, 13, 14, 15, 16]
[18, 19, 20, 21, 22]。
```

**解题思路**：

```java
class Solution {
    public int[][] findContinuousSequence(int target) {
        if (target <= 0) return new int[][]{};
        int small = 1, big = 1;
        int sum = 1;
        List<int[]> res = new ArrayList<>();
        while(small <= target / 2) {
            if (sum < target) {
                big++;
                sum += big;
            } else if (sum > target) {
                sum -= small;
                small++;
            } else if (sum == target) {
                res.add(getRes(small, big));
                sum -= small;
                small++;
            }
        }
        return res.toArray(new int[res.size()][]);
    }
    public int[] getRes(int small, int big) {
        int[] ans = new int[big - small + 1];
        int k = 0;
        for (int i = small; i <= big; i++) {
            ans[k++] = i;
        }
        return ans;
    }
}
```

# 58.1 翻转单词顺序列

[NowCoder](https://www.nowcoder.com/practice/3194a4f4cf814f63919d0790578d51f3?tpId=13&tqId=11197&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

```java
Input:
"I am a student."

Output:
"student. a am I"
```

**解题思路**

**方法一：双指针**

- 倒序遍历字符串 `s` ，记录单词左右索引边界 `i` , `j` ；
- 每确定一个单词的边界，则将其添加至单词列表 `res`；
- 最终，将单词列表拼接为字符串，并返回即可。

```java
class Solution {
    public String reverseWords(String s) {
        s = s.trim(); // 删除首尾空格
        int j = s.length() - 1, i = j;
        StringBuilder res = new StringBuilder();
        while(i >= 0) {
            while(i >= 0 && s.charAt(i) != ' ') i--; // 搜索首个空格
            res.append(s.substring(i + 1, j + 1) + " "); // 添加单词
            while(i >= 0 && s.charAt(i) == ' ') i--; // 跳过单词间空格
            j = i; // j 指向下个单词的尾字符
        }
        return res.toString().trim(); // 转化为字符串并返回
    }
}
```

**方法二：分割 + 倒序**

> 利用 “字符串分割”、“列表倒序” 的内置函数 *（面试时不建议使用）* ，可简便地实现本题的字符串翻转要求。
>
> 以空格为分割符完成字符串分割后，**若两单词间有 `x > 1` 个空格，则在单词列表 `strs` 中，此两单词间会多出 `x - 1` 个 “空单词” （即 "" ）**。解决方法：倒序遍历单词列表，并将单词逐个添加至 StringBuilder ，遇到空单词时跳过。
>
>  ![Picture0.png](https://pic.leetcode-cn.com/9ef4a9ea565bf1c2d9209ca94881a77288f90f222476cfd44c418fa3f2d2d7c1-Picture0.png) 

```java
class Solution {
    public String reverseWords(String s) {
        String[] strs = s.trim().split(" "); // 去除首尾空格。 
        StringBuilder res = new StringBuilder();
        for (int i = strs.length-1; i >= 0; i--) { // 倒序
            if (strs[i].equals("")) continue; // 遇到空单词则跳过。注：如果两个单词中间有几个空格
            res.append(strs[i] + " ");
        }
        return res.toString().trim();
    }
}
```

**以下方法有个问题：当两个单词之间间隔多个空格时，反转后任然保留多个空格（将空格一起反转）；**

**以上方法会将多个空格一起反转。**

> 题目应该有一个隐含条件，就是不能用额外的空间。虽然 Java 的题目输入参数为 String 类型，需要先创建一个字符数组使得空间复杂度为 O(N)，但是正确的参数类型应该和原书一样，为字符数组，并且只能使用该字符数组的空间。任何使用了额外空间的解法在面试时都会大打折扣，包括递归解法。
>
> 正确的解法应该是和书上一样，**先旋转每个单词，再旋转整个字符串**。

```java
public String ReverseSentence(String str) {
    int n = str.length();
    char[] chars = str.toCharArray();
    int i = 0, j = 0;
    while (j <= n) {
        if (j == n || chars[j] == ' ') {
            reverse(chars, i, j - 1);
            i = j + 1;
        }
        j++;
    }
    reverse(chars, 0, n - 1);
    return new String(chars);
}

private void reverse(char[] c, int i, int j) {
    while (i < j)
        swap(c, i++, j--);
}

private void swap(char[] c, int i, int j) {
    char t = c[i];
    c[i] = c[j];
    c[j] = t;
}
```

# 58.2 左旋转字符串

[NowCoder](https://www.nowcoder.com/practice/12d959b108cb42b1ab72cef4d36af5ec?tpId=13&tqId=11196&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

```
Input:
S="abcXYZdef"
K=3

Output:
"XYZdefabc"
```

**解题思路**

**方法一：字符串切片**

> 应用字符串切片函数，可方便实现左旋转字符串。
>
> 获取字符串 s[n:]s[n:] 切片和 s[:n]s[:n] 切片，使用 "++" 运算符拼接并返回即可。

```java
class Solution {
    public String reverseLeftWords(String s, int n) {
        return s.substring(n, s.length()) + s.substring(0, n);
    }
}
```

**方法二：列表遍历拼接**

> 若面试规定不允许使用 切片函数 ，则使用此方法。
>
> 算法流程：
>
> 1. 新建一个`StringBuilder`，记为 `res` ；
> 2. 先向 `res` 添加 “第 `n + 1` 位至末位的字符” ；
> 3. 再向 `res` 添加 “首位至第 `n `位的字符” ；
> 4. 将 `res` 转化为字符串并返回。

```java
class Solution {
    public String reverseLeftWords(String s, int n) {
        StringBuilder res = new StringBuilder();
        for (int i = n; i < s.length(); i++) {
            res.append(s.charAt(i));
        }
        for (int i = 0; i < n; i++) {
            res.append(s.charAt(i));
        }
        return res.toString();
    }
}
```

**方法三：**

> 先将 "abc" 和 "XYZdef" **分别翻转**，得到 "cbafedZYX"，然后再把**整个字符串翻转**得到 "XYZdefabc"。

```java
public String LeftRotateString(String str, int n) {
    if (n >= str.length())
        return str;
    char[] chars = str.toCharArray();
    reverse(chars, 0, n - 1);
    reverse(chars, n, chars.length - 1);
    reverse(chars, 0, chars.length - 1);
    return new String(chars);
}

private void reverse(char[] chars, int i, int j) {
    while (i < j)
        swap(chars, i++, j--);
}

private void swap(char[] chars, int i, int j) {
    char t = chars[i];
    chars[i] = chars[j];
    chars[j] = t;
}
```

# 59. 滑动窗口的最大值

[NowCoder](https://www.nowcoder.com/practice/1624bc35a45c42c0bc17d17fa0cba788?tpId=13&tqId=11217&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 给定一个数组和滑动窗口的大小，找出所有滑动窗口里数值的最大值。
>
> 例如，如果输入数组 {2, 3, 4, 2, 6, 2, 5, 1} 及滑动窗口的大小 3，那么一共存在 6 个滑动窗口，他们的最大值分别为 {4, 4, 6, 6, 6, 5}。

**解题思路**

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if (nums == null || nums.length == 0 || k <= 0) return new int[]{};
        List<Integer> list = new ArrayList<>();
        int i = 0, j = k-1;
        while(j < nums.length) {
            int max = nums[i];
            while(i <= j) {
                if (max < nums[i]) max = nums[i];
                i++;
            }
            list.add(max);
            j++;
            i = j - k + 1;
        }
        //int[] res = list.stream().mapToInt(Integer::valueOf).toArray();
        // int[] res = list.stream().mapToInt(num->Integer.valueOf(num)).toArray();
        int[] res = list.stream().mapToInt(num->num.intValue()).toArray();
        return res;
    }
}
```

```java
public ArrayList<Integer> maxInWindows(int[] num, int size) {
    ArrayList<Integer> ret = new ArrayList<>();
    if (size > num.length || size < 1)
        return ret;
    PriorityQueue<Integer> heap = new PriorityQueue<>((o1, o2) -> o2 - o1); // 顶堆
    for (int i = 0; i < size; i++)
        heap.add(num[i]);
    ret.add(heap.peek());
    for (int i = 0, j = i + size; j < num.length; i++, j++) { // 维护一个大小为 size 的大顶堆
        heap.remove(num[i]);
        heap.add(num[j]);
        ret.add(heap.peek());
    }
    return ret;
}
```

回忆 **面试题30. 包含min函数的栈** ，其使用 **单调栈** 实现了随意入栈、出栈情况下的 `O(1)`时间获取 “栈内最小值” 。本题同理，不同点在于 “出栈操作” 删除的是 “列表尾部元素” ，而 “窗口滑动” 删除的是 “列表首部元素” 。

**窗口对应的数据结构为双端队列**，本题使用**单调队列**即可解决以上问题。遍历数组时，每轮保证单调队列deque:

1. deque 内 **仅包含窗口内的元素**⇒ 每轮窗口滑动移除了元素 `nums[i - 1]`，需将 deque 内的对应元素一起删除。
2. deque 内的元素 **非严格递减** ⇒ 每轮窗口滑动添加了元素 `nums[j + 1]` ，需将 deque 内所有`<nums[j+1]` 的元素删除。

**算法流程：**

1. **初始化：** 双端队列 deque，结果列表 res ，数组长度 `n`；
2. **滑动窗口：** 左边界范围`i∈[1−k,n+1−k] `，右边界范围`j∈[0,n−1]`；
   1. 若` i > 0` 且 队首元素 `deque[0] =` 被删除元素 `nums[i−1]` ：则队首元素出队；
   2. 删除 deque 内所有 `<nums[j]` 的元素，以保持 deque 递减；
   3. 将 `nums[j]` 添加至 deque 尾部；
   4. 若已形成窗口（即`i≥0` ）：将窗口最大值（即队首元素 `deque[0]` ）添加至列表 res 。

3. **返回值：** 返回结果列表 res 。

   [leetcode 解题思想](https://leetcode-cn.com/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/solution/mian-shi-ti-59-i-hua-dong-chuang-kou-de-zui-da-1-6/)

```java
class Solution {
    public int[] maxSlidingWindow(int[] nums, int k) {
        if(nums.length == 0 || k == 0) return new int[0];
        Deque<Integer> deque = new LinkedList<>();
        int[] res = new int[nums.length - k + 1];
        for(int i = 0; i < k; i++) { // 未形成窗口
            while(!deque.isEmpty() && deque.peekLast() < nums[i]) deque.removeLast();
            deque.addLast(nums[i]);
        }
        res[0] = deque.peekFirst();
        for(int i = k; i < nums.length; i++) { // 形成窗口后
            if(deque.peekFirst() == nums[i - k]) deque.removeFirst();
            while(!deque.isEmpty() && deque.peekLast() < nums[i]) deque.removeLast();
            deque.addLast(nums[i]);
            res[i - k + 1] = deque.peekFirst();
        }
        return res;
    }
}
```

# 59.2. 队列的最大值

[leetcode](https://leetcode-cn.com/problems/dui-lie-de-zui-da-zhi-lcof/)

**题目描述**：

> 请定义一个队列并实现函数 max_value 得到队列里的最大值，要求函数max_value、push_back 和 pop_front 的均摊时间复杂度都是O(1)。
>
> 若队列为空，pop_front 和 max_value 需要返回 -1

**解题思路**：

> 与上题思路一致
>
> [leetcode 解题思想](https://leetcode-cn.com/problems/hua-dong-chuang-kou-de-zui-da-zhi-lcof/solution/mian-shi-ti-59-i-hua-dong-chuang-kou-de-zui-da-1-6/)
>
> [leetcode 解题思想](https://leetcode-cn.com/problems/dui-lie-de-zui-da-zhi-lcof/solution/mian-shi-ti-59-ii-javashi-xian-yuan-li-he-mian-shi/)

```java
class MaxQueue {
    private Queue<Integer> dataQueue = null;
    private Deque<Integer> maxQueue = null;
    
    public MaxQueue() {
        dataQueue = new LinkedList<>();
        maxQueue = new LinkedList<>();
    }
    
    public int max_value() {
        if (maxQueue.isEmpty()) return -1;
        return maxQueue.getFirst();
    }
    
    public void push_back(int value) {
        dataQueue.offer(value);
        while (!maxQueue.isEmpty() && maxQueue.getLast() < value) {
            maxQueue.removeLast();
        }
        maxQueue.addLast(value);
    }
    
    public int pop_front() {
        if (dataQueue.isEmpty()) return -1;
        if (dataQueue.peek().equals(maxQueue.getFirst())) maxQueue.removeFirst();
        return dataQueue.poll();
    }
}
```

# 60. n 个骰子的点数-----????

[Lintcode](https://www.lintcode.com/en/problem/dices-sum/)

**题目描述**

> 把 n 个骰子扔在地上，求点数和为 s 的概率。
>
> [![img](https://camo.githubusercontent.com/3bb885713c5dbc427c8b5e914946161520ef608c/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f31393566383639332d356563342d343938372d383536302d6632356533363538373964642e706e67)](https://camo.githubusercontent.com/3bb885713c5dbc427c8b5e914946161520ef608c/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f31393566383639332d356563342d343938372d383536302d6632356533363538373964642e706e67)

**解题思路**:

```java

```

# 61. 扑克牌顺子

[NowCoder](https://www.nowcoder.com/practice/762836f4d43d43ca9deb273b3de8e1f4?tpId=13&tqId=11198&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 五张牌，其中大小鬼为癞子，牌面为 0。判断这五张牌是否能组成顺子。
>
> [![img](https://camo.githubusercontent.com/4d9edf6de55f20d05b71eaab6fc077bcc8d8302e/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f65616135303662362d303734372d346265652d383166382d3363646137393564383135342e706e67)](https://camo.githubusercontent.com/4d9edf6de55f20d05b71eaab6fc077bcc8d8302e/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f65616135303662362d303734372d346265652d383166382d3363646137393564383135342e706e67)

**解题思路**

```java
class Solution {
    public boolean isStraight(int[] nums) {
        if (nums == null || nums.length < 5) return false;
        int zeros = 0;
        int gap = 0;
        Arrays.sort(nums);
        for (int i = 0; i < nums.length-1; i++) {
            if (nums[i] == 0) zeros++;
            else if (nums[i+1] == nums[i]) return false;
            else gap += (nums[i+1] - nums[i] - 1);
        }
        // if (gap <= zeros) return true;
        // else return false;
        return gap <= zeros;
    }
}


class Solution {
    public boolean isStraight(int[] nums) {
        int joker = 0;
        Arrays.sort(nums); // 数组排序
        for(int i = 0; i < 4; i++) {
            if(nums[i] == 0) joker++; // 统计大小王数量
            else if(nums[i] == nums[i + 1]) return false; // 若有重复，提前返回 false
        }
        return nums[4] - nums[joker] < 5; // 最大牌 - 最小牌 < 5 则可构成顺子
    }
}
```

# 62. 圆圈中最后剩下的数

[NowCoder](https://www.nowcoder.com/practice/f78a359491e64a50bce2d89cff857eb6?tpId=13&tqId=11199&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 让小朋友们围成一个大圈。然后，随机指定一个数 m，让编号为 0 的小朋友开始报数。每次喊到 m-1 的那个小朋友要出列唱首歌，然后可以在礼品箱中任意的挑选礼物，并且不再回到圈中，从他的下一个小朋友开始，继续 0...m-1 报数 .... 这样下去 .... 直到剩下最后一个小朋友，可以不用表演。

**解题思路**

```java
class Solution {
    public int lastRemaining(int n, int m) {
        if (n < 1 || m < 1) return -1;
        if (n == 1) return 0;
        List<Integer> list = new ArrayList<>();
        for (int i = 0; i < n; i++) {
            list.add(i);
        }
        int index = 0;
        while(list.size() > 1) {
            // list.remove(index = (index + m - 1) % list.size());
            index = (index + m -1) % list.size();
            list.remove(index);
        }
        return list.get(0);
    }
}

// 约瑟夫环，圆圈长度为 n 的解可以看成长度为 n-1 的解再加上报数的长度 m。因为是圆圈，所以最后需要对 n 取余。
class Solution {
    public int lastRemaining(int n, int m) {
        if (n < 1 || m < 1) return -1;
        if (n == 1) return 0;
        return (lastRemaining(n-1, m) + m) % n;
    }
}
class Solution {
    public int lastRemaining(int n, int m) {
        if (n < 1 || m < 1) return -1;
        if (n == 1) return 0;
        int res = 0;
        for (int i = 2; i <= n; i++) {
            res = (res + m) % i;
        }
        return res;
    }
}
```

# 63. 股票的最大利润

[Leetcode](https://leetcode.com/problems/best-time-to-buy-and-sell-stock/description/)

**题目描述**

可以有一次买入和一次卖出，买入必须在前。求最大收益。

[![img](https://camo.githubusercontent.com/e145285aad29bca13fb365c965c4da86029c1c83/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f34323636313031332d373530662d343230622d623363312d3433376539613131666236352e706e67)](https://camo.githubusercontent.com/e145285aad29bca13fb365c965c4da86029c1c83/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f34323636313031332d373530662d343230622d623363312d3433376539613131666236352e706e67)

**解题思路**

**动态规划解析：**

- **状态定义**： 设动态规划列表 `dp` ，`dp[i]` 代表以 `prices[i]` 为结尾的子数组的最大利润（以下简称为 **前 i 日的最大利润** ）。

- **转移方程：** 由于题目限定 “买卖该股票一次” ，因此前` i` 日最大利润`dp[i]`等于前`i−1`日最大利润`dp[i-1]` 和第 `i` 日卖出的最大利润中的最大值。
  $$
  前i日最大利润 = max（前 (i-1) 日最大利润，第 i 日价格 - 前 i 日最低价格）
  $$

  $$
  dp[i] = max(dp[i−1],prices[i]−min(prices[0:i]))
  $$

- **初始状态：** `dp[0] = 0` ，即首日利润为 `0` ；

- **返回值：** `dp[n - 1]` ，其中 `n`为 `dp` 列表长度。

![1595736222997](D:\学习资料\笔记与课件\数据结构与算法\assets\1595736222997.png)

```java
class Solution {
    public int maxProfit(int[] prices) {
        if (prices == null || prices.length < 2) return 0;
        int min = prices[0];
        int res = 0;
        for (int i = 1; i < prices.length; i++) {
            if (min > prices[i]) {
                min = prices[i];
                continue;
            }
            if (res < (prices[i] - min)) res = prices[i] - min;
        }
        return res;
    }
}

class Solution {
    public int maxProfit(int[] prices) {
        int cost = Integer.MAX_VALUE, profit = 0;
        for(int price : prices) {
            cost = Math.min(cost, price);
            profit = Math.max(profit, price - cost);
        }
        return profit;
    }
}
```

# 64. 求 1+2+3+...+n

[NowCoder](https://www.nowcoder.com/practice/7a0da8fc483247ff8800059e12d7caf1?tpId=13&tqId=11200&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 要求不能使用乘除法、for、while、if、else、switch、case 等关键字及条件判断语句 A ? B : C。

**解题思路**

> 使用递归解法最重要的是指定返回条件，但是本题无法直接使用 if 语句来指定返回条件。
>
> 条件与 && 具有短路原则，即在第一个条件语句为 false 的情况下不会去执行第二个条件语句。利用这一特性，将递归的返回条件取非然后作为 && 的第一个条件语句，递归的主体转换为第二个条件语句，那么当递归的返回条件为 true 的情况下就不会执行递归的主体部分，递归返回。
>
> 本题的递归返回条件为 n <= 0，取非后就是 n > 0；递归的主体部分为 sum += Sum_Solution(n - 1)，转换为条件语句后就是 (sum += Sum_Solution(n - 1)) > 0。

```java
class Solution {
    public int sumNums(int n) {
        int sum = 0;
        boolean flag = (n >= 0) && (sum = sumNums(n-1) + n) > 0;
        return sum;
    }
}
```

# 65. 不用加减乘除做加法

[NowCoder](https://www.nowcoder.com/practice/59ac416b4b944300b617d4f7f111b215?tpId=13&tqId=11201&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 写一个函数，求两个整数之和，要求不得使用 +、-、*、/ 四则运算符号。

**解题思路**

> a ^ b 表示没有考虑进位的情况下两数的和，(a & b) << 1 就是进位。
>
> 递归会终止的原因是 (a & b) << 1 最右边会多一个 0，那么继续递归，进位最右边的 0 会慢慢增多，最后进位会变为 0，递归终止。
>
> ![1595737071735](D:\学习资料\笔记与课件\数据结构与算法\assets\1595737071735.png)

```java
public int Add(int a, int b) {
    return b == 0 ? a : Add(a ^ b, (a & b) << 1);
}
```

# 65.1 交换两个变量的值

![1595817846183](D:\学习资料\笔记与课件\数据结构与算法\assets\1595817846183.png)

# 66. 构建乘积数组

[NowCoder](https://www.nowcoder.com/practice/94a4d381a68b47b7a8bed86f2975db46?tpId=13&tqId=11204&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

> 给定一个数组 A[0, 1,..., n-1]，请构建一个数组 B[0, 1,..., n-1]，其中 B 中的元素 B[i]=A[0]*A[1]*...*A[i-1]*A[i+1]*...*A[n-1]。要求不能使用除法。
>
> [![img](https://camo.githubusercontent.com/81de58ddca273956701ebf0d90cd702d3159279c/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f34323430613639662d346435312d346431362d623739372d3264666531313066333062642e706e67)](https://camo.githubusercontent.com/81de58ddca273956701ebf0d90cd702d3159279c/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f34323430613639662d346435312d346431362d623739372d3264666531313066333062642e706e67)

**解题思路**

```java
class Solution {
    public int[] constructArr(int[] a) {
        if (a == null || a.length <= 1) return new int[0];
        int[] res = new int[a.length];
        int sum = 1;
        res[0] = 1;
        res[a.length-1] = 1;
        for (int i = 0; i < a.length-1; i++) {
            sum *= a[i];
            res[i+1] = sum;
        }
        sum = 1;
        for (int i = a.length - 1; i > 0; i--) {
            sum *= a[i];
            res[i-1] *= sum;
        }
        return res;
    }
}

class Solution {
    public int[] constructArr(int[] a) {
        if (Objects.isNull(a) || a.length == 0) {
            return new int[]{};
        }
        int[] res = new int[a.length];
        int left = 1;
        for (int i = 0; i < res.length; i++) {
            res[i] =left;
            left *= a[i];
        }
        int right = 1;
        for (int i = res.length - 1; i >= 0; i--) {
            res[i] *= right;
            right *= a[i];
        }
        return res;
    }
}
```

# 67. 把字符串转换成整数

**题目链接**

[NowCoder](https://www.nowcoder.com/practice/1277c681251b4372bdef344468e4f26e?tpId=13&tqId=11202&tPage=1&rp=1&ru=/ta/coding-interviews&qru=/ta/coding-interviews/question-ranking&from=cyc_github)

**题目描述**

将一个字符串转换成一个整数，字符串不是一个合法的数值则返回 0，要求不能使用字符串转换整数的库函数。

```java
Iuput:
+2147483647
1a33

Output:
2147483647
0
```

**解题思路**

根据题意，有以下四种字符需要考虑：

1. **首部空格：** 删除之即可；

2. **符号位：** 三种情况，即 ''`+`'' , ''`−`'' , ''`无符号`" ；新建一个变量保存符号位，返回前判断正负即可。

3. **非数字字符：** 遇到首个非数字的字符时，应立即返回。

4. **数字字符**：

   - **字符转数字：** “此数字的 ASCII 码” 与 “ `0` 的 ASCII 码” 相减即可；

   - **数字拼接：** 若从左向右遍历数字，设当前位字符为 `c` ，当前位数字为 `x `，数字结果为 `res` ，则数字拼接公式为：
     $$
     res=10×res+x
     $$

     $$
     x=ascii(c)−ascii(′0′)
     $$

![1595903208302](D:\学习资料\笔记与课件\数据结构与算法\assets\1595903208302.png)

**数字越界处理：**

> 题目要求返回的数值范围应在[−2^31 ,2^(31−1)] ，因此需要考虑数字越界问题。而由于题目指出 环境只能存储 `32` 位大小的有符号整数 ，因此判断数字越界时，要始终保持 `res` 在 int 类型的取值范围内。
>

在每轮数字拼接前，判断 `res` 在**此轮拼接后是否超过**` 2147483647` ，若超过则加上符号位直接返回。
设数字拼接边界 `bndry = 2147483647 // 10 = 214748364` ，则以下两种情况越界：

![1595903183940](D:\学习资料\笔记与课件\数据结构与算法\assets\1595903183940.png)

```java
class Solution {
    public int strToInt(String str) {
        if (str == null || str.length() == 0) return 0;
        char[] chars = str.trim().toCharArray(); // 去除前后空格
        if (chars.length == 0) return 0;
        int res = 0, bndry = Integer.MAX_VALUE / 10;
        int sign = 0; // 符号位表示：1:正; -1:负; 0:无符号
        int i = 1;
        if (chars[0] == '-') sign = -1;
        else if (chars[0] == '+') sign = 1;
        else i = 0;
        for (int j = i; j < chars.length; j++) {
            if (chars[j] > '9' || chars[j] < '0') break;
            int x = chars[j] - '0';
            if (res > bndry || (res == bndry && x > 7)) 
                return sign==-1? Integer.MIN_VALUE: Integer.MAX_VALUE;
            else res = res * 10 + x; // 不越界
        }
        return sign == -1? res * -1 : res;
    }
}
```

# 68.1.  二叉搜索树中两个节点的最低公共祖先

[leetcode](https://leetcode-cn.com/problems/er-cha-sou-suo-shu-de-zui-jin-gong-gong-zu-xian-lcof/)

**题目描述**：

>  给定一个二叉搜索树, 找到该树中两个指定节点的最近公共祖先。 
>
> 最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”
>

**解题思路**：

> 二叉查找树中，两个节点 p, q 的公共祖先 root 满足 root.val >= p.val && root.val <= q.val。
>
> [![img](https://camo.githubusercontent.com/34ba820a4a6183ae7be254594483e74a5991ac38/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f30343766616163342d613336382d343536352d383333312d3262363632353330383064332e6a7067)](https://camo.githubusercontent.com/34ba820a4a6183ae7be254594483e74a5991ac38/68747470733a2f2f63732d6e6f7465732d313235363130393739362e636f732e61702d6775616e677a686f752e6d7971636c6f75642e636f6d2f30343766616163342d613336382d343536352d383333312d3262363632353330383064332e6a7067)

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null) return null;
        if (root.val > p.val && root.val > q.val) {
            return lowestCommonAncestor(root.left, p, q);
        } else if (root.val < p.val && root.val < q.val) {
            return lowestCommonAncestor(root.right, p, q);
        }
        return root;
    }
}
```

# 68.2.  二叉树中两个节点的最低公共祖先

[leetcode]( https://leetcode-cn.com/problems/er-cha-shu-de-zui-jin-gong-gong-zu-xian-lcof/ )

**题目描述**：

>  给定一个二叉树, 找到该树中两个指定节点的最近公共祖先。 
>
> 最近公共祖先的定义为：“对于有根树 T 的两个结点 p、q，最近公共祖先表示为一个结点 x，满足 x 是 p、q 的祖先且 x 的深度尽可能大（一个节点也可以是它自己的祖先）。”

**解题思路**

若 root 是 p, q 的 最近公共祖先 ，则只可能为以下情况之一：

- p 和 q 在 root 的子树中，且分列 root 的 异侧（即分别在左、右子树中）；
- p = root，且 q 在 root 的左或右子树中；
- q = root，且 p 在 root 的左或右子树中；

考虑通过递归对二叉树进行后序遍历，当遇到节点 p 或 q 时返回。从底至顶回溯，当节点 p, q 在节点 root 的异侧时，节点 root 即为最近公共祖先，则向上返回 root。

**递归解析：**

1. 终止条件：

   - 当越过叶节点，则直接返回 null ；

   - 当 root 等于 p,q ，则直接返回 root ；

2. 递推工作：

   - 开启递归左子节点，返回值记为 left ；

   - 开启递归右子节点，返回值记为 right ；

3. 返回值： 根据 left 和 right ，可展开为四种情况；

   - 当 left 和 right 同时为空 ：说明 root 的左 / 右子树中都不包含 p,q ，返回 null ；

   - 当 left 和 right 同时不为空 ：说明 p,q 分列在 root 的 异侧 （分别在 左 / 右子树），因此root为最近公共祖先，返回root ；

   - 当 left 为空 ，right 不为空 ：p,q 都不在 root 的左子树中，直接返回 right 。具体可分为两种情况：

     - p,q 其中一个在 root 的 右子树 中，此时 right 指向 p（假设为 p ）；

     - p,q 两节点都在 root 的 右子树 中，此时的 right 指向 最近公共祖先节点 ；

   - 当 left 不为空 ， right 为空 ：与情况 3. 同理；

>  观察发现， 情况 `1.` 可合并至 `3.` 和 `4.` 内，详见文章末尾代码。 

```java
class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if (root == null || root == p || root == q)
            return root;
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        if (left == null && right == null) return null; // 1
        else if (left != null) return left; // 4
        else if (right != null) return right; // 3
        else return root; // 2 if(left != null and right != null)
    }
}


class Solution {
    public TreeNode lowestCommonAncestor(TreeNode root, TreeNode p, TreeNode q) {
        if(root == null || root == p || root == q) return root;
        TreeNode left = lowestCommonAncestor(root.left, p, q);
        TreeNode right = lowestCommonAncestor(root.right, p, q);
        if(left == null) return right;
        if(right == null) return left;
        return root;
    }
}
```

