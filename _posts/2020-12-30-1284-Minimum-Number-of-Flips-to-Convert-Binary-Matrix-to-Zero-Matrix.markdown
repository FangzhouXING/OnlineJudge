---
layout: post
title:  "1284. Minimum Number of Flips to Convert Binary Matrix to Zero Matrix"
date:   2020-12-30 18:13:49 -0700
categories: Leetcode Solution
---


<span style="color:red">Hard</span>

A searching problem. 

When using go, there is a small issue: map does not accept slice as key type. As a workaround, I have to use a hash/serialize function to track visited states. 

Once we have this hash function, it's easy to do either BFS or DFS. I chose DFS here.

```go
func minFlips(mat [][]int) int {
    //brute force solution
    
    // BFS, at each step, there are (n+m) choices. Store visited matrix.
    
    N, M := len(mat), len(mat[0])
    
    isZeroMatrix := func(mat [][]int) bool {
        for _, row := range mat {
            for _, v := range row {
                if v != 0 {
                    return false
                }
            }
        }
        return true
    }
    
    flipCell := func(x, y int) {
        mat[x][y] = 1-mat[x][y]
        if x > 0 {
            mat[x-1][y] = 1-mat[x-1][y]
        }
        if y > 0 {
            mat[x][y-1] = 1-mat[x][y-1]
        }
        if x+1 < len(mat) {
            mat[x+1][y] = 1-mat[x+1][y]
        }
        if y+1 < len(mat[x]) {
            mat[x][y+1] = 1-mat[x][y+1]
        }
    }
    
    visited := make(map[int]int)
    
    hash := func(mat [][]int) int {
        h := 0
        for i:=0; i < len(mat); i++ {
            for j:=0; j < len(mat[i]); j++ {
                h = h * 2 + mat[i][j]
            }
        }
        return h
    }

    INTMAX := 10000000
    
    minStepCnt := INTMAX // large number
    
    var dfs func(int)
    dfs = func(dist int) {
        if dist >= minStepCnt {
            return
        }
        
        if isZeroMatrix(mat) {
            //fmt.Println("Found a solution")
            if minStepCnt > dist {
                minStepCnt = dist
            }
            return
        }
        
        for i:=0; i < N; i++ {
            for j:=0; j < M; j++ {
                flipCell(i, j)
                
                curHash := hash(mat)
                
                if visited[curHash] == 0 || visited[curHash] > dist+1 {
                    visited[hash(mat)] = dist 
                    dfs(dist+1)
                }
                flipCell(i, j)
            }
        }
    }
    
    dfs(0)
    if minStepCnt >= INTMAX {
        return -1
    }
    return minStepCnt
}
```