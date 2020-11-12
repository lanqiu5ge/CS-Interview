# 剑指 Offer 03. 数组中重复的数字
## 题目描述
[剑指 Offer 03. 数组中重复的数字](https://leetcode-cn.com/problems/shu-zu-zhong-zhong-fu-de-shu-zi-lcof/)

找出数组中重复的数字。

在一个长度为 n 的数组 nums 里的所有数字都在 0～n-1 的范围内。数组中某些数字是重复的，但不知道有几个数字重复了，也不知道每个数字重复了几次。请找出数组中任意一个重复的数字。

### 示例
```
示例 1：

输入：
[2, 3, 1, 0, 2, 5, 3]
输出：2 或 3 

```

> 限制：
> 
> 2 <= n <= 100000

## 解题思路
### 思路 1
- 标签：哈希表
- 借助 map 处理，遍历数组，如果 nums[i] 已在 map 中则认为是重复的，否则将 nums[i] 加入到 map 中继续遍历，知道遍历到重复元素为止
- 时间复杂度：O(n)
- 空间负责度：O(n)

### Code
```
    // 边界 case
	if len(nums) <= 1 {
		return -1
	}

	n := len(nums)
	m := make(map[int]int, n)

	for i := 0; i < n; i++ {
		v := nums[i]

		if m[v] > 0 {
			return v
		}

		m[v] = 1
	}

	return -1

```
