---
title: LIS(Longest Increasing Subsequence)
date: 2021-03-04 09:58:25
updated: 2020-03-04 09:58:25
categories: [algorithm]
---

### DP之最长递增子序列的写法

#### 时间复杂度为O（n^2）的DP写法

```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int n = (int)nums.size();
        if (n == 0) {
            return 0;
        }
        vector<int> dp(n, 0);
        for (int i = 0; i < n; ++i) {
            dp[i] = 1;
            for (int j = 0; j < i; ++j) {
                if (nums[j] < nums[i]) {
                    dp[i] = max(dp[i], dp[j] + 1);
                }
            }
        }
        return *max_element(dp.begin(), dp.end());
    }
};
```

------

代码思路介绍：使用dp数组保存在数组中第i个位置的最长子序列长度，则第i+1个位置最长子序列长度等于遍历dp前i项得到的比较结果。

#### 时间复杂度为O（nlogn）的贪心+二分查找写法

```c++
class Solution {
public:
    int lengthOfLIS(vector<int>& nums) {
        int len = 1, n = (int)nums.size();
        if (n == 0) {
            return 0;
        }
        vector<int> d(n + 1, 0);
        d[len] = nums[0];
        for (int i = 1; i < n; ++i) {
            if (nums[i] > d[len]) {
                d[++len] = nums[i];
            } else {
                int l = 1, r = len, pos = 0; // 如果找不到说明所有的数都比 nums[i] 大，此时要更新 d[1]，所以这里将 pos 设为 0
                while (l <= r) {
                    int mid = (l + r) >> 1;
                    if (d[mid] < nums[i]) {
                        pos = mid;
                        l = mid + 1;
                    } else {
                        r = mid - 1;
                    }
                }
                d[pos + 1] = nums[i];
            }
        }
        return len;
    }
};
```

维护一个最长上升子序列，dp[i]表示长度为i的最长上升子序列的最大项的最小值。

代码逻辑：

遍历数组的过程中如果第i+1个数大于dp[len]则len++，dp[len+1]=nums[i+1];

否则寻找到dp中第一个大于nums[i+1]的数的位置pos，dp[pos]=nums[i+1].

#### 时间复杂度为O（nlogn）的简写代码

```
class Solution {
public:
	int lengthOfLIS(vector<int>& nums) {
		int length = nums.size();
		int max=0;
		int dp[100000];
		for (int i = 0; i < length; i++) {
			dp[lower_bound(dp, dp + max, nums[i])-dp] = nums[i];
			if (lower_bound(dp, dp + max, nums[i]) == dp + max)max++;
			}
		return max;
	}
};

```

lower_bound是algorithm库函数里面自带的函数。

lower_ bound(first,last,val)用来寻找在数组或容器的[first,last)范围内第一个值大于等于val的元素的位置，如果是数组，则返回该位置的指针；如果是容器，则返回该位置的迭代器。




