---
layout: post
title: 게임 서버 기초 1.2 - 쓰레드 생성
date: 2025-01-20 19:37 +0900
categories: [GameServer]
tags: [GameServer]
---

# 쓰레드 생성

```cpp
//C++11에서 추가된 thread 기능을 담은 헤더
#include <thread>

void HelloThread()
{
    std::cout << "Hello Thread!" << std::endl;
}

void PrintNumber(int n)
{
    std::cout << n << std::endl;
}

int main()
{
    // 기본형, 첫 번째 패러미터는 참조 전달로 함수를 받음
    std::thread t1(HelloThread);


    /*
     * 인자와 함께 전달하는 형태
     * 두 번째 패러미터는 템플릿 패러미터 팩으로
     * 작성 되어 있으므로 아무거나, 원하는 수 만큼
     * 넘길 수 있음
*/ 
    std::thread t2(PrintNumber, 11);


    /*
     * 굳이 std::thread 객체의 생성과 동시에 실제 스레드를 할당할 필요는 없음
     * 객체 생성과 스레드 할당 타이밍 분리 가능
     */
    std::thread t3;
    t3 = std::thread(HelloThread);
}
```

- 스레드 생성

  - `#include <thread>` 헤더에 포함된 std::thread 객체를 생성하고 해당 객체의 생성자 혹은 대입 연산자를 통해 실제 스레드를 할당할 수 있음

  - 모던 C++ 이전 방법으로는 `#include <windows.h>` 에 포함된 CreateThread 함수를 사용할 수 있으나 해당 방법은 다른 OS환경에서의 호환성 문제 및 멀티스레드 사용 문제로 인해 현재는 잘 사용되지 않음

     

  - 스레드 객체의 선언 타이밍과 실제 할당 타이밍은 다르게 할 수 있으며 vector같은데 담아놓고 한번에 할당하는 등에 활용 가능



## std::thread의 주요 함수들

```cpp
#include <thread>

void HelloThread()
{
    std::cout << "Hello Thread!" << std::endl;
}

int main()
{
    std::thread t(HelloThread);

    // 스레드를 제어할 수 있는 CPU의 실제 코어 개수를 반환 (항상 정확하지는 않음)
    int32 core_count = t.hardware_concurrency();

    // 스레드 마다 별개의 ID가 존재하며, 이를 반환해줌
    auto id = t.get_id();

    /*
    * std::thread 객체로부터 실제 스레드를 분리해줌
    * detach 이후에는 분리된 기존 std::thread 객체를 통해서 실제 스레드에 엑세스 하는 것이 불가능함
    */
    //t.detach();


    //현재 연결된 실제 스레드가 유효한지 여부 반환 (실제 스레드의 id가 0인지 체크)
    if(t.joinable())
    {
        //해당 스레드가 작업을 마치고 종료될 떄까지 대기
        t.join();
    }
}
```

- `hardware_concurrency()`
  - 스레드를 제어할 수 있는 CPU의 실제 코어 개수를 반환 (0을 반환하기도 하는 등, 항상 정확하지는 않음)
- `get_id()`
  - 스레드 마다 별개의 ID가 존재하며, 이를 반환해줌
- `detach()`
  - std::thread 객체로부터 실제 스레드를 분리해줌
  - detach 이후에는 분리된 기존 std::thread 객체를 통해서 실제 스레드에 엑세스 하는 것이 불가능함
- `joinable()`
  - 현재 연결된 실제 스레드가 유효한지 여부 반환
  - 실제 스레드의 id가 0인지 체크하는 방식
- `join()`
  - 해당 스레드가 작업을 마치고 종료될 때까지 대기

 

## 스레드 순서 동기화의 필요성

```cpp
#include <thread>
#include <vector>

void HelloThread()
{
	cout << "Hello Thread" << endl;
}

void PrintNumber(int n)
{
	cout << n << endl;
}

int main()
{
	vector<std::thread> v;

        // 자기 자신의 순번을 출력하는 스레드 10개 할당
	for (int32 i = 0; i < 10; i++)
	{
		v.push_back(std::thread(PrintNumber, i));
	}

	for (int32 i = 0; i < 10; i++)
	{
		if (v[i].joinable())
			v[i].join();
	}

	return 0;
}
```

출력결과

![image.png](https://cdn.inflearn.com/public/files/posts/4464b262-d1ca-426e-8ece-e166e4938454/8972d4bf-b959-470e-8f08-0da230f04453.png)

위 출력 결과는 매 실행마다 바뀌며 숫자 뒤에 줄 바꿈 문자까지 포함 시켰음에도 줄 바꿈 타이밍까지 랜덤한 것을 알 수 있다.

 

- 실제로는 스레드 스케줄링에 따라 출력 결과가 바뀌기 때문에 스레드의 동기화가 필요한 것을 알 수 있다.
- 단순 산술 연산이 아닌 std::cout과 같은 기능들은 OS의 커널 요청을 거쳐야 하기 때문에 상대적으로 무거운 기능이며, 출력 중에 소유권을 다른 스레드에 빼앗길 수 있는 것이다.
