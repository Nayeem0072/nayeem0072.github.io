---
title: "Coding Solutions (Java): LeetCode 200 - Number of Islands"
date: 2024-01-01T23:30:00+06:00
description: "In-depth discussion about approaches to different solutions of LeetCode 200"
tags: [code, problem solving, leetcode]
---
[LeetCode 200](https://leetcode.com/problems/number-of-islands/)

Difficulty: **Medium**

This is a very fundamental problem related to graph traversals. We are using `BFS` traversing method to determine if a land `grid[r][c] == '1'` is a part of an island and mark all the connected lands in `visited`.

Helpful [youtube link](https://www.youtube.com/watch?v=pV2kpPD66nE) if you want to get a detailed explanation of this problem.

Time and space complexity: `O(rows * cols)`

```java
class Solution {
    Integer rows = null;
	Integer cols = null;
	int[][] directions = new int[][] {{1, 0}, {-1, 0}, {0, 1}, {0, -1}};
	
	public int numIslands(char[][] grid) {
		rows = grid.length;
		cols = grid[0].length;
		
		boolean[][] visited = new boolean[rows][cols];
		int noOfIslands = 0;
		
		for(int r = 0; r < rows; r++) {
			for(int c = 0; c < cols; c++) {
				if(grid[r][c] == '1' && visited[r][c] != true) {  // grid search every piece of land
					bfs(r, c, grid, visited);
					noOfIslands++;
				}
			}
		}			
		
		return noOfIslands;
    }
    
	// bfs traversal to mark all the connected lands of an island
	private void bfs(int r, int c, char[][] grid, boolean[][] visited) {
		Queue<Integer[]> q = new LinkedList<>();
		q.add(new Integer[] {r, c});		
		visited[r][c] = true;
		
		while(!q.isEmpty()) {
			Integer[] points = q.poll();
			for(int[] dir: directions) {
				r = points[0] + dir[0];
				c = points[1] + dir[1];
				
				if(r >= 0 && r < rows 
					&& c >= 0 && c < cols 
					&& !visited[r][c]
					&& grid[r][c] == '1') {	
                    
					q.add(new Integer[] {r, c});		
					visited[r][c] = true;					
				}
			}		
		}
	}
}

```
