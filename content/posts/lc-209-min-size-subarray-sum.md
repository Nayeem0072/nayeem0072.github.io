---
title: "Coding Solutions (Java): LeetCode 209 - Minimum Size Subarray Sum"
date: 2024-01-01T23:40:00+06:00
description: "In-depth discussion about approaches to different solutions of LeetCode 209"
tags: [code, problem solving, leetcode]
---

[LeetCode 209](https://leetcode.com/problems/minimum-size-subarray-sum/)

Difficulty: **Medium**

**Steps:**

1. Find first sliding window of which sum satisfies >= target condition.
2. Decrease sliding window length by one (by removing the first element from the window).
3. Try to find another solution with this decreased sliding window.
4. If another solution is found, repeat 2 and 3 till the array nums[i] is traversed fully.

{{< figure src="https://assets.leetcode.com/users/images/1171386e-8bde-4764-9211-20bc1c04f47b_1645817267.1341352.png" >}}

**Complexities:**

TC: `O(n)`

SC: `O(1)`

**Solution**

```java
class Solution {
    public int minSubArrayLen(int target, int[] nums) {
		int sum = 0;
		int len = 0;
		
		// finding first sliding window
		for(int i = 0; i < nums.length; i++) {
			sum += nums[i];
			if(sum >= target) {
				len = i + 1;
				break;
			}
			
			// no such sliding window is found in nums[]
			if(i == nums.length - 1) 
				return 0;
		}

		int found = len;
		for(int i = 0; i < nums.length; i++) {
			// condition is satisfied, so decreasing sliding window length by removing first elem
			if(sum >= target) {
				sum = sum - nums[i];
				found = len;
				len = len - 1;
			}
			// condition is not satisfied, so moving current sliding window forward
			else { 
				if(i + len < nums.length) {
					sum = sum - nums[i] + nums[i + len];
					if(i + len == nums.length - 1) {						
						if(sum >= target) 
							found = len;
						break;
					}
				}				
			}
		}

		return found;
	}
}
```
