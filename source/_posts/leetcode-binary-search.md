---
ctitle: 二分搜索模板
date: 2020-04-19 18:44:31
tags: 
	- leetcode
	- course-note
typora-root-url: ../../source
---

# 模板

## 基础款

左闭右开 [l, r)

```c++
int binary_search(int l, int r)
{
    int m = 0;
  	while (l < r)
    {
        m = l + ((r - l) >> 1);
        if (f(m)) return m; // optional
        if (g(m)) {
            r = m;
        } else {
          	l = m + 1;
        }
    }
    return l; // not found return -1
}
```

例：A=[1,2,5,7,8,12], search(8) = 4, search(6) = -1

```c++
int binary_search(vector<int> A, int val, int l, int r)
{
    int m = 0;
  	while (l < r)
    {
        m = l + ((r - l) >> 1);
        if (A[m] == val) return m;
        if (A[m] > val) {
            r = m;
        } else {
        		l = m + 1;
        }
    }
    return -1;
}
```

## 上下界

给出上下界的模板之前先看一个例子：

A=[1,2,2,2,4,4,5]

Lower_bound(A,2) = 1

Upper_bound(A,2) = 4

Lower_bound(A,3) = 4

Upper_bound(A,5) = 7

### 下界

```c++
int lower_bound(vector<int> A, int val, int l, int r)
{
    int m = 0;
  	while (l < r)
    {
        m = l + ((r - l) >> 1);
        if (A[m] >= val) {
            r = m;
        } else {
        		l = m + 1;
        }
    }
    return l;
}
```



### 上界

```c++
int upper_bound(vector<int> A, int val, int l, int r)
{
    int m = 0;
  	while (l < r)
    {
        m = l + ((r - l) >> 1);
        if (A[m] > val) {
            r = m;
        } else {
        		l = m + 1;
        }
    }
    return l;
}
```

上下界的区别主要是A[m] == val的处理

# 常见题目

https://zxi.mytechroad.com/blog/category/algorithms/binary-search/