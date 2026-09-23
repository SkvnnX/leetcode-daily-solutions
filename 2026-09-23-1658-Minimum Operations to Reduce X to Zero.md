# 1658. Minimum Operations to Reduce X to Zero（将 x 减到 0 的最小操作数）

- 日期：2026-09-23
- 题号：1658
- 题名：Minimum Operations to Reduce X to Zero
- 难度：Medium / 中等
- 标签/考点：数组、哈希表、二分查找、滑动窗口、前缀和
- 题目链接：https://leetcode.com/problems/minimum-operations-to-reduce-x-to-zero/
- 官方每日一题来源：LeetCode GraphQL activeDailyCodingChallengeQuestion，返回日期 2026-09-23

## 题意概括

给定正整数数组 `nums` 和整数 `x`。每次操作只能从数组最左端或最右端删除一个元素，并把该元素值从 `x` 中减去。要求用最少操作次数让 `x` 恰好变成 `0`；如果无法做到，返回 `-1`。

等价地说：从数组两端拿走若干元素，使拿走元素的总和为 `x`，并让拿走的元素数量尽量少。

## 关键思路

直接枚举左边拿多少、右边拿多少可以做，但不够直观。更好的转化是：

- 数组总和为 `total`。
- 如果两端被拿走的元素和为 `x`，那么中间保留下来的连续子数组和就是 `total - x`。
- 拿走的元素数量最少，等价于保留下来的连续子数组长度最长。

因此问题变成：找一个和为 `target = total - x` 的最长连续子数组。答案为：

```text
n - 最长长度
```

因为 `nums[i]` 都是正数，可以用滑动窗口维护当前窗口和：

1. 右指针不断扩展窗口。
2. 当窗口和大于 `target` 时，左指针右移缩小窗口。
3. 当窗口和等于 `target` 时，更新最长长度。

特殊情况：

- `target < 0`：总和都不够减到 `x`，返回 `-1`。
- `target == 0`：必须删除整个数组，返回 `n`。

## 为什么这样做是正确的

任意一种合法操作序列，最终删除的一定是原数组的一个前缀加一个后缀，中间剩下的部分仍然是连续子数组。删除元素和为 `x`，等价于剩余连续子数组和为 `total - x`。

如果剩余子数组越长，删除的元素就越少；所以最少操作次数对应最长的、和为 `target` 的连续子数组。由于数组元素全为正数，窗口和随右端扩展只会增加，随左端收缩只会减少，滑动窗口不会漏掉任何可能的连续子数组。

## 复杂度分析

- 时间复杂度：`O(n)`，每个元素最多被左右指针各访问一次。
- 空间复杂度：`O(1)`，只使用常数额外变量。

## 容易踩坑点

1. 不要直接贪心删除较大的一端；局部最优不保证全局最优。
2. 目标不是找和为 `x` 的最长子数组，而是找和为 `total - x` 的最长保留子数组。
3. `target == 0` 时答案是 `n`，表示全部删除。
4. 本题约束下 `nums[i] > 0`，所以滑动窗口成立；如果数组允许负数，就不能直接使用这个窗口逻辑。
5. `total` 最大可能到 `10^9` 量级，C++ 用 `long long` 更稳妥。

## LeetCode 可提交解法：C++

```cpp
class Solution {
public:
    int minOperations(vector<int>& nums, int x) {
        long long total = 0;
        for (int v : nums) total += v;

        long long target = total - x;
        int n = (int)nums.size();
        if (target < 0) return -1;
        if (target == 0) return n;

        long long windowSum = 0;
        int left = 0;
        int best = -1;

        for (int right = 0; right < n; ++right) {
            windowSum += nums[right];

            while (left <= right && windowSum > target) {
                windowSum -= nums[left];
                ++left;
            }

            if (windowSum == target) {
                best = max(best, right - left + 1);
            }
        }

        return best == -1 ? -1 : n - best;
    }
};
```

## LeetCode 可提交解法：Python 3

```python
from typing import List

class Solution:
    def minOperations(self, nums: List[int], x: int) -> int:
        total = sum(nums)
        target = total - x
        n = len(nums)

        if target < 0:
            return -1
        if target == 0:
            return n

        left = 0
        window_sum = 0
        best = -1

        for right, value in enumerate(nums):
            window_sum += value

            while left <= right and window_sum > target:
                window_sum -= nums[left]
                left += 1

            if window_sum == target:
                best = max(best, right - left + 1)

        return -1 if best == -1 else n - best
```

## ACM 答题模式

### 自定义输入格式

```text
n x
nums[0] nums[1] ... nums[n-1]
```

输出一个整数，表示最少操作次数；如果无法做到，输出 `-1`。

### ACM C++ 完整代码

```cpp
#include <bits/stdc++.h>
using namespace std;

int minOperations(vector<int>& nums, int x) {
    long long total = 0;
    for (int v : nums) total += v;

    long long target = total - x;
    int n = (int)nums.size();
    if (target < 0) return -1;
    if (target == 0) return n;

    long long windowSum = 0;
    int left = 0;
    int best = -1;

    for (int right = 0; right < n; ++right) {
        windowSum += nums[right];

        while (left <= right && windowSum > target) {
            windowSum -= nums[left];
            ++left;
        }

        if (windowSum == target) {
            best = max(best, right - left + 1);
        }
    }

    return best == -1 ? -1 : n - best;
}

int main() {
    ios::sync_with_stdio(false);
    cin.tie(nullptr);

    int n;
    int x;
    cin >> n >> x;

    vector<int> nums(n);
    for (int i = 0; i < n; ++i) {
        cin >> nums[i];
    }

    cout << minOperations(nums, x) << "\n";
    return 0;
}
```

### ACM Python 3 完整代码

```python
import sys


def min_operations(nums, x):
    total = sum(nums)
    target = total - x
    n = len(nums)

    if target < 0:
        return -1
    if target == 0:
        return n

    left = 0
    window_sum = 0
    best = -1

    for right, value in enumerate(nums):
        window_sum += value

        while left <= right and window_sum > target:
            window_sum -= nums[left]
            left += 1

        if window_sum == target:
            best = max(best, right - left + 1)

    return -1 if best == -1 else n - best


def main():
    data = list(map(int, sys.stdin.read().split()))
    if not data:
        return

    n, x = data[0], data[1]
    nums = data[2:2 + n]
    print(min_operations(nums, x))


if __name__ == "__main__":
    main()
```

## 示例与边界用例

| 用例 | 输入 | 输出 | 说明 |
|---|---|---:|---|
| 示例 1 | `nums = [1,1,4,2,3], x = 5` | `2` | 删除右侧 `2,3` |
| 示例 2 | `nums = [5,6,7,8,9], x = 4` | `-1` | 无法恰好减到 0 |
| 示例 3 | `nums = [3,2,20,1,1,3], x = 10` | `5` | 删除左侧 `3,2` 和右侧 `1,1,3` |
| 边界 1 | `nums = [1,1], x = 3` | `-1` | 总和小于 `x` |
| 边界 2 | `nums = [1,1], x = 2` | `2` | 删除整个数组 |
| 边界 3 | `nums = [1,2,3], x = 3` | `1` | 删除右端 `3` |
