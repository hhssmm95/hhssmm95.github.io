---
layout: post
title: 알고리즘 문제풀이 Study - 순열
date: 2025-01-21 21:55 +0900
categories: [AlgorithmStudy]
tags: [Algorithm]
---

# 순열

 

집합에서 원소를 뽑을 때,

**순서가 있도록 뽑는다면 -> 순열**

**순서와 상관없이 뽑는다면 -> 조합**

 

## 예시

> **집합 {1, 2, 3} 에서 원소를 순서 상관 없이 뽑는 경우**

- 답: {1,2,3}
  - 조합에 대한 문제이며, '순서 상관 없이' 라는 명제가 있으므로 {1,2,3} = {3,2,1}이며 하나의 경우의 수 밖에 없음

> **집합 {1,2,3} 에서 원소의 순서를 고려하여 뽑는 경우**

- 답: {1,2,3}, {2,1,3}, {1,3,2}, {2,3,1}, {3,1,2}, {3,2,1}

  - 순열에 대한 문제이며, '순서를 고려' 해야하는 상황이므로 {1,2,3} != {3,2,1} 이며, 총 6개의 경우의 수가 있음

     

    

## C++에서의 순열

c++에서는 `std::next_permutation()`함수를 통해 오름차순 순열을 구할 수 있다.

호출 문법은`next_permutation(first, last)` 형식이며, first에는 순차 컨테이너 (Sequence Container)에서 순열을 뽑아낼 첫 번째 항의 iterator를, last에는 순열을 뽑아낼 마지막 항의 다음 번 iterator를 인자로 넘겨준다.

STL 시퀀스 컨테이너가 아닌 일반 배열도 사용 가능하다.

 

**사용 예시**

```cpp
int a[] = {1,2,3};

//일반적으로 do-while 문과 함께 사용하는 경우가 많음
do
{
    for(int i:a)
        cout << i << " ";

    cout<<endl;
}while(next_permutation(a, a+3));
//same grammar
//while(next_permutation(0,3));
//while(next_permutation(&a[0], &a[0] + 3));

//vector case
//while(next_permutation(vec.begin(), vec.end());
```

 

내림차순 함수도 존재한다.

```
prev_permutation(first, last)
```

 



## 주의사항

`next_permutation()`을 통해 순열을 뽑는 경우, 오름차순으로 순열을 뽑아내기 때문에 기존 컨테이너가 정렬 되어있지 않은 경우 모든 순열을 뽑아내지 못하고 중간 부터 뽑아내게 된다.

```
vector<int> vec = {2, 1, 3};

do
{
    for(i : vec)
         cout << i << " ";
    cout<<endl;
}while(next_permutation(vec.begin(), vec.end());

/* 출력 결과
* 2 1 3
* 2 3 1
* 3 1 2
* 3 2 1
*/
```

`{2,1,3}` 의 경우 `{1}`이 가장 앞에 있을 때의 순열을 스킵한 채로 `{2}`가 가장 앞에 있을 때의 순열부터 시작해버린다.

따라서 `std::sort()` 등을 통해 `std::next_permutation()`을 호출하기 전에 반드시 컨테이너를 정렬 해주어야 한다.



## 순열 공식

 

$~n~P~r~ = \frac{n!}{(n-r)!}$

**n개중 r개를 뽑는 경우의 수 공식**

 

ex) 3개중 2개를 뽑는 경우의 수

$~3~P~2~ = \frac{3!}{(3-2)!}$ 

$ = 3!$ 

$ = 3*2*1$ 

$ = 6$ 

- 답: 6개

 

## 순열 문제 해결 팁

순서와 상관 있게 모든 경우를 구하는 문제 -> 순열

순서 상관 없이 모든 경우를 구하는 문제 -> 조합

> 순서를 재배치하여 ~하라

> ~한 순서의 경우 max 값을 ~~하라

모두 순열 문제일 가능성 높음
