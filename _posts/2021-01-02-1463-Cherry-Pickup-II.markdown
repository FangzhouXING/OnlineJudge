---
layout: post
title:  "1463. Cherry Pickup II"
date:   2021-01-02 22:32:00 -0700
categories: Leetcode Solution
---

<span style="color:red">Hard</span>

3 dimensional DP. 

DP[Level][Robot1][Robot2], Level ranges from 0 to M-1, Robot1 and Robot2 means positions chosen in the current level.

To move to DP[L][R1][R2], we need to find max of nine values from previous level L-1. (R1-1, R2-1), (R1-1, R2)...(R1+1, R2+1)
Adding values in current cells, we get:

```
DP[0][0][N-1] = grid[0][0] + grid[0][N-1]
DP[L][R1][R2] = max(DP[L-1][R1+s][R2+t]) + grid[L][R1] + grid[L][R2], s,t ∈ {-1, 0, 1}
```

Two things to note here.
1. when R1 == R2, need to remove the extra scores we added.
2. Some places are not reachable, we need to exclude those. For example, DP[1][N/2][N/2] is not reachable when N >= 3. I used another array to track this.




```go
func cherryPickup(grid [][]int) int {
    // DP[layer][A][B]
    
    M, N := len(grid), len(grid[0])
    
    DP := make([][][]int, M)
    possible := make([][][]bool, M)
    
    for i:=0; i < M; i++ {
        DP[i] = make([][]int, N)
        possible[i] = make([][]bool, N)
        for j:=0; j < N; j++ {
            DP[i][j] = make([]int, N)
            possible[i][j] = make([]bool, N)
        }
    }
    DP[0][0][N-1] = grid[0][0] + grid[0][N-1]
    possible[0][0][N-1] = true
    
    if 0 == N-1 {
        DP[0][0][N-1] -= grid[0][0]
    }
    
    for i:=1; i < M; i++ {
        for j:=0; j < N; j++ {
            for k:=0; k < N; k++ {
                maxVal := 0
                reachable := false
                for s:=-1; s <= 1; s++ {
                    if j+s < 0 || j+s >= N {
                        continue
                    }
                    for t:=-1; t <= 1; t++ {
                        if k+t < 0 || k+t >= N {
                            continue
                        }
                        
                        if possible[i-1][j+s][k+t] {
                            reachable = true
                            if DP[i-1][j+s][k+t] > maxVal {
                                maxVal = DP[i-1][j+s][k+t]
                            }
                        }
                    }
                }
                if reachable {
                    DP[i][j][k] = maxVal + grid[i][j] + grid[i][k]
                    if j == k {
                        DP[i][j][k] -= grid[i][k]
                    }
                    possible[i][j][k] = true
                }
            }
        }
    }
    
    //fmt.Println(DP)
    
    maxVal := 0
    for i:=0; i < N; i++ {
        for j:=0; j < N; j++ {
            if DP[M-1][i][j] > maxVal {
                maxVal = DP[M-1][i][j]
            }
        }
    }
    return maxVal
}
```