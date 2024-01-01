---
title: "Coding Solutions (Java): LeetCode 1254 - Number of Closed Islands"
date: 2024-01-01T23:35:00+06:00
description: "In-depth discussion about approaches to different solutions of LeetCode 1254"
tags: [code, problem solving, leetcode]
---
[LeetCode 1254](https://leetcode.com/problems/number-of-closed-islands/)

Difficulty: **Medium**

This problem is a derivation of [LC200](https://leetcode.com/problems/number-of-islands/).

We are using a bfs traversing method to determine if a land `grid[r][c]` == 0 is a part of a closed island and mark all the connected lands in `visited`.

Time and space complexity: `O(rows * cols)`

```java
class Solution {
    // same as lc 200
    Integer rows = null;
	Integer cols = null;
	int[][] directions = new int[][] {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
    
    public int closedIsland(int[][] grid) {
        rows = grid.length;
        cols = grid[0].length;
        boolean[][] visited = new boolean[rows][cols];
        int noOfIslands = 0;
        for(int r = 1; r < rows - 1; r++) {
            for(int c = 1; c < cols - 1; c++) {
                if(!visited[r][c] && grid[r][c] == 0) {
                    if(bfs(r, c, grid , visited)) // grid search for checking if this land is part of a closed island
                        noOfIslands++;
                }
            }
        }
        
        return noOfIslands;
    }
    
    // using bfs traversal to check if a land is part of a closed island
    private boolean bfs(int r, int c, int[][] grid, boolean[][] visited) {
        boolean isIsland = true;
        Queue<Integer[]> q = new LinkedList<>();
		q.add(new Integer[] {r, c});		
		visited[r][c] = true;
        
        while(!q.isEmpty()) {
            Integer[] points = q.poll();
            for(int[] dir: directions) {
                r = points[0] + dir[0];
                c = points[1] + dir[1];
                
                if (r >= 0 && r < rows && c >= 0 && c < cols) {
                    if ((r == 0 || r == rows - 1 || c == 0 || c == cols - 1) && grid[r][c] == 0 && !visited[r][c]) {
                        isIsland = false; // even if we know at this point this is not a closed island, we 
                                          // still search all the connected lands to traverse the whole island
                                          // so the connected lands dont appear in the next grid search
                    }
                    if (grid[r][c] == 0 && !visited[r][c]) {
                        q.add(new Integer[] {r, c});		
					    visited[r][c] = true;
                    }
                }
            }
        }
        
        return isIsland;
    }
    
}

```
