---
title: "Coding Solutions (Java): LeetCode 343 - Integer Break"
date: 2024-01-01T23:15:00+06:00
description: "In-depth discussion about approaches to different solutions of LeetCode 343"
tags: [code, problem solving, leetcode]
---
[LeetCode 343](https://leetcode.com/problems/integer-break/)

Difficulty: **Medium**

This is a variation of the `0/1 Knapsack Problem`, where capacity of the bag is `n`, and the available items are in the range of `1` to `n`.

The basic general idea of this particular solution is to express `n` as the sum of `(n - i, i)`, start to solve for `n - i`, and find the max product of `(n - i, i)` along the way. 
Base case is when `n = 1`, output should be `1`.


[I usually find dynamic programming solutions really baffling if I don't solve the recursion solutions first, so I solved this problem step-by-step. If you find the dynamic solution derived from recursive solution is puzzling, maybe this [link](https://www.educative.io/courses/grokking-dynamic-programming-a-deep-dive-using-java/introduction-to-0-1-knapsack) regarding step-by-step Knapsack problem solution can be helpful for you. ]


**1. Basic Recursion Solution [TLE]**

```java

public int integerBreak(int n) {
	return recurse(n);
}

private int recurse(int n) {
	if(n == 1) return 1;

	int max = 0;
	for(int i = 1; i < n; i++) {
		int val = Math.max(i * (n - i), i * recurse(n - i));
		max = Math.max(max, val);
	}

	return max;
}
```

**2. Recursion with Memoization Solution [Accepted - 0ms]**
```java
public int integerBreak(int n) {
	int[] memo = new int[n + 1];
	return recurse(n, memo);
}

private int recurse(int n, int[] memo) {
	if(n == 1) return 1;
	if(memo[n] != 0) return memo[n];

	int max = 0;
	for(int i = 1; i < n; i++) {
		int val = Math.max(i * (n - i), i * recurse(n - i, memo));
		max = Math.max(max, val);
	}

	return memo[n] = max;
}

```

**3. Dynamic Programming Solution [Accepted - 1ms]**
```java
public int integerBreak(int n) {
	int[] dp = new int[n + 1];
	dp[1] = 1;

	for(int i = 1; i < n; i++) {
		for(int j = i; j <= n; j++) {
			int val = Math.max(i * (j - i), i * dp[j - i]);
			dp[j] = Math.max(dp[j], val);
		}
	}

	return dp[n];
}

```