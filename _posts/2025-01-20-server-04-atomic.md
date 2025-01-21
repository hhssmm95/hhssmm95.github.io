---
layout: post
title: 게임 서버 기초 1.3 - Atomic
date: 2025-01-20 19:46 +0900
categories: [GameServer]
tags: [GameServer]
---



## 경쟁 상태 (Race Condition)

두 스레드가 하나의 공유 메모리(전역 변수 등의 데이터/스택 영역 메모리) 에 접근하여 연산을 수행할 때, 그 결과가 일정하지 않고 각 스레드의 접근 순서에 영향을 받아 의도치 않은 상황이 발생하는 상태.

 

### 경쟁 상태 예시

```cpp
int32 sum = 0;

void Add()
{
	for (int i = 0; i < 100'0000; i++)
	{
		sum++;
	}
}

void Sub()
{
	for (int i = 0; i < 100'0000; i++)
	{
		sum--;
	}
}

int main()
{
	std::thread t1(Add);
	std::thread t2(Sub);

	t1.join();
	t2.join();
	cout << sum << endl;

	return 0;
}
```

`sum++`과 `sum--`의 연산 과정만 디스어셈블리 해보면 아래와 같음

![image.png](https://cdn.inflearn.com/public/files/posts/c523ce05-add1-4ed1-b112-9a396bb05a93/ee2b04fe-abec-4bf6-b11d-5944ebfba719.png)

`sum++`같은 경우 

1. `sum의 값을 eax 레지스터에 복사` -> 2. `eax 레지스터 값에 1을 더함` -> 3. `eax 레지스터 값을 다시 sum에 복사하여 돌려줌`

의 과정으로 수행 되는데, 여기서 1과 2의 과정을 수행한 후 모종의 이유로 컨텍스트 스위칭이 발생 하여 3까지의 과정을 마치지 못한 상태로, 스레드 t2가 1. `sum의 값을 eax 레지스터에 복사` -> 2. `eax 레지스터 값에 1을 뺌` -> 3. `eax 레지스터 값을 다시 sum에 복사하여 돌려줌` 의 과정을 모두 마쳐버린다면 다시 t1의 과정을 마치기 위해 이전에 끊겼던 과정 3부터 다시 시작 할 때 `eax 레지스터의 값을 다시 sum에 복사하여 돌려줌` 을 수행하는 순간 이 때의 eax 레지스터는 sum--연산의 수행 결과를 담고 있기 때문에 sum++의 결과가 아닌 sum--의 결과로 다시 sum을 덮어쓰는 것

 

결국 위 결과가 발생한 이유는 `sum++`이 3가지 과정으로 나누어져 있어서 중간에 컨텍스트 스위칭이 발생할 수 있기 때문이며 이를 방지할 수 있는 방법 중 하나로 `atomic`을 사용할 수 있다.

 

## Atomic

```cpp
atomic<int32> sum = 0;

void Add()
{
	for (int i = 0; i < 100'0000; i++)
	{
		sum.fetch_add(1);
		//sum++과 같음
	}
}

void Sub()
{
	for (int i = 0; i < 100'0000; i++)
	{
		sum.fetch_sub(1);
		//sum--와 같음
	}
}

int main()
{
	std::thread t1(Add);
	std::thread t2(Sub);

	t1.join();
	t2.join();
	cout << sum << endl;

	return 0;
}
```

위 결과에선 기존 의도대로 0을 출력하며 이는 atomic 템플릿 구조체가 All-Or-Nothing의 특성을 가지고 있기 때문이며 메모리를 읽고 쓰는 과정을 한번에 수행하므로 중간에 컨텍스트 스위칭에 의해 불규칙한 값이 나올일이 없다.
