---
layout: post
title: 알고리즘 문제풀이 Study - 조합
date: 2025-01-21 22:01 +0900
categories: [AlgorithmStudy]
tags: [Algorithm]
---

# 조합

순서와 상관 없이 집합에서 그저 몇 개를 뽑을 때의 경우의 수

 



## 조합 공식

$~n~C~r~ = \frac{n!}{r!(n-r)!}$

$n$ 은 집합의 원소의 수

$r$ 은 뽑을 원소의 수

 



### 예시

> **집합 {1, 2, 3, 4, 5} 에서 3개의 원소를 뽑는 경우의 수**

 

$~5~C~3~ = \frac{5!}{3!(5-3)!}$

$= \frac{5!}{3! 2!}$

$= \frac{5*4*3*2}{3*2*2}$

$= 10$

 

## C++에서의 조합

1. 재귀함수로 작성하는 방법
2. 중첩 for문으로 작성하는 방법

r이 4 이상일 때 (4개 이상 뽑을 때)는 재귀함수로 작성하는 것이 용이하고, 그 이하일 때는 중첩 for문으로 작성하는 편이 나음, 상대적으로 중첩 for문이 더 간단하기 때문.

 

**재귀함수**

```cpp
int n = 5;
int r = 3;

void combi(int start, vector<int> b)
{
    if(b.size() == k)
    {
        //logic
        return;
    }

    for(int i = start + 1; i < n; i++)
    {
        b.push_back(i);
        combi(i, b);
        b.pop_back();
    }
}
```

 

**중첩 for문 (r == 3)**

```cpp
int n = 5;
int r = 3;

//case1 오름차순
for(int i = 0; i < n; i++){
    for(int j = i + 1; j < n; j++){
        for(int k = j + 1; k < n; k++){
            cout << i << " " << j << " " <<k << endl;
        }
    }
}

//case2 내림차순
for(int i = 0; i < n; i++){
    for(int j = 0; j < i; j++){
        for(int k = 0; k < j; k++){
            cout << i << " " << j << " " <<k << endl;
        }
    }
}
```

 

**중첩 for문 (r==2)**

```cpp
int n = 5;
int r = 2;

for(int i = 0; i < n; i++){
    for(int j = i + 1; j < n; j++){
        cout << i << " " << j << endl; 
    }
}
```

 

 
