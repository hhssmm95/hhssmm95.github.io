---
layout: post
title: DX12 Chapter 0 - 테스트 프로젝트 세팅
categories: [Graphics, DirectX12]
tags: [DX12]
date: 2025-01-14 21:44 +0900
---


해당 포스트는 인프런 Rookiss 지식공유자님의 강의를 듣고 필기했던 기록에 살을 붙여 정리한 게시물입니다.
[[C++과 언리얼로 만드는 MMORPG 게임 개발 시리즈] Part2: 게임 수학과 DirectX12](https://inf.run/pdSD "강의 보러가기")<br><br><br><br><br>






## **프로젝트 세팅**
vs에서 새 Windows 애플리케이션 생성을 통해 새로운 프로젝트를 생성한다.<br><br><br><br><br>





## **Pre-Compiled Header**
사전 컴파일된 헤더 파일

프로젝트 세팅에서 사전 컴파일된 헤더 파일 사용 옵션을 활성화 하고 헤더 하나를 생성하여 자주 사용하는 코드와 include 선언 등을 해당 파일에 몰아서 작성해 놓은 후 PCH 옵션 사용을 활성화해주면 자동으로 해당 헤더가 추가되므로 편리하게 사용할 수 있을 뿐더러, 이름대로 사전에 이미 컴파일된 코드이기 때문에 성능적으로도 효율적이다.<br><br><br><br><br>





## **테스트 프로젝트의 기본적인 루틴**
```cpp
class Game
{
public:
    Init();
    Update();
}
```
```cpp
//client.cpp (메인 진입점 소스 코드)
MSG msg;

//위에서 작성한 Game 객체 생성 및 초기화
unique_ptr<Game> game = make_unique<Game>();
game->Init();

// 기본적인 루프 (게임의 메인 업데이트 루프)
while (true)
{
    if (PeekMessage(&msg, nullptr, 0, 0, PM_REMOVE))
    {
        if(msg.message == WM_QUIT)
            break;

        if (!TranslateAccelerator(msg.hwnd, hAccelTable, &msg))
        {
            TranslateMessage(&msg);
            DispatchMessage(&msg);
        }
    }

    //게임의 메인 업데이트 동작
    game->Update();
}
```
- 윈도우즈 애플리케이션의 진입점 소스인 client.cpp에서 앞으로 작성할 메인 로직 클래스인 Game의 객체를 생성
- client.cpp의 메인 루프에서 Game 객체의 Update를 호출하도록 하여 게임에서 프레임마다 갱신되는 기본적인 Update 구조 완성<br><br><br><br><br>





## **라이브러리 파일 작성**

- 정적 라이브러리
    - 애플리케이션이 빌드될 때 함께 포함되어 빌드 후 분리할 수 없는 라이브러리 파일
    - 확장자는 .lib

- 동적 라이브러리
    - 프로젝트에 종속되지 않으므로 빌드 전 후 관계 없이 이동시킬 수 있으며 애플리케이션이 실행될 때 링크하여 사용되는 라이브러리 파일
    - 확장자는 .dll<br><br><br><br><br>





## **출력 파일 (빌드 결과 파일) 생성 위치 설정**
프로젝트 속성 -> 일반 -> 출력 디렉터리 지정
- 상대 경로로 작성 권장
    - $(SolutionDir)폴더이름\폴더이름\
- 프로젝트 속성 상단 요소
    - 구성: Debug, Release중 어떤 구성에 현재 설정이 적용되게 할 것인지
    - 플랫폼: x86, x64 중 어떤 플랫폼에 현재 설정이 적용되게 할 것인지<br><br><br><br><br>





## **다른 프로젝트의 파일을 포함시키는 방법**
작성 중인 프로젝트의 속성 -> VC++ 디렉터리 -> 일반
- 포함 디렉터리
    - 포함 시킬 헤더 파일의 경로 (타 프로젝트의 헤더 파일 경로 지정)
- 라이브러리 디렉터리
    - 포함 시킬 라이브러리 (dll, lib) 파일의 경로 ( 타 프로젝트의 라이브러리 파일 경로 지정)<br><br><br><br><br>





## **라이브러리 파일 종속 선언**
라이브러리 파일을 포함 시켰으나 해당 라이브러리 파일에 대한 종속 선언이 없을 경우, 프로젝트 빌드 시 링크 에러가 발생한다.

 
라이브러리 종속 선언 방법

- 방법1
    - 프로젝트 속성 -> 링커 -> 입력 -> 추가 종속성
    - 종속 선언할 라이브러리 파일의 상대 경로 입력
- 방법2
    - `#pragma comment(lib, "library.lib")` 전처리문 사용
        - 추가 종속성에 해당 라이브러리 파일을 추가하도록 링커에게 명시적으로 지시하는 전처리문
    - 헤더에 위 전처리문 작성