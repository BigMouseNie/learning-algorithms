# [739. 每日温度](https://leetcode.cn/problems/daily-temperatures/)

给定一个整数数组 `temperatures` ，表示每天的温度，返回一个数组 `answer` ，其中 `answer[i]` 是指对于第 `i` 天，下一个更高温度出现在几天后。如果气温
在这之后都不会升高，请在该位置用 `0` 来代替。

**示例 1:**

**输入:** `temperatures` = [73,74,75,71,69,72,76,73]
**输出:** [1,1,4,2,1,1,0,0]

**示例 2:**

**输入:** temperatures = [30,40,50,60]
**输出:** [1,1,1,0]

**示例 3:**

**输入:** temperatures = [30,60,90]
**输出:** [1,1,0]

题解：单调栈
```C++
class Solution {
public:
    vector<int> dailyTemperatures(vector<int>& temperatures) {
        stack<int> stk;
        int size = temperatures.size();
        vector<int> ret(size, 0);
        for (int i=size-1; i>=0; --i)
        {
            while(!stk.empty() && temperatures.at(i) >= temperatures.at(stk.top()))
            {
                stk.pop();
            }
            if (stk.empty())
            {
                ret[i] = 0;
            }
            else
            {
                ret[i] = stk.top() - i;
            }
            stk.push(i);
        }

        return ret;
    }
```

# [647. 回文子串](https://leetcode.cn/problems/palindromic-substrings/)

给你一个字符串 `s` ，请你统计并返回这个字符串中 **回文子串** 的数目。

**回文字符串** 是正着读和倒过来读一样的字符串。

**子字符串** 是字符串中的由连续字符组成的一个序列。

**示例 1：**

**输入：** s = "abc"
**输出：** 3
**解释：** 三个回文子串: "a", "b", "c"

**示例 2：**

**输入：** s = "aaa"
**输出：** 6
**解释：** 6个回文子串: "a", "a", "a", "aa", "aa", "aaa"

题解：dp
当 `s[i] == s[j]` 时 `dp[i][j]` 是否回文取决于 `dp[i+1][j-1]`

```C++
class Solution {
public:
    int countSubstrings(string s) {
        int len = s.size();
        
        vector<vector<bool>> dp(len, vector<bool>(len, false));
        int result = 0;

        for(int i=len-1; i>=0; --i)
        {
            for(int j=i; j<len; ++j)
            {
                if (s[i] == s[j])
                {
                    if (j - i <= 1)
                    {
                        dp[i][j] = true;
                        result++;
                    }
                    else if (i+1<len && j-1>=0 && dp[i+1][j-1])
                    {
                        dp[i][j] = true;
                        result++;
                    }
                }
            }
        }
        return result;
    }
};
```


# [621. 任务调度器](https://leetcode.cn/problems/task-scheduler/)

给你一个用字符数组 `tasks` 表示的 CPU 需要执行的任务列表，用字母 A 到 Z 表示，以及一个冷却时间 `n`。每个周期或时间间隔允许完成一项任务。任务可以按任何顺序完成，但有一个限制：两个 **相同种类** 的任务之间必须有长度为 `n` 的冷却时间。

返回完成所有任务所需要的 **最短时间间隔** 。

**示例 1：**

**输入：**tasks = ["A","A","A","B","B","B"], n = 2

**输出：**8

**解释：**

在完成任务 A 之后，你必须等待两个间隔。对任务 B 来说也是一样。在第 3 个间隔，A 和 B 都不能完成，所以你需要待命。在第 4 个间隔，由于已经经过了 2 个间隔，你可以再次执行 A 任务。

**示例 2：**

**输入：** tasks = ["A","C","A","B","D","B"], n = 1

**输出：** 6

**解释：** 一种可能的序列是：A -> B -> C -> D -> A -> B。

由于冷却间隔为 1，你可以在完成另一个任务后重复执行这个任务。

**示例 3：**

**输入：** tasks = ["A","A","A","B","B","B"], n = 3

**输出：** 10

**解释：** 一种可能的序列为：A -> B -> idle -> idle -> A -> B -> idle -> idle -> A -> B。

只有两种任务类型，A 和 B，需要被 3 个间隔分割。这导致重复执行这些任务的间隔当中有两次待命状态。

题解：贪心？

```C++
/*
 * 解题思路：
 * 1、将任务按类型分组，正好A-Z用一个int[26]保存任务类型个数
 * 2、对数组进行排序，优先排列个数（count）最大的任务，
 *      如题得到的时间至少为 retCount =（count-1）* (n+1) + 1 ==> A->X->X->A->X->X->A(X为其他任务或者待命)
 * 3、再排序下一个任务，如果下一个任务B个数和最大任务数一致，
 *      则retCount++ ==> A->B->X->A->B->X->A->B
 * 4、如果空位都插满之后还有任务，那就随便在这些间隔里面插入就可以，因为间隔长度肯定会大于n，在这种情况下就是任务的总数是最小所需时间
 */

class Solution {
public:
    int leastInterval(vector<char>& tasks, int n) {
        int taskKind = 26;
        vector<int> vec(26, 0);
        for(char c : tasks)
        {
            vec[c-'A']++;
        }

        std::sort(vec.begin(), vec.end(), [](int a, int b){
            return a > b;
        });

        int maxTN = vec[0];
        int t = (maxTN - 1) * (n + 1) + 1;
        for (int i = 1; i<26; i++)
        {
            if (maxTN == vec[i])
            {
                t++;
            }
            else
            {
                break;
            }
        }
        return std::max(t, static_cast<int>(tasks.size()));
    }
};
```


# [617. 合并二叉树](https://leetcode.cn/problems/merge-two-binary-trees/)

给你两棵二叉树： `root1` 和 `root2` 。

想象一下，当你将其中一棵覆盖到另一棵之上时，两棵树上的一些节点将会重叠（而另一些不会）。你需要将这两棵树合并成一棵新二叉树。合并的规则是：如果两个节点重叠，那么将这两个节点的值相加作为合并后节点的新值；否则，**不为** null 的节点将直接作为新二叉树的节点。

返回合并后的二叉树。

**注意:** 合并过程必须从两个树的根节点开始。

**示例 1：**

![](https://assets.leetcode.com/uploads/2021/02/05/merge.jpg)

**输入：** root1 = [1,3,2,5], root2 = [2,1,3,null,4,null,7]
**输出：**[3,4,5,5,4,null,7]

**示例 2：**

**输入：** root1 = [1], root2 = [1,2]
**输出：** [2,2]

题解：常规遍历
```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    TreeNode* mergeTrees(TreeNode* root1, TreeNode* root2) {
        if (root1 || root2)
        {
            TreeNode* node = new TreeNode((root1 ? root1->val : 0) + (root2 ? root2->val : 0));
            node->left = mergeTrees(root1 ? root1->left : nullptr, root2 ? root2->left : nullptr);
            node->right = mergeTrees(root1 ? root1->right : nullptr,root2 ? root2->right : nullptr);
            return node;
        }
        return nullptr;
    }
};
```


# [581. 最短无序连续子数组](https://leetcode.cn/problems/shortest-unsorted-continuous-subarray/)

给你一个整数数组 `nums` ，你需要找出一个 **连续子数组** ，如果对这个子数组进行升序排序，那么整个数组都会变为升序排序。

请你找出符合题意的 **最短** 子数组，并输出它的长度。

**示例 1：**

**输入：** nums = [2,6,4,8,10,9,15]
**输出：** 5
**解释：** 你只需要对 [6, 4, 8, 10, 9] 进行升序排序，那么整个表都会变为升序排序。

**示例 2：**

**输入：** nums = [1,2,3,4]
**输出：** 0

**示例 3：**

**输入：** nums = [1]
**输出：** 0

题解：？
```C++
/*
从左到右循环，记录最大值为 max，若 nums[i] < max, 则表明位置 i 需要调整, 循环结束，记录需要调整的最大位置 i 为 high; 同理，从右到左循环，记录最小值为 min, 若 nums[i] > min, 则表明位置 i 需要调整，循环结束，记录需要调整的最小位置 i 为 low.
*/
/*
或者排序(简单)
*/

class Solution {
public:
    int findUnsortedSubarray(vector<int>& nums) {
        int len = nums.size();
        if(len <= 1) return 0;
        int low = len - 1;
        int high = 0;
        int _max = nums[0];
        int _min = nums[len-1];

        for (int i = 0; i < len; ++i)
        {
            _max = max(nums[i], _max);
            _min = min(nums[len-i-1], _min);
            if (nums[i] < _max)
                high = i;
            if (nums[len-i-1] > _min)
                low = len-i-1;
        }

        return high > low ? high - low + 1 : 0;
    }
};

```


# [560. 和为 K 的子数组](https://leetcode.cn/problems/subarray-sum-equals-k/)

给你一个整数数组 `nums` 和一个整数 `k` ，请你统计并返回 _该数组中和为 `k` 的子数组的个数_ 。

子数组是数组中元素的连续非空序列。

**示例 1：**

**输入：** nums = [1,1,1], k = 2
**输出：** 2

**示例 2：**

**输入：** nums = [1,2,3], k = 3
**输出：** 2

**提示：**

题解：前缀和

```C++
/*
p[j]-p[i]=k;即数组[i+1,j]的和为k
*/
class Solution {
public:
    int subarraySum(vector<int>& nums, int k) {
        int pred = 0;
        int res = 0;
        unordered_map<int,int> map;
        map[0] = 1;
        for(int num:nums){
            pred += num;
            if(map.find(pred-k)!=map.end()){
                res += map[pred-k];
            }
            map[pred]++;
        }

        return res;
    }
};

```


# [543. 二叉树的直径](https://leetcode.cn/problems/diameter-of-binary-tree/)

已解答

简单

相关标签

相关企业

给你一棵二叉树的根节点，返回该树的 **直径** 。

二叉树的 **直径** 是指树中任意两个节点之间最长路径的 **长度** 。这条路径可能经过也可能不经过根节点 `root` 。

两节点之间路径的 **长度** 由它们之间边数表示。

**示例 1：**

![](https://assets.leetcode.com/uploads/2021/03/06/diamtree.jpg)

**输入：** root = [1,2,3,4,5]
**输出：** 3
**解释：** 3 ，取路径 [4,2,1,3] 或 [5,2,1,3] 的长度。

**示例 2：**

**输入：** root = [1,2]
**输出：** 1

题解：常规
```C++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    int diameterOfBinaryTree(TreeNode* root) {
        dfs(root);
        return maxLen;
    }

    int dfs(TreeNode* root)
    {
        if (!root)
            return -1;
        int leftLen = 1 + dfs(root->left);
        int rightLen = 1 + dfs(root->right);
        maxLen = max(maxLen, leftLen + rightLen);
        return max(leftLen, rightLen);
    }

    int maxLen = 0;
};
```





# [538. 把二叉搜索树转换为累加树](https://leetcode.cn/problems/convert-bst-to-greater-tree/)



给出二叉 **搜索** 树的根节点，该树的节点值各不相同，请你将其转换为累加树（Greater Sum Tree），使每个节点 `node` 的新值等于原树中大于或等于 `node.val` 的值之和。

提醒一下，二叉搜索树满足下列约束条件：

- 节点的左子树仅包含键 **小于** 节点键的节点。
- 节点的右子树仅包含键 **大于** 节点键的节点。
- 左右子树也必须是二叉搜索树。

**注意：**本题和 1038: https://leetcode-cn.com/problems/binary-search-tree-to-greater-sum-tree/ 相同

 

**示例 1：**

**![img](https://assets.leetcode-cn.com/aliyun-lc-upload/uploads/2019/05/03/tree.png)**

```
输入：[4,1,6,0,2,5,7,null,null,null,3,null,null,null,8]
输出：[30,36,21,36,35,26,15,null,null,null,33,null,null,null,8]
```

**示例 2：**

```
输入：root = [0,null,1]
输出：[1,null,1]
```

**示例 3：**

```
输入：root = [1,0,2]
输出：[3,3,2]
```

**示例 4：**

```
输入：root = [3,2,4,1]
输出：[7,9,4,10]
```



题解：常规解法

```c++
/**
 * Definition for a binary tree node.
 * struct TreeNode {
 *     int val;
 *     TreeNode *left;
 *     TreeNode *right;
 *     TreeNode() : val(0), left(nullptr), right(nullptr) {}
 *     TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
 *     TreeNode(int x, TreeNode *left, TreeNode *right) : val(x), left(left), right(right) {}
 * };
 */
class Solution {
public:
    TreeNode* convertBST(TreeNode* root) {
        if (nullptr == root)
            return nullptr;
        convertBST(root->right);
        curSum += root->val;
        root->val = curSum;
        convertBST(root->left);
        return root;
    }

private:
    int curSum = 0;
};

```





# \*\*[494. 目标和](https://leetcode.cn/problems/target-sum/)



给你一个非负整数数组 `nums` 和一个整数 `target` 。

向数组中的每个整数前添加 `'+'` 或 `'-'` ，然后串联起所有整数，可以构造一个 **表达式** ：

- 例如，`nums = [2, 1]` ，可以在 `2` 之前添加 `'+'` ，在 `1` 之前添加 `'-'` ，然后串联起来得到表达式 `"+2-1"` 。

返回可以通过上述方法构造的、运算结果等于 `target` 的不同 **表达式** 的数目。

 

**示例 1：**

```
输入：nums = [1,1,1,1,1], target = 3
输出：5
解释：一共有 5 种方法让最终目标和为 3 。
-1 + 1 + 1 + 1 + 1 = 3
+1 - 1 + 1 + 1 + 1 = 3
+1 + 1 - 1 + 1 + 1 = 3
+1 + 1 + 1 - 1 + 1 = 3
+1 + 1 + 1 + 1 - 1 = 3
```

**示例 2：**

```
输入：nums = [1], target = 1
输出：1
```

题解：dfs/dp





# [461. 汉明距离](https://leetcode.cn/problems/hamming-distance/)



两个整数之间的 [汉明距离](https://baike.baidu.com/item/汉明距离) 指的是这两个数字对应二进制位不同的位置的数目。

给你两个整数 `x` 和 `y`，计算并返回它们之间的汉明距离。

 

**示例 1：**

```
输入：x = 1, y = 4
输出：2
解释：
1   (0 0 0 1)
4   (0 1 0 0)
       ↑   ↑
上面的箭头指出了对应二进制位不同的位置。
```

**示例 2：**

```
输入：x = 3, y = 1
输出：1
```

 

提示：汉明码意思是一个字符串替换成另一个的最小变化

```C++
class Solution {
public:
    int hammingDistance(int x, int y) {
        int res = 0;
        int p1 = 1 << 30;
        for (int t=0; t<31;++t)
        {
            if ((p1 & x) != (p1 & y))
            {
                ++res;
            }
            p1 = p1 >> 1;
        }

        return res;
    }
};

```



# [448. 找到所有数组中消失的数字](https://leetcode.cn/problems/find-all-numbers-disappeared-in-an-array/)



给你一个含 `n` 个整数的数组 `nums` ，其中 `nums[i]` 在区间 `[1, n]` 内。请你找出所有在 `[1, n]` 范围内但没有出现在 `nums` 中的数字，并以数组的形式返回结果。

 

**示例 1：**

```
输入：nums = [4,3,2,7,8,2,3,1]
输出：[5,6]
```

**示例 2：**

```
输入：nums = [1,1]
输出：[2]
```

 

**提示：**

- `n == nums.length`
- `1 <= n <= 105`
- `1 <= nums[i] <= n`

**进阶：**你能在不使用额外空间且时间复杂度为 `O(n)` 的情况下解决这个问题吗? 你可以假定返回的数组不算在额外空间内。

题解：数组边界

```C++
class Solution {
public:
    vector<int> findDisappearedNumbers(vector<int>& nums) {
        int size = nums.size();

        for (int i = 0; i < size; ++i)
        {
            if (nums[i] == i+1)
            {
                continue;
            }
            
            int t = nums[i];
            while (nums[t-1] != t)
            {
                int s = nums[t-1];
                nums[t-1] = t;
                t = s;
            }
        }

        vector<int> ret;
        for (int i = 0; i < size; ++i)
        {
            if (nums[i] != i+1)
            {
                ret.push_back(i+1);
            }
        }

        return ret;
    }
};
```





























