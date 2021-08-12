## 双指针

#### [1. 两数之和](https://leetcode-cn.com/problems/two-sum/)

难度简单

给定一个整数数组 `nums` 和一个目标值 `target`，请你在该数组中找出和为目标值的那 **两个** 整数，并返回他们的数组下标。

你可以假设每种输入只会对应一个答案。但是，数组中同一个元素不能使用两遍。

**示例:**

```
给定 nums = [2, 7, 11, 15], target = 9

因为 nums[0] + nums[1] = 2 + 7 = 9
所以返回 [0, 1]
```

```java
class Solution {
    public int[] twoSum(int[] nums, int target) {
        if (nums == null || nums.length <= 1) return new int[0];
        Map<Integer, Integer> map = new HashMap<>();
        for (int i = 0; i < nums.length; i++) {
            int num = target - nums[i];
            if (map.containsKey(num)) 
                return new int[] {map.get(num), i};
            map.put(nums[i], i);
        }
        return new int[0];
    }
}
```



#### [2. 两数相加](https://leetcode-cn.com/problems/add-two-numbers/)

难度中等

给出两个 **非空** 的链表用来表示两个非负的整数。其中，它们各自的位数是按照 **逆序** 的方式存储的，并且它们的每个节点只能存储 **一位** 数字。

如果，我们将这两个数相加起来，则会返回一个新的链表来表示它们的和。

您可以假设除了数字 0 之外，这两个数都不会以 0 开头。

**示例：**

```
输入：(2 -> 4 -> 3) + (5 -> 6 -> 4)
输出：7 -> 0 -> 8
原因：342 + 465 = 807
```

**思想：**

>我们首先从最低有效位也就是列表 l1 和 l2 的表头开始相加。由于每位数字都应当处于 0…9 的范围内，我们计算两个数字的和时可能会出现 “溢出”。例如，5 + 7 = 12。在这种情况下，我们会将当前位的数值设置为2，并将进位 carry = 1带入下一次迭代。进位 carry 必定是 0 或 1，这是因为两个数字相加（考虑到进位）可能出现的最大和为 9 + 9 + 1 = 19。
>
>1. 将当前结点初始化为返回列表的哑结点。
>2. 将进位 carry 初始化为 0。
>3. 将 p 和 q 分别初始化为列表 l1 和 l2 的头部。
>4. 遍历列表 l1 和 l2 直至到达它们的尾端：
>   - 将 x 设为结点 p 的值。如果 p 已经到达 l1 的末尾，则将其值设置为 0。
>   - 将 y 设为结点 q 的值。如果 q 已经到达 l2 的末尾，则将其值设置为 0。
>   - 设定 sum = x + y + carry。
>   - 更新进位的值，carry = sum / 10。
>   - 创建一个数值为 (sum mod 10) 的新结点，并将其设置为当前结点的下一个结点，然后将当前结点前进到下一个结点。
>   - 同时，将 p 和 q 前进到下一个结点。
>5. 检查 carry = 1 是否成立，如果成立，则向返回列表追加一个含有数字 1 的新结点。
>6. 返回哑结点的下一个结点。

```java
class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode dummyHead = new ListNode(0);
        ListNode p = l1, q = l2, curr = dummyHead;
        int carry = 0;
        while (p != null || q != null) {
            int x = (p != null) ? p.val : 0;
            int y = (q != null) ? q.val : 0;
            int sum = carry + x + y;
            carry = sum / 10;
            curr.next = new ListNode(sum % 10);
            curr = curr.next;
            if (p != null) p = p.next;
            if (q != null) q = q.next;
        }
        if (carry > 0) {
            curr.next = new ListNode(carry);
        }
        return dummyHead.next;
    }
}

class Solution {
    public ListNode addTwoNumbers(ListNode l1, ListNode l2) {
        ListNode dummyHead = new ListNode(-1);
        ListNode cur = dummyHead;
        int carry = 0, l1val = 0, l2val = 0;
        while (l1 != null || l2 != null) {
            if (l1 == null) {
                l1val = 0;
                l2val = l2.val;
                l2 = l2.next;
            } else if (l2 == null) {
                l1val = l1.val;
                l2val = 0;
                l1 = l1.next;
            } else {
                l1val = l1.val;
                l2val = l2.val;
                l1 = l1.next;
                l2 = l2.next;
            }
            int sum = l1val + l2val + carry;
            carry = sum / 10;
            cur.next = new ListNode(sum % 10);
            cur = cur.next;
        }
        if (carry > 1) cur.next = new ListNode(carry);
        return dummyHead.next;
    }
}
```

#### [15. 三数之和](https://leetcode-cn.com/problems/3sum/)

难度中等

给你一个包含 *n* 个整数的数组 `nums`，判断 `nums` 中是否存在三个元素 *a，b，c ，*使得 *a + b + c =* 0 ？请你找出所有满足条件且不重复的三元组。

**注意：**答案中不可以包含重复的三元组。

**示例：**

```
给定数组 nums = [-1, 0, 1, 2, -1, -4]，

满足要求的三元组集合为：
[
  [-1, 0, 1],
  [-1, -1, 2]
]
```

**思想：**

>**可以先固定一个值，然后寻找后两个值时可采取==双指针==的方法**，将总的时间复杂度优化到 O(n^2)。实现的过程中，要注意优化以及**去重**。
>
>假设数组的长度为 n，我们先枚举 a，它在数组中的位置为 i；为了防止重复枚举，我们在位置 [i+1, n) 的范围内枚举 b 和 c
>
>1. 首先，我们对原数组继续**排序**，这样可以将重复的元素聚集到一起，便于去重；
>2. 开始遍历数组（固定三元组中第一个值），注意这里要**去重**：
>   - 如果当前元素比 0 大，则数组后面元素都比 0 大，则三数相加不会等于 0；
>   - 如果当前元素与前一元素相同，则直接跳到下一元素；
>   - 当固定好第一个值nums[i]后，开始用**双指针**方法：left == i + 1; right = nums.length - 1; **逐个进行判断，并注意去重。** 

```java
class Solution {
    public List<List<Integer>> threeSum(int[] nums) {// 总时间复杂度：O(n^2)
        List<List<Integer>> ans = new ArrayList<>();
        if (nums == null || nums.length <= 2) return ans;

        Arrays.sort(nums); // O(nlogn)

        for (int i = 0; i < nums.length - 2; i++) { // O(n^2)
            if (nums[i] > 0) break; // 第一个数大于 0，后面的数都比它大，肯定不成立了
            if (i > 0 && nums[i] == nums[i - 1]) continue; // 去掉重复情况
            int target = -nums[i];
            int left = i + 1, right = nums.length - 1;
            while (left < right) {
                if (nums[left] + nums[right] == target) {
                    ans.add(new ArrayList<>(Arrays.asList(nums[i], nums[left], nums[right])));
                    
                    // 现在要增加 left，减小 right，但是不能重复，比如: [-2, -1, -1, -1, 3, 3, 3], i = 0, left = 1, right = 6, [-2, -1, 3] 的答案加入后，需要排除重复的 -1 和 3
                    left++; right--; // 首先无论如何先要进行加减操作
                    while (left < right && nums[left] == nums[left - 1]) left++;
                    while (left < right && nums[right] == nums[right + 1]) right--;
                } else if (nums[left] + nums[right] < target) {
                    left++;
                } else {  // nums[left] + nums[right] > target
                    right--;
                }
            }
        }
        return ans;
    }
}
```

#### [16. 最接近的三数之和](https://leetcode-cn.com/problems/3sum-closest/)

难度中等

给定一个包括 *n* 个整数的数组 `nums` 和 一个目标值 `target`。找出 `nums` 中的三个整数，使得它们的和与 `target` 最接近。返回这三个数的和。假定每组输入只存在唯一答案。

**示例：**

```
输入：nums = [-1,2,1,-4], target = 1
输出：2
解释：与 target 最接近的和是 2 (-1 + 2 + 1 = 2) 。
```

**提示：**

- `3 <= nums.length <= 10^3`
- `-10^3 <= nums[i] <= 10^3`
- `-10^4 <= target <= 10^4`

**思想：**

> 假设数组的长度为 n，我们先枚举 a，它在数组中的位置为 i；
>
> 为了防止重复枚举，我们在位置 [i+1, n) 的范围内枚举 b 和 c。
>
> 与[15. 三数之和](https://leetcode-cn.com/problems/3sum/)思想一样。

```java
class Solution {
    public int threeSumClosest(int[] nums, int target) {
        if (nums == null || nums.length < 3) return 0;
        Arrays.sort(nums);
        int min = Integer.MAX_VALUE, res = 0;
        for (int i = 0; i < nums.length - 2; i++) {
            if (i > 0 && nums[i] == nums[i-1]) continue;
            int left = i + 1, right = nums.length - 1;
            while (left < right) {
                int sum = nums[i] + nums[left] + nums[right];
                if (sum == target) return target;
                
                if (Math.abs(sum - target) < min) {
                        min = Math.abs(sum - target);
                        res = sum;
                }

                if (sum > target) {
                    right--;
                    while (left < right && nums[right] == nums[right+1]) right--;
                } else if (sum < target) {
                    left++;
                    while (left < right && nums[left] == nums[left-1]) left++;
                }
            }
        }
        return res;
    }
}
```

#### [18. 四数之和](https://leetcode-cn.com/problems/4sum/)

难度中等545收藏分享切换为英文关注反馈

给定一个包含 *n* 个整数的数组 `nums` 和一个目标值 `target`，判断 `nums` 中是否存在四个元素 *a，**b，c* 和 *d* ，使得 *a* + *b* + *c* + *d* 的值与 `target` 相等？找出所有满足条件且不重复的四元组。

**注意：**

答案中不可以包含重复的四元组。

**示例：**

```
给定数组 nums = [1, 0, -1, 0, -2, 2]，和 target = 0。

满足要求的四元组集合为：
[
  [-1,  0, 0, 1],
  [-2, -1, 1, 2],
  [-2,  0, 0, 2]
]
```

**思想：**和15题思想一样，先固定一个数，时间复杂度为O(n^3)。排序去重

```java
class Solution {
    public List<List<Integer>> fourSum(int[] nums, int target) {
        List<List<Integer>> res = new ArrayList<>();
        if (nums == null || nums.length < 4) return res;
        Arrays.sort(nums);
        
        for (int i = 0; i < nums.length-3; i++) {
            if (nums[i]+nums[i+1]+nums[i+2]+nums[i+3] > target) break;
            if (i > 0 && nums[i] == nums[i-1]) continue;
            int sum3 = target - nums[i];

            for (int j = i+1; j < nums.length-2; j++) {
                if (nums[j]+nums[j+1]+nums[j+2] > sum3) break;
                if (j > i+1 && nums[j] == nums[j-1]) continue; // 去重
                int sum = sum3 - nums[j];

                int left = j+1, right = nums.length-1;
                while (left < right) {
                    if (nums[left] + nums[right] == sum) {
                        res.add(new ArrayList<>(Arrays.asList(nums[i], nums[j], nums[left], nums[right])));
                        left++; right--;
                        while (left < right && nums[left] == nums[left-1]) left++;
                        while (left < right && nums[right] == nums[right+1]) right--;
                    } else if (nums[left] + nums[right] > sum) {
                        right--;
                    } else left++;
                }
            }
        }
        return res;
    }
}
```

#### [11. 盛最多水的容器](https://leetcode-cn.com/problems/container-with-most-water/)

难度中等

给你 *n* 个非负整数 `a1，a2，...，an`，每个数代表坐标中的一个点` (i, ai) `。在坐标内画 `n` 条垂直线，垂直线 `i`的两个端点分别为 `(i, ai)` 和 `(i, 0)`。找出其中的两条线，使得它们与 `x` 轴共同构成的容器可以容纳最多的水。

**说明：**你不能倾斜容器，且 `n` 的值至少为 2。

![img](https://aliyun-lc-upload.oss-cn-hangzhou.aliyuncs.com/aliyun-lc-upload/uploads/2018/07/25/question_11.jpg)

图中垂直线代表输入数组 [1,8,6,2,5,4,8,3,7]。在此情况下，容器能够容纳水（表示为蓝色部分）的最大值为 49。

**示例：**

```
输入：[1,8,6,2,5,4,8,3,7]
输出：49
```

**思想：**

双指针代表的是**可以作为容器边界的所有位置的范围**。在一开始，双指针指向数组的左右边界，表示 **数组中所有的位置都可以作为容器的边界**，因为我们还没有进行过任何尝试。在这之后，我们每次将 **对应的数字较小的那个指针** 往 **另一个指针** 的方向移动一个位置，就表示我们认为 **这个指针不可能再作为容器的边界了**。

**证明：**

假设当前左指针和右指针指向的数分别为 `x` 和 `y`，不失一般性，我们假设 `x ≤ y`。同时，两个指针之间的距离为 `t`。那么，它们组成的容器的容量为：
$$
min(x,y)∗t=x∗t
$$
 我们可以断定，**如果我们保持左指针的位置不变，那么无论右指针在哪里，这个容器的容量都不会超过x * t了** 。

```java
public class Solution {
    public int maxArea(int[] height) {
        int l = 0, r = height.length - 1;
        int ans = 0;
        while (l < r) {
            int area = Math.min(height[l], height[r]) * (r - l);
            ans = Math.max(ans, area);
            if (height[l] <= height[r]) {
                ++l;
            }
            else {
                --r;
            }
        }
        return ans;
    }
}
```



#### [215. 数组中的第K个最大元素](https://leetcode-cn.com/problems/kth-largest-element-in-an-array/)-8.24

难度中等

在未排序的数组中找到第 **k** 个最大的元素。请注意，你需要找的是数组排序后的第 k 个最大的元素，而不是第 k 个不同的元素。

**示例 1:**

```
输入: [3,2,1,5,6,4] 和 k = 2
输出: 5
```

**示例 2:**

```
输入: [3,2,3,1,2,4,5,5,6] 和 k = 4
输出: 4
```

**说明:**

你可以假设 k 总是有效的，且 1 ≤ k ≤ 数组的长度。

**思想：**

> 在快排的 partition 操作中，每次 partition 操作结束都会返回⼀个点，这个标定点的下标和最终排序之后有序数组中这个元素所在的下标是⼀致的。利⽤这个特性，我们可以不断的划分数组区间，最终找到第 K ⼤的元素。执⾏⼀次 partition 操作以后，如果这个元素的下标⽐ K ⼩，那么接着就在后边的区间继续执⾏ partition 操作；如果这个元素的下标⽐ K ⼤，那么就在左边的区间继续执⾏ partition 操作；如果相等就直接输出这个下标对应的数组元素即可。

```java
// 方法一：排序，排序的⽅法反⽽速度是最快的
class Solution {
    public int findKthLargest(int[] nums, int k) {
        Arrays.sort(nums);
        return nums[nums.length - k];
    }
}

// 方法二：这个⽅法的理论依据是 partition 得到的点的下标就是最终排序之后的下标，根据这个下标，我们可以判断第 K ⼤的数在哪⾥
public class Solution {

    public int findKthLargest(int[] nums, int k) {
        if (nums.length == 0) return 0;
        return selection(nums, 0, nums.length-1, nums.length-k);
    }

    public int selection(int[] nums, int left, int right, int k) {
        if (left == right) return nums[left];
        int p = partition(nums, left, right);
        if (p == k) return nums[p];
        else if (k < p) return selection(nums, left, p-1, k);
        else return selection(nums, p+1, right, k);
    }

    /**
     * 在数组 nums 的子区间 [left, right] 执行 partition 操作，返回 nums[left] 排序以后应该在的位置
     * 在遍历过程中保持循环不变量的语义
     * 1、[left + 1, j] < nums[left]
     * 2、(j, i] >= nums[left]
     *
     * @param nums
     * @param left
     * @param right
     * @return
     */
    public int partition(int[] nums, int left, int right) {
        int pivot = nums[left];
        int j = left;
        for (int i = left + 1; i <= right; i++) {
            if (nums[i] < pivot) {
                // 小于 pivot 的元素都被交换到前面
                j++;
                swap(nums, j, i); 
            }
        }
        // 在之前遍历的过程中，满足 [left + 1, j] < pivot，并且 (j, i] >= pivot
        swap(nums, j, left);
        // 交换以后 [left, j - 1] < pivot, nums[j] = pivot, [j + 1, right] >= pivot
        return j;
    }

    private void swap(int[] nums, int index1, int index2) {
        int temp = nums[index1];
        nums[index1] = nums[index2];
        nums[index2] = temp;
    }
}
```



## 排序

#### [剑指 Offer 45. 把数组排成最小的数](https://leetcode-cn.com/problems/ba-shu-zu-pai-cheng-zui-xiao-de-shu-lcof/)

难度中等

输入一个非负整数数组，把数组里所有数字拼接起来排成一个数，打印能拼接出的所有数字中最小的一个。

**示例 1:**

```
输入: [10,2]
输出: "102"
```

**示例 2:**

```
输入: [3,30,34,5,9]
输出: "3033459"
```

**提示:**

- `0 < nums.length <= 100`

**说明:**

- 输出结果可能非常大，所以你需要返回一个字符串而不是整数
- 拼接起来的数字可能会有前导 0，最后结果不需要去掉前导 0

**思想：**

> 此题求拼接起来的 “最小数字” ，本质上是一个排序问题。
>
> **排序判断规则：** 设 `nums` 任意两数字的字符串格式 `x` 和 `y` ，则
>
> - 若拼接字符串 `x + y > y + x`，则 `x > y` ；
> - 反之，若 `x + y < y + x`，则 `x < y` ；
>
> 根据以上规则，套用任何排序方法对 `nums` 执行排序即可。
>
> **算法流程：**
>
> - **初始化：** 将数字数组 `nums` 转换成字符串列表 `strs`，保存各数字的字符串格式；
> - **列表排序：** 应用以上 “排序判断规则” ，对 `strs` 执行排序；
> - **返回值：** 拼接 `strs`中的所有字符串，并返回。

```java
// 方法一：使用内置排序
class Solution {
    public String minNumber(int[] nums) {
        if (nums == null || nums.length == 0) return "";
        // 1. 初始化
        String[] strs = new String[nums.length];
        for (int i = 0; i < nums.length; i++) {
            strs[i] = nums[i] + "";
            // strs[i] = String.valueOf(nums[i]);
        }
        // 2. 列表排序
        Arrays.sort(strs, (str1, str2) -> (str1 + str2).compareTo(str2 + str1));
        // 3. 返回值
        StringBuilder sb = new StringBuilder();
        for (String str: strs) {
            sb.append(str);
        }
        return sb.toString();
    }
}

// 方法二：使用快速排序
class Solution {
    public String minNumber(int[] nums) {
        if (nums == null || nums.length == 0) return "";
        // 1. 初始化
        String[] strs = new String[nums.length];
        for (int i = 0; i < nums.length; i++) {
            strs[i] = nums[i] + "";
            // strs[i] = String.valueOf(nums[i]);
        }
        // 2. 列表排序
        //Arrays.sort(strs, (str1, str2) -> (str1 + str2).compareTo(str2 + str1));
        fastSort(strs, 0, strs.length);
        // 3. 返回值
        StringBuilder sb = new StringBuilder();
        for (String str: strs) {
            sb.append(str);
        }
        return sb.toString();
    }
    /*
    public void fastSort(String[] strs, int left, int right) {
        if (left >= right) return;
        int l = left, r = right;
        while (l < r) { // 排序方式：strs[l] ~ strs[left] ~ strs[r]
            while ((strs[r] + strs[left]).compareTo(strs[left] + strs[r]) >= 0 && l<r) {
                r--;
            }
            while ((strs[l] + strs[left].compareTo(strs[left] + strs[l])) <= 0 && l<r) {
                l++;
            }
            String temp = strs[left];
            Strs[l] = strs[left];
            strs[left] = temp;
            fastSort(strs, left, l-1);
            fastSort(strs, l+1, right);
        }
    }
    */
}

```



## 深度优先搜索与回溯法

#### [17. 电话号码的字母组合](https://leetcode-cn.com/problems/letter-combinations-of-a-phone-number/)

难度中等834收藏分享切换为英文关注反馈

给定一个仅包含数字 `2-9` 的字符串，返回所有它能表示的字母组合。

给出数字到字母的映射如下（与电话按键相同）。注意 1 不对应任何字母。

![img](https://assets.leetcode-cn.com/aliyun-lc-upload/original_images/17_telephone_keypad.png)

**示例:**

```
输入："23"
输出：["ad", "ae", "af", "bd", "be", "bf", "cd", "ce", "cf"].
```

**说明:**
尽管上面的答案是按字典序排列的，但是你可以任意选择答案输出的顺序。

**思想：**DFS 递归深搜即可

```java
class Solution {
    List<String> res = new ArrayList<>();
    StringBuilder str = new StringBuilder();
    String[] letterMap = new String[] {
        " ", //0
        "", //1
        "abc", //2
        "def", //3
        "ghi", //4
        "jkl", //5
        "mno", //6
        "pqrs", //7
        "tuv", //8
        "wxyz", //9
    };

    public List<String> letterCombinations(String digits) {
        if (digits == null || digits.length() == 0) return res;
        char[] chars = digits.toCharArray();
        dfs(chars, 0);
        return res;
    }

    public void dfs(char[] chars, int k) {
        if (k == chars.length) {
            res.add(str.toString());
            return;
        }

        for (int i = 0; i < letterMap[chars[k]-'0'].length(); i++) {
            Character c = letterMap[chars[k]-'0'].charAt(i);
            str.append(c);
            dfs(chars, k+1);
            str.deleteCharAt(str.length() - 1);
        }
    }
}
```



#### [39. 组合总和](https://leetcode-cn.com/problems/combination-sum/)

难度中等821

给定一个**无重复元素**的数组 `candidates` 和一个目标数 `target` ，找出 `candidates` 中所有可以使数字和为 `target` 的组合。

`candidates` 中的数字可以无限制重复被选取。

**说明：**

- 所有数字（包括 `target`）都是正整数。
- 解集不能包含重复的组合。 

**示例 1：**

```
输入：candidates = [2,3,6,7], target = 7,
所求解集为：
[
  [7],
  [2,2,3]
]
```

**示例 2：**

```
输入：candidates = [2,3,5], target = 8,
所求解集为：
[
  [2,2,2,2],
  [2,3,3],
  [3,5]
]
```

 **思想：**

> 题目要求出总和为 target 的所有组合，组合需要去重。 
>
> 这⼀题和第 47 题类似，只不过元素可以反复使用
>
> **注意点：**因为这里的元素是可以反复使用的，所以和47题不同的是，这里不能使用marked来标记是否使用过，但又因为组合不能重复，所以dfs时，不能重新遍历之前已经遍历过的元素（当前元素可以），所以传入一个**索引**来作为标记。

```java
class Solution {
    public List<List<Integer>> res = new ArrayList<>();
    public List<Integer> ans = new ArrayList<>();

    public List<List<Integer>> combinationSum(int[] candidates, int target) {
        if (candidates == null || candidates.length == 0) return res;
        // Arrays.sort(candidates); // 排序
        dfs(candidates, target, 0);
        return res;
    }

    public void dfs(int[] candidates, int target, int index) {
        if (target == 0) {
            res.add(new ArrayList<>(ans));
            return;
        }

        for (int i = index; i < candidates.length; i++) {
            if (target - candidates[i] < 0) continue; // 如果上面没排序。这里不能是break
            ans.add(candidates[i]);
            dfs(candidates, target - candidates[i], i);
            ans.remove(ans.size()-1);
        }
    }
}
```

#### [40. 组合总和 II](https://leetcode-cn.com/problems/combination-sum-ii/)

难度中等

给定一个数组 `candidates` 和一个目标数 `target` ，找出 `candidates` 中所有可以使数字和为 `target` 的组合。

`candidates` 中的每个数字在每个组合中只能使用一次。

**说明：**

- 所有数字（包括目标数）都是正整数。
- 解集不能包含重复的组合。 

**示例 1:**

```
输入: candidates = [10,1,2,7,6,1,5], target = 8,
所求解集为:
[
  [1, 7],
  [1, 2, 5],
  [2, 6],
  [1, 1, 6]
]
```

**示例 2:**

```
输入: candidates = [2,5,2,1,2], target = 5,
所求解集为:
[
  [1,2,2],
  [5]
]
```

**思想：**组合和排列不一样，一般用**索引和排序**来去重

```java
class Solution {
    public List<List<Integer>> res = new ArrayList<>();
    public List<Integer> ans = new ArrayList<>();

    public List<List<Integer>> combinationSum2(int[] candidates, int target) {
        if (candidates == null || candidates.length == 0) return res;
        boolean[] marked = new boolean[candidates.length];
        Arrays.sort(candidates); // 这⾥是去重的关键逻辑
        dfs(candidates, marked, target, 0);
        return res;
    }

    public void dfs(int[] candidates, boolean[] marked, int target, int index) {
        if (target == 0) {
            res.add(new ArrayList<>(ans));
            return;
        }

        for (int i = index; i < candidates.length; i++) {
            if (candidates[i] > target) break;
            if (marked[i] == true) continue;
            if (i > index && candidates[i] == candidates[i-1]) 
                continue; // 这⾥是去重的关键逻辑,本次不取重复数字，下次循环可能会取重复数字
            marked[i] = true;
            ans.add(candidates[i]);
            dfs(candidates, marked, target - candidates[i], i+1); // i+1，这里与39题有差别
            ans.remove(ans.size() - 1);
            marked[i] = false;
        }
    }
}
```



#### [46. 全排列](https://leetcode-cn.com/problems/permutations/)

难度中等

给定一个 **没有重复** 数字的序列，返回其所有可能的全排列。

**示例:**

```
输入: [1,2,3]
输出:
[
  [1,2,3],
  [1,3,2],
  [2,1,3],
  [2,3,1],
  [3,1,2],
  [3,2,1]
]
```

**思想：**这里和 79题 的思想不一样，虽然都是回溯法，但因为该题的遍历方式：不是只能遍历左右或上下，而是整个数组都可以遍历，所以要用for循环来遍历。

```java
class Solution {
    public List<List<Integer>> res = new ArrayList<>();
    public List<Integer> ans = new ArrayList<>();

    public List<List<Integer>> permute(int[] nums) {
        if (nums == null || nums.length == 0) return res;
        boolean[] marked = new boolean[nums.length];
        dfs(marked, nums);
        return res;
    }

    public void dfs(boolean[] marked, int[] nums) {
        // 1. 终止条件
        if (ans.size() == nums.length) {
            res.add(new ArrayList(ans));
            return;
        }
		// 2. 使用for循环遍历（所以不用考虑边界条件）
        for (int i = 0; i < nums.length; i++) {
            // 3. 当前路径已遍历过
            if (marked[i] == true) continue;
            // 4. 满足条件，设标志为true
            marked[i] = true;
            ans.add(nums[i]);
            // 5. 递归，遍历下一条路径
            dfs(marked, nums);
            // 6. 回溯
            ans.remove(ans.size()-1);
            marked[i] = false;
        }
    }
}
```

##### [剑指 Offer 38. 字符串的排列](https://leetcode-cn.com/problems/zi-fu-chuan-de-pai-lie-lcof/)

难度中等

输入一个字符串，打印出该字符串中字符的所有排列。

你可以以任意顺序返回这个字符串数组，但里面不能有重复元素。

**示例:**

```
输入：s = "abc"
输出：["abc","acb","bac","bca","cab","cba"]
```

**限制：**

```
1 <= s 的长度 <= 8
```

**思想：**思想上和46题一样，但因为不能有重复元素，要**通过排序来去重**。

```java
class Solution {
    public List<String> ans = new ArrayList<>();
    public String[] permutation(String s) {
        if (s == null || s.length() == 0) return new String[0];
        char[] chars = s.toCharArray();
        Arrays.sort(chars); // 通过排序来去除重复排列
        boolean[] marked = new boolean[chars.length];
        StringBuilder str = new StringBuilder();

        dfs(chars, marked, str);
        
        String[] res = new String[ans.size()];
        return ans.toArray(res);
    }

    public void dfs(char[] chars, boolean[] marked, StringBuilder str) {
        if (str.length() == chars.length) {
            ans.add(new String(str));
            return;
        }

        for (int i = 0; i < chars.length; i++) {
            if (marked[i] == true) continue;
            if (i > 0 && chars[i] == chars[i-1] && marked[i-1] == true) continue;
            marked[i] = true;
            str.append(chars[i]);
            dfs(chars, marked, str);
            str.deleteCharAt(str.length() - 1);
            marked[i] = false;
        }
    }
}
```

#### [47. 全排列 II](https://leetcode-cn.com/problems/permutations-ii/)

难度中等

给定一个可包含重复数字的序列，返回所有不重复的全排列。

**示例:**

```
输入: [1,1,2]
输出:
[
  [1,1,2],
  [1,2,1],
  [2,1,1]
]
```

**思想：**

> 这⼀题是第 46 题的加强版，第 46 题中求数组的排列，数组中元素不重复，但是这⼀题中，数组元素会重复，所以需要最终排列出来的结果需要去重。去重的⽅法是经典逻辑，**将数组排序以后，判断重复元素再做逻辑判断**。 其他思路和第 46 题完全⼀致，DFS 深搜即可。

```java
class Solution {
    public List<List<Integer>> permuteUnique(int[] nums) {
        List<List<Integer>> permutes = new ArrayList<>();
        List<Integer> permuteList = new ArrayList<>();
        if (nums == null || nums.length == 0) return permutes;
        Arrays.sort(nums);
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
            if (i > 0 && nums[i] == nums[i-1] && marked[i-1] == true) continue;
            marked[i] = true;
            permuteList.add(nums[i]);
            dfs(permutes, permuteList, nums, marked);
            permuteList.remove(permuteList.size() - 1);
            marked[i] = false;
        }
    }
}
```



#### [77. 组合](https://leetcode-cn.com/problems/combinations/)

难度中等

给定两个整数 *n* 和 *k*，返回 1 ... *n* 中所有可能的 *k* 个数的组合。

**示例:**

```
输入: n = 4, k = 2
输出:
[
  [2,4],
  [3,4],
  [2,3],
  [1,2],
  [1,3],
  [1,4],
]
```

**思想：**计算排列组合中的组合，用DFS 深搜即可，注意剪枝。与46、47差不多思想

```java
class Solution {
    public List<List<Integer>> res = new ArrayList<>();
    public List<Integer> ans = new ArrayList<>();

    public List<List<Integer>> combine(int n, int k) {
        if (n <= 0 || k <= 0 || k > n) return res;
        dfs(n, k, 1);
        return res;
    }

    public void dfs(int n, int k, int start) {
        if (ans.size() == k) {
            res.add(new ArrayList<>(ans));
            return;
        }

        for (int i = start; i <= n; i++) {
            ans.add(i);
            dfs(n, k, i+1);
            ans.remove(ans.size() - 1);
        }
    }
}
```

#### [78. 子集](https://leetcode-cn.com/problems/subsets/)

难度中等711收藏分享切换为英文关注反馈

给定一组**不含重复元素**的整数数组 *nums*，返回该数组所有可能的子集（幂集）。

**说明：**解集不能包含重复的子集。

**示例:**

```
输入: nums = [1,2,3]
输出:
[
  [3],
  [1],
  [2],
  [1,2,3],
  [1,3],
  [2,3],
  [1,2],
  []
]
```

**思想：**子集问题可以看成是一个组合问题。见方法二

```java
class Solution {
    public List<List<Integer>> res = new ArrayList<>();
    public List<Integer> ans = new ArrayList<>();

    public List<List<Integer>> subsets(int[] nums) {
        if (nums == null || nums.length == 0) return res;
        dfs(nums, 0);
        return res;
    }

    public void dfs(int[] nums, int index) {
        if (ans.size() <= nums.length) {
            res.add(new ArrayList<>(ans));
        }

        for (int i = index; i < nums.length; i++) {
            ans.add(nums[i]);
            dfs(nums, i+1);
            ans.remove(ans.size() - 1);
        }
    }
}

// =============================方法二=================================
class Solution {
    public List<List<Integer>> res = new ArrayList<>();
    public List<Integer> ans = new ArrayList<>();

    public List<List<Integer>> subsets(int[] nums) {
        if (nums == null || nums.length == 0) return res;
        for (int k = 0; k <= nums.length; k++) { // k表示这次遍历中要组合的个数
            dfs(nums, 0, k);
        }
        
        return res;
    }

    public void dfs(int[] nums, int index, int k) {
        if (ans.size() == k) {
            res.add(new ArrayList<>(ans));
            return;
        }

        for (int i = index; i < nums.length; i++) {
            ans.add(nums[i]);
            dfs(nums, i+1, k);
            ans.remove(ans.size() - 1);
        }
    }
}
```

#### [90. 子集 II](https://leetcode-cn.com/problems/subsets-ii/)

难度中等289收藏分享切换为英文关注反馈

给定一个可能包含重复元素的整数数组 ***nums***，返回该数组所有可能的子集（幂集）。

**说明：**解集不能包含重复的子集。

**示例:**

```
输入: [1,2,2]
输出:
[
  [2],
  [1],
  [1,2,2],
  [2,2],
  [1,2],
  []
]
```

**思想：**在78题基础上去重

```java
class Solution {
    public List<List<Integer>> res = new ArrayList<>();
    public List<Integer> ans = new ArrayList<>();

    public List<List<Integer>> subsetsWithDup(int[] nums) {
        if (nums == null || nums.length == 0) return res;
        Arrays.sort(nums);
        dfs(nums, 0);
        return res;
    }

    public void dfs(int[] nums, int index) {
        if (ans.size() <= nums.length) {
            res.add(new ArrayList<>(ans));
        }

        for (int i = index; i < nums.length; i++) {
            if (i > index && nums[i] == nums[i-1]) continue;
            ans.add(nums[i]);
            dfs(nums, i+1);
            ans.remove(ans.size()-1);
        }
    } 
}
// =============================方法二=================================
class Solution {
    public List<List<Integer>> res = new ArrayList<>();
    public List<Integer> ans = new ArrayList<>();

    public List<List<Integer>> subsetsWithDup(int[] nums) {
        if (nums == null || nums.length == 0) return res;
        Arrays.sort(nums); // 通过排序来去重
        for (int k = 0; k <= nums.length; k++) {
            dfs(nums, 0, k);
        }
        return res;
    }

    public void dfs(int[] nums, int index, int k) {
        if (ans.size() == k) {
            res.add(new ArrayList<>(ans));
            return;
        }

        for (int i = index; i < nums.length; i++) {
            if (i > index && nums[i] == nums[i-1]) continue; // 去重
            ans.add(nums[i]);
            dfs(nums, i+1, k);
            ans.remove(ans.size()-1);
        }
    } 
}
```



#### [79. 单词搜索](https://leetcode-cn.com/problems/word-search/)

难度中等

给定一个二维网格和一个单词，找出该单词是否存在于网格中。

单词必须按照字母顺序，通过相邻的单元格内的字母构成，其中“相邻”单元格是那些水平相邻或垂直相邻的单元格。同一个单元格内的字母不允许被重复使用。

**示例:**

```
board =
[
  ['A','B','C','E'],
  ['S','F','C','S'],
  ['A','D','E','E']
]

给定 word = "ABCCED", 返回 true
给定 word = "SEE", 返回 true
给定 word = "ABCB", 返回 false
```

**提示：**

- `board` 和 `word` 中只包含大写和小写英文字母。
- `1 <= board.length <= 200`
- `1 <= board[i].length <= 200`
- `1 <= word.length <= 10^3`

**思想：**看剑指offer 12题

```java
class Solution {
    public boolean exist(char[][] board, String word) {
        if (board == null || board.length == 0 || board[0].length == 0 || word == null) 
            return false;
        boolean[][] marked = new boolean[board.length][board[0].length];
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[i].length; j++) {
                if (dfs(marked, board, i, j, word, 0))
                    return true;
            }
        }
        return false;
    }

    public boolean dfs(boolean[][] marked, char[][] board, int r, int c, String word, int k) {
        if (r >= board.length || r < 0 || c >= board[r].length || c < 0 || k >= word.length())
            return false;
        if (board[r][c] != word.charAt(k) || marked[r][c] == true) return false;
        if (word.length() - 1 == k) return true;
        
        marked[r][c] = true;
        boolean res = dfs(marked, board, r+1, c, word, k+1) ||
                        dfs(marked, board, r-1, c, word, k+1) ||
                        dfs(marked, board, r, c+1, word, k+1) ||
                        dfs(marked, board, r, c-1, word, k+1);
        marked[r][c] = false;
        return res;
    }
}

//
class Solution {
    public boolean exist(char[][] board, String word) {
        if (board == null || board.length == 0 || board[0].length == 0 || word == null) 
            return false;
        boolean[][] marked = new boolean[board.length][board[0].length];
        for (int i = 0; i < board.length; i++) {
            for (int j = 0; j < board[i].length; j++) {
                if (dfs(marked, board, i, j, word))
                    return true;
            }
        }
        return false;
    }

    public boolean dfs(boolean[][] marked, char[][] board, int r, int c, String word) {
        // 1. 终止条件
        if (word.length() == 0) return true;
        // 2. 边界
        if (r >= board.length || r < 0 || c >= board[r].length || c < 0) return false;
        // 3. 当前路径不满足条件 或 已经遍历过
        if (board[r][c] != word.charAt(0) || marked[r][c] == true) return false;
        // 4. 当前路径满足条件，设标志为true，表示该路径已访问
        marked[r][c] = true;
        // 5. 遍历下一路径
        boolean res = dfs(marked, board, r+1, c, word.substring(1, word.length())) ||
                        dfs(marked, board, r-1, c, word.substring(1, word.length())) ||
                        dfs(marked, board, r, c+1, word.substring(1, word.length())) ||
                        dfs(marked, board, r, c-1, word.substring(1, word.length()));
        // 6. 将当前路径标志为false
        marked[r][c] = false;
        return res;
    }
}
```

##### [剑指 Offer 13. 机器人的运动范围](https://leetcode-cn.com/problems/ji-qi-ren-de-yun-dong-fan-wei-lcof/)

难度中等

地上有一个m行n列的方格，从坐标 `[0,0]` 到坐标 `[m-1,n-1]` 。一个机器人从坐标 `[0, 0] `的格子开始移动，它每次可以向左、右、上、下移动一格（不能移动到方格外），也不能进入行坐标和列坐标的数位之和大于k的格子。例如，当k为18时，机器人能够进入方格 [35, 37] ，因为3+5+3+7=18。但它不能进入方格 [35, 38]，因为3+5+3+8=19。请问该机器人能够到达多少个格子？

**示例 1：**

```
输入：m = 2, n = 3, k = 1
输出：3
```

**示例 2：**

```
输入：m = 3, n = 1, k = 0
输出：1
```

**提示：**

- `1 <= n,m <= 100`
- `0 <= k <= 20`

**思想：**

> 使用**深度优先搜索**（Depth First Search，DFS）方法进行求解。**回溯是深度优先搜索的一种特例**，**它在一次搜索过程中需要设置一些本次搜索过程的局部状态，并在本次搜索结束之后清除状态**。而普通的深度优先搜索**并不需要使用这些局部状态**，虽然还是有可能设置一些全局状态。 

```java
class Solution {
    public int res = 0;
    public int movingCount(int m, int n, int k) {
        if (m < 0 || n < 0 || k < 0) return 0;
        boolean[][] marked = new boolean[m][n];
        dfs(marked, 0, 0, k);
        return res;
    }

    public void dfs(boolean[][] marked, int m, int n, int k) {
        // 1. 边界条件
        if (m < 0 || m >= marked.length || n < 0 || n >= marked[m].length) return;
        // 2. 该路径不符合条件 或 已经遍历过
        if (DigitSum(m) + DigitSum(n) > k || marked[m][n] == true) return;
        // 3. 终止条件
        // 4. 满足条件，设标志为true
        res++;
        marked[m][n] = true;
        // 5. 遍历下一路径
        dfs(marked, m+1, n, k);
        dfs(marked, m-1, n, k);
        dfs(marked, m, n+1, k);
        dfs(marked, m, n+1, k);
        // 6. 回溯，即设标志为false // 深度优先搜索算法在这里不用回溯标记。
        // marked[m][n] = false;
    }

    public int DigitSum(int n) {
        int digitSumOne = 0;
        while(n > 0) {
            digitSumOne += n % 10;
            n /= 10;
        }
        return digitSumOne;
    }
}
```

#### [112. 路径总和](https://leetcode-cn.com/problems/path-sum/)

难度简单

给定一个二叉树和一个目标和，判断该树中是否存在根节点到叶子节点的路径，这条路径上所有节点值相加等于目标和。

**说明:** 叶子节点是指没有子节点的节点。

**示例:** 
给定如下二叉树，以及目标和 `sum = 22`，

```
              5
             / \
            4   8
           /   / \
          11  13  4
         /  \      \
        7    2      1
```

返回 `true`, 因为存在目标和为 22 的根节点到叶子节点的路径 `5->4->11->2`。

思想：

```java
class Solution {
    public boolean hasPathSum(TreeNode root, int sum) {
        if (root == null) return false;
        // 到达叶子节点
        if (root.left == null && root.right == null) return sum == root.val;
        // 没到达叶子节点
        return hasPathSum(root.left, sum - root.val) 
            || hasPathSum(root.right, sum - root.val);
    }
```

#### [113. 路径总和 II](https://leetcode-cn.com/problems/path-sum-ii/)

难度中等

给定一个二叉树和一个目标和，找到所有从根节点到叶子节点路径总和等于给定目标和的路径。

**说明:** 叶子节点是指没有子节点的节点。

**示例:**
给定如下二叉树，以及目标和 `sum = 22`，

```
              5
             / \
            4   8
           /   / \
          11  13  4
         /  \    / \
        7    2  5   1
```

返回:

```
[
   [5,4,11,2],
   [5,8,4,5]
]
```

**思想：**

```java
class Solution {
    public List<List<Integer>> res = new ArrayList<>();
    public List<Integer> ans = new ArrayList<>();

    public List<List<Integer>> pathSum(TreeNode root, int sum) {
        if (root == null) return res;
        dfs(root, sum);
        return res;
    }

    public void dfs(TreeNode root, int sum) {
        // 1. 边界
        if (root == null) return;
        // 2. 到达叶子节点，看是否满足条件：不满足条件 （这条件可以归类到【边界】中）
        // if (root.left == null && root.right == null && sum != root.val) return;
        ans.add(root.val);
        // 3. 到达叶子节点，看是否满足条件：满足条件
        if (root.left == null && root.right == null && sum == root.val) {
            res.add(new ArrayList<>(ans));
        } else { // 4. 没有到达叶子节点
            // 5. 遍历下一节点
            dfs(root.left, sum - root.val);
            dfs(root.right, sum - root.val);
        }
        // 6. 回溯
        ans.remove(ans.size() - 1);
    }
}
```







## 动态规划

#### [5. 最长回文子串](https://leetcode-cn.com/problems/longest-palindromic-substring/)

难度中等

给定一个字符串 `s`，找到 `s` 中最长的回文子串。你可以假设 `s` 的最大长度为 1000。

**示例 1：**

```
输入: "babad"
输出: "bab"
注意: "aba" 也是一个有效答案。
```

**示例 2：**

```
输入: "cbbd"
输出: "bb"
```

**思想：**

>动态规划。
>
>一个子串如果是回文串，那它去掉首尾两个字符还是回文串；首尾分别加上一个字符：如果两个字符相等，则还是回文串；否则不是。
>
>我们用 `s[i, j]` 来表示从第 `i` 个位置的字符到第 `j` 个位置上的字符是否是回文串：
>
>- 如果`s[i, j] = true`：是回文串；如果`= false`：不是回文串。
>
>也就是说：只有 `s[i+1, j-1]`是回文串，并且 `s`的第 `i` 和 `j` 个字母相同时，`s[i, j]`才会是回文串。 则**动态规划方程**为：
>$$
>s[i, j] = s[i+1, j-1] ∩ (s[i] == s[j]);
>$$
>考虑动态规划中的边界条件，即子串的长度为 1 或 2。对于长度为 1 的子串，它显然是个回文串；对于长度为 2 的子串，只要它的两个字母相同，它就是一个回文串。因此我们就可以写出动态规划的**边界条件**：
>
>$$
>s[i,i] = true;\\
>s[i,i+1] = (s[i] == s[i+1])
>$$

**填表规则：先一列一列的填写，再一行一行的填，保证左下方的单元格先进行计算**

```java
class Solution {
    public String longestPalindrome(String s) {
        int len = s.length();
        // 特判
        if (len < 2){
            return s;
        }

        int maxLen = 1; // 要考虑下面是否进入for循环的情况
        int begin  = 0;

        // 1. 状态定义
        // dp[i][j] 表示s[i...j] 是否是回文串


        // 2. 初始化
        boolean[][] dp = new boolean[len][len];
        for (int i = 0; i < len; i++) {
            dp[i][i] = true;
        }

        char[] chars = s.toCharArray();
        // 3. 状态转移
        // 注意：先填左下角
        // 填表规则：先一列一列的填写，再一行一行的填，保证左下方的单元格先进行计算
        for (int j = 1;j < len;j++){
            for (int i = 0; i < j; i++) {
                // 头尾字符不相等，不是回文串
                if (chars[i] != chars[j]){
                    dp[i][j] = false;
                }else {
                    // 相等的情况下
                    // 考虑头尾去掉以后没有字符剩余，或者剩下一个字符的时候，肯定是回文串
                    if (j - i < 3){
                        dp[i][j] = true;
                    }else {
                        // 状态转移
                        dp[i][j] = dp[i + 1][j - 1];
                    }
                }

                // 只要dp[i][j] == true 成立，表示s[i...j] 是否是回文串
                // 此时更新记录回文长度和起始位置
                if (dp[i][j] && j - i + 1 > maxLen){
                    maxLen = j - i + 1;
                    begin = i;
                }
            }
        }
        // 4. 返回值
        return s.substring(begin,begin + maxLen);
    }
}
```

#### 



## 滑动窗口

#### [3. 无重复字符的最长子串](https://leetcode-cn.com/problems/longest-substring-without-repeating-characters/)

难度中等

给定一个字符串，请你找出其中不含有重复字符的 **最长子串** 的长度。

**示例 1:**

```
输入: "abcabcbb"
输出: 3 
解释: 因为无重复字符的最长子串是 "abc"，所以其长度为 3。
```

**示例 2:**

```
输入: "bbbbb"
输出: 1
解释: 因为无重复字符的最长子串是 "b"，所以其长度为 1。
```

**示例 3:**

```
输入: "pwwkew"
输出: 3
解释: 因为无重复字符的最长子串是 "wke"，所以其长度为 3。
     请注意，你的答案必须是 子串 的长度，"pwke" 是一个子序列，不是子串。
```

**思想：**

>滑动窗口。
>
>初始化一个哈希表，用于存放元素值以及其索引。
>
>滑动窗口左边界为 `left`，初始化为 `0`；右边界从`0`开始遍历（依次存放到哈希表中），如果当前遍历到的元素 能在哈希表中找到，分为两种情况：
>
>- 从哈希表中取出该元素对应的索引，如果该索引小于`left`，则说明该重复元素不在滑动窗口中，继续遍历下一个元素；
>- 如果该索引大于等于`left`，说明该重复元素在滑动窗口中，所以将滑动窗口的左边界`left`移到`索引+1`;

```java
class Solution {
    public int lengthOfLongestSubstring(String s) {
        if (s == null || s.length() == 0) return 0;
        HashMap<Character, Integer> map = new HashMap<>();
        int left = 0, right = 0;
        int maxLen = 0;
        for (int i = 0; i < s.length(); i++) {
            if (map.containsKey(s.charAt(i))) {
                left = Math.max(map.get(s.charAt(i)) + 1, left);
            }
            map.put(s.charAt(i), i);
            maxLen = Math.max(maxLen, i-left+1);
        }
        return maxLen;
    }
}
```

#### 





## 双指针—链表

#### [19. 删除链表的倒数第N个节点](https://leetcode-cn.com/problems/remove-nth-node-from-end-of-list/)-8.24

难度中等

给定一个链表，删除链表的倒数第 *n* 个节点，并且返回链表的头结点。

**示例：**

```
给定一个链表: 1->2->3->4->5, 和 n = 2.

当删除了倒数第二个节点后，链表变为 1->2->3->5.
```

**说明：**

给定的 *n* 保证是有效的。

**进阶：**

你能尝试使用一趟扫描实现吗？

**思想：**快慢指针，快指针指向第n+1个节点，这时慢指针才开始和快指针一起移动。

```java
class Solution {
    public ListNode removeNthFromEnd(ListNode head, int n) {
        if (n <= 0) return head;
        ListNode fast = head, slow = head;
        int len = 0;
        while (fast != null && len < n) {
            fast = fast.next;
            len++;
        } // if(len == n)，则fast指向第n+1节点

        if (len != n && fast == null) return head;
        if (len == n && fast == null) return head.next;
        if (len == n && fast != null) { 
            while (fast.next != null) { // 此时fast指向第n+1个节点
                fast = fast.next;
                slow = slow.next;
            }
            slow.next = slow.next.next;
            return head;
        }
        return null;
    }
}
```



#### [61. 旋转链表](https://leetcode-cn.com/problems/rotate-list/)-8.24

难度中等320

给定一个链表，旋转链表，将链表每个节点向右移动 *k* 个位置，其中 *k* 是非负数。

**示例 1:**

```
输入: 1->2->3->4->5->NULL, k = 2
输出: 4->5->1->2->3->NULL
解释:
向右旋转 1 步: 5->1->2->3->4->NULL
向右旋转 2 步: 4->5->1->2->3->NULL
```

**示例 2:**

```
输入: 0->1->2->NULL, k = 4
输出: 2->0->1->NULL
解释:
向右旋转 1 步: 2->0->1->NULL
向右旋转 2 步: 1->2->0->NULL
向右旋转 3 步: 0->1->2->NULL
向右旋转 4 步: 2->0->1->NULL
```

**思想：**

> 这道题需要注意的点是，K 可能很⼤，K = 2000000000 ，如果是循环肯定会超时。应该找出 O(n) 的复杂度的算法才行。由于是循环旋转，最终状态其实是确定的，利⽤链表的⻓度取余可以得到链表的最终旋转结果。 
>
> 这道题也不能⽤递归，递归解法会超时。
>
> 1. 首先定义指针`cur`和 int 型变量`len`，遍历完之后`cur`指向链表的最后一个元素（NULL前面的那个元素），`len`即为链表的长度。
> 2. 我们发现，假如`k`刚好是`len`的整数倍的话，其实链表旋转了`k`次之后，压根就没有变化（直接返回`head`即可），所以不管`k`是多少，链表旋转`k`次的结果，会和旋转`k%len`次相同。
> 3. 遍历完的cur指向尾节点，将其`next`指向头结点`head`，则链表成环；
> 4. 将`cur`指针重新指向`head`，当`cur`往后走`len - k -1`个单位之后，它后面的部分就是要断开的部分。
>    - 比如说，链表是a->b->c->d->e->f->g，`k=3`。那么会有：`len=7`、`cur`最终指向g、`k=3`和`len-k-1=3`。
>    - 最终链表应该是e->f->g->a->b->c->d，所以e->f->g是应该被断开的部分，然后接到`a`的前面。
>    - 于是乎，`cur`从`head`处往后走`len-k-1`个单位之后，会来到d，然后`cur`后面刚好就会是e->f->g。
>    - 让`cur -> next = null`，就会把e->f->g从链表中断开。

```java
class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        if (head == null) return null;
        int len = 0;
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        ListNode cur = dummy;
        while (cur.next != null) {
            len++;
            cur = cur.next;
        }
        if (k % len == 0) return head;
        cur.next = head; // 将链表 链成环（cur此时指向尾节点，尾节点的next指向头结点head）
        cur = dummy; // 将cur重新指向虚拟节点dummy
        for (int i = 0; i < len - k%len; i++) {
            cur = cur.next;
        }
        ListNode res = cur.next; // 旋转后的头结点为cur的下一节点
        cur.next = null;
        return res;
    }
}

class Solution {
    public ListNode rotateRight(ListNode head, int k) {
        if (head == null) return null;
        int len = 0;
        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        ListNode cur = dummy;
        while (cur.next != null) {
            len++;
            cur = cur.next;
        }
        if (k % len == 0) return head;
        cur.next = head; // 将链表 链成环（cur此时指向尾节点，尾节点的next指向头结点head）
        cur = head; // 将cur重新指向虚拟节点head
        for (int i = 0; i < len - k%len - 1; i++) {
            cur = cur.next;
        }
        ListNode res = cur.next; // 旋转后的头结点为cur的下一节点
        cur.next = null;
        return res;
    }
}
```



#### [86. 分隔链表](https://leetcode-cn.com/problems/partition-list/)-8.24

难度中等244

给定一个链表和一个特定值 *x*，对链表进行分隔，使得所有小于 *x* 的节点都在大于或等于 *x* 的节点之前。

你应当保留两个分区中每个节点的初始相对位置。

**示例:**

```
输入: head = 1->4->3->2->5->2, x = 3
输出: 1->2->2->4->3->5
```

思想：

```java
class Solution {
    public ListNode partition(ListNode head, int x) {
        if (head == null || head.next == null) return head;
        ListNode list_min = new ListNode(-1);
        ListNode list_max = new ListNode(-1);
        ListNode cur = head, l1 = list_min, l2 = list_max;
        while (cur != null) {
            if (cur.val < x) {
                l1.next = new ListNode(cur.val);
                l1 = l1.next;
            } else {
                l2.next = new ListNode(cur.val);
                l2 = l2.next;
            }
            cur = cur.next;
        }
        // l2.next = null;
        l1.next = list_max.next;
        return list_min.next;
    }
}

class Solution {
    public ListNode partition(ListNode head, int x) {
        if (head == null || head.next == null) return head;
        ListNode list_min = new ListNode(-1);
        ListNode list_max = new ListNode(-1);
        ListNode cur = head, l1 = list_min, l2 = list_max;
        while (cur != null) {
            if (cur.val < x) {
                l1.next = cur;
                l1 = l1.next;
            } else {
                l2.next = cur;
                l2 = l2.next;
            }
            cur = cur.next;
        }
        l2.next = null; // 此处一定要写，防止链表成环
        l1.next = list_max.next;
        return list_min.next;
    }
}
```

#### [面试题 02.01. 移除重复节点](https://leetcode-cn.com/problems/remove-duplicate-node-lcci/)

难度简单61

编写代码，移除未排序链表中的重复节点。保留最开始出现的节点。

**示例1:**

```
 输入：[1, 2, 3, 3, 2, 1]
 输出：[1, 2, 3]
```

**示例2:**

```
 输入：[1, 1, 1, 1, 2]
 输出：[1, 2]
```

**提示：**

1. 链表长度在[0, 20000]范围内。
2. 链表元素在[0, 20000]范围内。

**进阶：**

如果不得使用临时缓冲区，该怎么解决？

思想：

```java
// 进阶：没有使用临时缓冲区
class Solution {
    public ListNode removeDuplicateNodes(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode cur = head;
        while (cur != null) {
            ListNode pos = cur.next;
            ListNode pre = cur;
            while (pos != null) {
                if (cur.val == pos.val) {
                    pre.next = pos.next;
                    pos = pos.next;
                } else {
                    pos = pos.next;
                    pre = pre.next;
                }
            }
            cur = cur.next;
        }
        return head;
    }
}
// 简化
class Solution {
    public ListNode removeDuplicateNodes(ListNode head) {
        if (head == null || head.next == null) return head;
        ListNode cur = head;
        while (cur != null) {
            ListNode pos = cur
            while (pos.next != null) {
                if (cur.val == pos.next.val) {
                    pos.next = pos.next.next;
                } else {
                    pos = pos.next;
                }
            }
            cur = cur.next;
        }
        return head;
    }
}
```

#### [23. 合并K个升序链表](https://leetcode-cn.com/problems/merge-k-sorted-lists/)

难度困难869收藏分享切换为英文关注反馈

给你一个链表数组，每个链表都已经按升序排列。

请你将所有链表合并到一个升序链表中，返回合并后的链表。

 

**示例 1：**

```
输入：lists = [[1,4,5],[1,3,4],[2,6]]
输出：[1,1,2,3,4,4,5,6]
解释：链表数组如下：
[
  1->4->5,
  1->3->4,
  2->6
]
将它们合并到一个有序链表中得到。
1->1->2->3->4->4->5->6
```

**示例 2：**

```
输入：lists = []
输出：[]
```

**示例 3：**

```
输入：lists = [[]]
输出：[]
```

**思想：**将数组中的链表两两合并

```java
class Solution {
    public ListNode mergeKLists02(ListNode[] lists) { // 方法一：逐步合并
        if (lists == null || lists.length == 0) return null;
        if (lists.length == 1) return lists[0];
        ListNode res = lists[0];
        for (int i = 1; i < lists.length; i++) {
            res = mergeTwoLists(res, lists[i]);
        }
        return res;
    }
    
    public ListNode mergeKLists02(ListNode[] lists) { // 方法二：两两合并
        if (lists == null || lists.length == 0) return null;
        if (lists.length == 1) return lists[0];
        return merge(lists, 0, lists.length-1);
    }
    private ListNode merge(ListNode[] lists, int lo, int hi) {
        if (lo == hi) {
            return lists[lo];
        }
        int mid = lo + (hi - lo) / 2;
        ListNode l1 = merge(lists, lo, mid);
        ListNode l2 = merge(lists, mid + 1, hi);
        return mergeTwoLists(l1, l2);
    }

    public ListNode mergeTwoLists(ListNode l1, ListNode l2) {
        if (l1 == null && l2 == null) return null;
        if (l1 == null) return l2;
        if (l2 == null) return l1;
        ListNode p = l1, q = l2;
        ListNode newList = new ListNode(-1);
        ListNode cur = newList;
        while (p != null && q != null) {
            if (p.val > q.val) {
                cur.next = q;
                q = q.next;
            } else if (p.val <= q.val) {
                cur.next = p;
                p = p.next;
            }
            cur = cur.next;
        }

        if (p == null && q == null) return newList.next;
        else if (p == null) cur.next = q;
        else if (q == null) cur.next = p;
        return newList.next;
    }
}
```

#### [24. 两两交换链表中的节点](https://leetcode-cn.com/problems/swap-nodes-in-pairs/)

难度中等600收藏分享切换为英文关注反馈

给定一个链表，两两交换其中相邻的节点，并返回交换后的链表。

**你不能只是单纯的改变节点内部的值**，而是需要实际的进行节点交换。

**示例:**

```
给定 1->2->3->4, 你应该返回 2->1->4->3.
```

**思想：**

我们把链表分为两部分，即奇数节点为一部分，偶数节点为一部分，`A` 指的是交换节点中的前面的节点，`B` 指的是要交换节点中的后面的节点。在完成它们的交换，我们还得用 `prevNode` 记录 A 的前驱节点。

1. `firstNode（即 A）` 和 `secondNode（即 B）` 分别遍历偶数节点和奇数节点，即两步看作一步。

2. 交换两个节点：

   ```java
    firstNode.next = secondNode.next
    secondNode.next = firstNode
   ```

3. 还需要更新 `prevNode.next` 指向交换后的头。

   ```java
   prevNode.next = secondNode
   ```

4. 迭代完成后得到最终的交换结果。

```java
class Solution {
    public ListNode swapPairs(ListNode head) {
        if (head == null || head.next == null) return head;

        ListNode dummy = new ListNode(-1);
        dummy.next = head;
        ListNode pre = dummy;
        while (head != null && head.next != null) {

            ListNode firstNode = head, secondNode = head.next;

            firstNode.next = secondNode.next;
            secondNode.next = firstNode;
            pre.next = secondNode;

            pre = firstNode;
            head = firstNode.next;
        }
        return dummy.next;
    }
}
```

#### [25. K 个一组翻转链表](https://leetcode-cn.com/problems/reverse-nodes-in-k-group/)

难度困难

给你一个链表，每 *k* 个节点一组进行翻转，请你返回翻转后的链表。

*k* 是一个正整数，它的值小于或等于链表的长度。

如果节点总数不是 *k* 的整数倍，那么请将最后剩余的节点保持原有顺序。

**示例：**

给你这个链表：`1->2->3->4->5`

当 *k* = 2 时，应当返回: `2->1->4->3->5`

当 *k* = 3 时，应当返回: `3->2->1->4->5`

**说明：**

- 你的算法只能使用常数的额外空间。
- **你不能只是单纯的改变节点内部的值**，而是需要实际进行节点交换。

**思想：**这题是上一题的加强版。我们需要使用`双指针`来解决问题，用`start`表示头结点，`end`表示尾节点(将`end.next == null`)，将链表每k个节点分成一个新的链表进行反转。

```java
class Solution {
    public ListNode reverseKGroup(ListNode head, int k) {
        if (head == null || head.next == null || k <= 1) return head;
        ListNode dummpy = new ListNode(-1);
        dummpy.next = head;
        ListNode pre = dummpy;
        ListNode end = dummpy;
        while (end.next != null) {
            for (int i = 0; i < k && end != null; i++) {
                end = end.next;
            }
            if (end == null) break; // 当cur==null时，说明不足k个节点
            ListNode start = pre.next;
            ListNode next = end.next;
            end.next = null;
            pre.next = reverse(start);
            start.next = next;
            pre = start;

            end = pre;
        }
        return dummpy.next;
    }

    private ListNode reverse(ListNode head) {
        ListNode pre = null;
        ListNode curr = head;
        while (curr != null) {
            ListNode next = curr.next;
            curr.next = pre;
            pre = curr;
            curr = next;
        }
        return pre;
    }
}
```



## LeetCode

#### [7. 整数反转](https://leetcode-cn.com/problems/reverse-integer/)

难度简单

给出一个 32 位的有符号整数，你需要将这个整数中每位上的数字进行反转。

**示例 1:**

```
输入: 123
输出: 321
```

 **示例 2:**

```
输入: -123
输出: -321
```

**示例 3:**

```
输入: 120
输出: 21
```

**注意:**

假设我们的环境只能存储得下 32 位的有符号整数，则其数值范围为 [−2^31, 2^31 − 1]。请根据这个假设，如果反转后整数溢出那么就返回 0。

**思想：**

```java
rev = 0;// rev从0开始

//pop operation:
pop = x % 10;
x /= 10;

//push operation:
temp = rev * 10 + pop;
rev = temp;
```

 当 `temp = rev⋅10 + pop` 时会导致溢出 ，有两种情况：

1.  假设 rev 是正数：
    - 如果 `temp = rev⋅10 + pop` 导致溢出，那么一定有 `rev >= INTMAX / 10` ；
    - 如果 `rev > INTMAX / 10` ，那么 `temp = rev · 10 + pop `一定溢出；
    - 如果 `rev = INTMAX / 10` ，那么只要 `pop > 7`，`temp = rev · 10 + pop `就会溢出。
2.  假设 rev 是负数：
    - 如果 `temp = rev⋅10 + pop` 导致溢出，那么一定有 `rev <= INTMIN / 10` ；
    - 如果 `rev < INTMIN / 10` ，那么 `temp = rev · 10 + pop `一定溢出；
    - 如果 `rev = INTMIN / 10` ，那么只要 `pop < -8`，`temp = rev · 10 + pop `就会溢出。

```java
class Solution {
    public int reverse(int x) {
        int rev = 0;
        while (x != 0) {
            int pop = x % 10;
            x /= 10;
            if (rev > Integer.MAX_VALUE/10 || (rev == Integer.MAX_VALUE / 10 && pop > 7)) 
                return 0;
            if (rev < Integer.MIN_VALUE/10 || (rev == Integer.MIN_VALUE / 10 && pop < -8)) 
                return 0;
            rev = rev * 10 + pop;
        }
        return rev;
    }
}
```

#### [9. 回文数](https://leetcode-cn.com/problems/palindrome-number/)

难度简单

判断一个整数是否是回文数。回文数是指正序（从左向右）和倒序（从右向左）读都是一样的整数。

**示例 1:**

```
输入: 121
输出: true
```

**示例 2:**

```
输入: -121
输出: false
解释: 从左向右读, 为 -121 。 从右向左读, 为 121- 。因此它不是一个回文数。
```

**示例 3:**

```
输入: 10
输出: false
解释: 从右向左读, 为 01 。因此它不是一个回文数。
```

**进阶:**

你能不将整数转为字符串来解决这个问题吗？

**思想：**

有三种想法：

1. 将**数字转换为字符串**，并检查字符串是否是回文。但需要**额外的空间**来创建字符串；
2. 将**数字本身反转**，然后将反转后的数字与原始数字继续比较，相同则为回文数。但反转后的数字要考虑是否**溢出**；
3. 考虑只反转int数字的一半，例如将数字的后半部分反转，如果是回文数，其应该与前半部分的数字相同。

**算法：**（反转一半数字）

1. 首先，我们应该处理一些**临界情况**：
   - 所有负数都不可能是回文；
   - 除了 0 以外，所有个位是 0 的数字不可能是回文，因为最高位不等于 0。所以我们可以对所有大于 0 且个位是 0 的数字返回 false。
2. 接下来就是反转数字了，和上一题【整数反转】一样的思想，关键就是**如何知道是反转数字的位数已经达到原始数字位的一半**：
   - 反转的整个过程，我们不断将原始数字除以`10`，然后给反转后的数字乘以`10`，所以，当**原始数字小于或等于反转后的数字**时，就意味着我们已经处理了一半位数的数字了。
3. 注意：最后，我们还要考虑数字长度的**奇偶性**。

```java
class Solution {
    public boolean isPalindrome(int x) {
        // 特殊情况：
        // 如上所述，当 x < 0 时，x 不是回文数。
        // 同样地，如果数字的最后一位是 0，为了使该数字为回文，
        // 则其第一位数字也应该是 0
        // 只有 0 满足这一属性
        if (x < 0 || (x % 10 == 0 && x != 0)) {
            return false;
        }

        int revertedNumber = 0;
        while (x > revertedNumber) {
            revertedNumber = revertedNumber * 10 + x % 10;
            x /= 10;
        }

        // 当数字长度为奇数时，我们可以通过 revertedNumber/10 去除处于中位的数字。
        // 例如，当输入为 12321 时，在 while 循环的末尾我们可以得到 x = 12，revertedNumber = 123，
        // 由于处于中位的数字不影响回文（它总是与自己相等），所以我们可以简单地将其去除。
        return x == revertedNumber || x == revertedNumber / 10;
    }
}
```

#### [14. 最长公共前缀](https://leetcode-cn.com/problems/longest-common-prefix/)

难度简单

编写一个函数来查找字符串数组中的最长公共前缀。

如果不存在公共前缀，返回空字符串 `""`。

**示例 1:**

```
输入: ["flower","flow","flight"]
输出: "fl"
```

**示例 2:**

```
输入: ["dog","racecar","car"]
输出: ""
解释: 输入不存在公共前缀。
```

**说明:**

所有输入只包含小写字母 `a-z` 。

**思想：**

三种想法：

1. **横向扫描：**依次遍历字符串数组中的每个字符串，对于每个遍历到的字符串，更新最长公共前缀，当遍历完所有的字符串以后，即可得到字符串数组中的最长公共前缀。  如果在尚未遍历完所有的字符串时，最长公共前缀已经是空串，则最长公共前缀一定是空串，因此不需要继续遍历剩下的字符串，直接返回空串即可。 
2. **纵向扫描：**从前往后遍历所有字符串的每一列，比较相同列上的字符是否相同，如果相同则继续对下一列进行比较，如果不相同则当前列不再属于公共前缀，当前列之前的部分为最长公共前缀。（`需注意该列是否已经超出了某一字符串的长度`）

```java
// 横向扫描
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) {
            return "";
        }
        String prefix = strs[0];
        int count = strs.length;
        for (int i = 1; i < count; i++) {
            prefix = longestCommonPrefix(prefix, strs[i]);
            if (prefix.length() == 0) {
                break;
            }
        }
        return prefix;
    }

    public String longestCommonPrefix(String str1, String str2) {
        int length = Math.min(str1.length(), str2.length());
        int index = 0;
        while (index < length && str1.charAt(index) == str2.charAt(index)) {
            index++;
        }
        return str1.substring(0, index);
    }
}

// 纵向扫描
class Solution {
    public String longestCommonPrefix(String[] strs) {
        if (strs == null || strs.length == 0) return "";
        for (int i = 0; i < strs[0].length(); i++) {
            for (int j = 1; j < strs.length; j++) {
                if (i == strs[j].length() || strs[0].charAt(i) != strs[j].charAt(i))
                    return strs[0].substring(0, i);
            }
        }
        return strs[0];
    }
}
```



#### [20. 有效的括号](https://leetcode-cn.com/problems/valid-parentheses/)

难度简单1803

给定一个只包括 `'('`，`')'`，`'{'`，`'}'`，`'['`，`']'` 的字符串，判断字符串是否有效。

有效字符串需满足：

1. 左括号必须用相同类型的右括号闭合。
2. 左括号必须以正确的顺序闭合。

注意空字符串可被认为是有效字符串。

**示例 1:**

```
输入: "()"
输出: true
```

**示例 2:**

```
输入: "()[]{}"
输出: true
```

**示例 3:**

```
输入: "(]"
输出: false
```

**示例 4:**

```
输入: "([)]"
输出: false
```

**示例 5:**

```
输入: "{[]}"
输出: true
```

**思想：**

遇到左括号就进栈push，遇到右括号并且栈顶为与之对应的左括号，就把栈顶元素出栈。最后看栈⾥⾯还有没有其他元素，如果为空，即匹配。 

需要注意，空字符串是满⾜括号匹配的，即输出 true

```java
class Solution {
    public boolean isValid(String s) {
        if (s == null || s.length() % 2 == 1) return false;
        if (s.length() == 0) return true;
        Stack<Character> stack = new Stack<>();
        char[] chars = s.toCharArray();
        for (int i = 0; i < chars.length; i++) {
            if (chars[i] == '(' || chars[i] == '{' || chars[i] == '['){
                stack.push(chars[i]);
            } else if (chars[i] == ')' && stack.size() > 0 && stack.peek() == '(') {
                stack.pop();
            } else if (chars[i] == ']' && stack.size() > 0 && stack.peek() == '[') {
                stack.pop();
            } else if (chars[i] == '}' && stack.size() > 0 && stack.peek() == '{') {
                stack.pop();
            } else return false;
        }
        return stack.isEmpty();
    }
}
```

#### [21. 合并两个有序链表](https://leetcode-cn.com/problems/merge-two-sorted-lists/)

难度简单

将两个升序链表合并为一个新的 **升序** 链表并返回。新链表是通过拼接给定的两个链表的所有节点组成的。 

**示例：**

```
输入：1->2->4, 1->3->4
输出：1->1->2->3->4->4
```

思想：

```java

```





## Top K问题

#### 1. 返回N个数中最大的前M个数

```java
public class Main {
    public int[] topKFrequent(int[] nums, int k) {
        HashMap<Integer, Integer> map = new HashMap<>();
        Arrays.stream(nums)
            .forEach(x -> map.put(x, !map.containsKey(x)? 1: map.get(x)+1));
        PriorityQueue<Integer> pq = new PriorityQueue<>((Comparator.comparingInt(map::get)));
        for (Integer key: map.keySet()) {
            if (pq.size() < k) pq.add(key);
            else if (map.get(key) > map.get(pq.peek())) {
                pq.remove();
                pq.add(key);
            }
        }
        int[] res = new int[pq.size()];
        for (int i = 0; i < res.length; i++) {
            res[i] = pq.remove();
        }
        return res;
    }
}
```

