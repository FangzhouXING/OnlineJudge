---
layout: post
title:  "1255. Maximum Score Words Formed by Letters"
date:   2020-12-30 21:04:00 -0700
categories: Leetcode Solution
---

<span style="color:red">Hard</span>

Brute Force

Problem size is small enough to use Brute Force, and I haven't found a good way to optimize.

Two choices for each word, take or leave. Using this, we can build a dfs search.

```go
func maxScoreWords(words []string, letters []byte, scoreTable []int) int {
    left := [26]int{}
    for _, b := range letters {
        left[b-byte('a')]++
    }

    
    processWord := func(w string) (need [26]int, s int) {
        for _, r := range w {
            c := byte(r)-byte('a')
            need[c]++
            s += scoreTable[c]
        }
        return 
    }
    
    larger := func(a, b [26]int) bool {
        for i:=0; i < len(a); i++ {
            if a[i] < b[i] {
                return false
            }
        }
        return true
    }
    
    var bruteForce func(int) int
    bruteForce = func(cur int) int {
        if cur >= len(words) {
            return 0
        }
        need, score := processWord(words[cur])
        take := 0
        if larger(left, need) {
            for i, v := range left {
                left[i] = v - need[i]
            }
            
            take = bruteForce(cur+1) + score
            
            for i, v := range left {
                left[i] = v + need[i]
            }
        }
        notTake := bruteForce(cur+1)
        if take > notTake {
            return take
        }
        return notTake
    }
    
    
    return bruteForce(0)
}
```