---
title: OpenVR 개발기 Day 01
tags: ["vr", "cmake", "clion"]
categories: vr
date: 2021-07-19
---

# 발단

나는 2018년 초에 Beat Saber라는 VR 게임의 영상을 보고 확 꽂혀서 HTC Vive를 질렀다.
VR 기기를 지르고 비트세이버로 약 100시간 이상을 하며 기기 산 값은 충분히 한 것 같지만,
모름지기 개발자라면 이 힙스터 기기로 개발을 해 봐야하지 않겠는가.

...라는 생각만 몇 년째 하고 있었다. 그러다 최근 주변에서 다시 VR 얘기를 들어서,
그리고 한동안 데탑 GPU가 부족해서 비트세이버도 못 하고 있었지만 새 PC를 산 김에
본격적으로 VR 개발을 시작해보고자 한다.

VR 개발에는 여러 방법을 쓸 수 있을 것이고, 사실 그 중에선 Unity나 Unreal Engine을 기반으로
OpenVR 플러그인을 사용하는 것이 가장 시작하기 쉬울 것이다.
이 엔진들은 웬만한 프로젝트 기초를 모두 제공하며, 특히 물리엔진의 기능이 필요하다던가 하면
가장 효율적인 선택지일 것이다.

하지만 나는 고민 끝에 엔진을 사용하지 않고 개발해보기로 했다. 이유는 몇 가지가 있는데

1. 프로젝트 특성
  - 내가 만들고자 하는 아이템은 일종의 3D 뷰어라서, 물리엔진 기능 같은 건 필요없는 반면
    2차원 이미지 배열에서 3차원 볼륨을 만드는 등 이미지 처리 등 엔진과 무관한 기능이 많이 필요하다
  - 내가 유니티나 언리얼 엔진의 기능을 잘 모르기에 확신은 없지만, 대략 검색해봤을 때
    내가 원하는 작업을 할 수 있는 깔끔한 방법을 찾지 못했다
  - 아예 전업으로 할 거라면 엔진에 대한 공부도 해 가면서 개발하는 게 좋을 테지만, 어디까지나 취미개발이고
    개발 자유도 등의 측면에서도 엔진에 의존하지 않는 것이 좋을 것 같다는 생각이 들었다
  - 만약 이게 잘 되서 회사에도 써먹게 된다면 최적화가 필요한데 엔진을 끼면 어려울 것 같았다.
    물론 이건 어디까지나 가능성이고 미래의 일이므로 비중을 크게 두진 않았다
2. 취향
  - 회사일이 아닌 취미개발에 대해서는 무언가 처음 배울 때 바닥부터 해 보면서 하나씩 배워나가는
    걸 좋아하는 편이다
  - 개발 과정에 GUI가 안 들어가는 걸 선호하는 편이다 (꼰대)

기술스택에 대한 결정을 내렸으니, 이제 개발환경 세팅을 시작하자.

# OpenVR Setup

[OpenVR SDK](https://github.com/ValveSoftware/openvr)는 C++로 되어있다.
레포에 보면 몇 가지 샘플이 있는데, 그 중 `hellovr_opengl`은 OpenGL 라이브러리를 렌더링에 사용한다.
회사에서도 WebGL을 사용하고 있기 때문에 친숙한 OpenGL 기반으로 선택하기로 결정했다.

그런데 헬로월드에 해당하는 예제일텐데 LoC가 2k 남짓이다. 음...\
코드 내용은 나중에 생각하기로 하고, 파일 실행을 먼저 해 보자.

공식 README에 Unix와 Windows 빌드 커맨드가 있긴 한데, 아무리 Qt나 Visual Studio 버전을 맞춰도 도무지 되지 않았다.
한참 검색해보며 씨름한 결과, cmake 커맨드로 빌드를 하는 대신 **그냥 Visual Studio로 열어서 빌드**를 하면 되더라.
허무한 결말이지만 이걸 하려고 토요일 하루종일 씨름했다.

# Custom Build

하지만 나는 Visual Studio (혹은 VS Code) 대신 JetBrain 제품을 애용하기 때문에 이게 별로 마음에 들지 않았다.

> 어차피 C++ 라이브러리들만 연결해주면 되는데, 설정으로 MSVC만 쓰게 하고 CLion으로 개발할 수 있다면 더 좋지 않을까?

라는 생각으로 프로젝트 세팅을 시작했다.

목표는 `hellovr_opengl`과 동일한 프로그램을 CLion + CMake로 개발하는 환경을 만드는 것이다.
OpenVR 레포를 대부분 베껴오면 어렵지 않게 할 수 있을테지만, 이 레포의 샘플도 관리가 잘 안 되는 것 같고
(README가 실패하는 시점에서 신뢰도는 매우 낮다) 간만에 CMake 설정도 손에 익힐 겸 디펜던시 하나씩 연결해보기로 했다.

## SDL2

[SDL](https://www.libsdl.org/)은 크로스 플랫폼으로 오디오, 키보드, 마우스, 컨트롤러, 그래픽 장치 등 다양한 입력을
처리할 수 있는 라이브러리이다. 그래픽으로는 OpenGL과 Direct 3D를 지원한다.
`hellovr_opengl`에서는 Companion window (VR 화면 말고 PC 화면에 뜨는 보조 스크린)의 생성과 그곳에서 발생하는
키보드 입력에 대한 처리에 사용된다. 또한 VR 화면으로 넘겨줄 텍스쳐 연산에 필요한 GL context 생성도 맡는다.

SDL 자체는 필수 라이브러리가 아닌 보조도구 느낌이다. `hellovr_opengl`에서 SDL을 제거한다면,
일단 companion window 없이 VR로만 화면을 내보내고 직접 GL context를 생성해주는 식으로 수정할 수 있을 것이다.

[다운로드 페이지](https://www.libsdl.org/download-2.0.php)에서 VC++ 개발 라이브러리를 받아서 프로젝트에 추가해준 뒤
CMake 설정을 다음과 같이 해줬다.

```
cmake_minimum_required(VERSION 3.19)
project(3dviewer)

set(CMAKE_CXX_STANDARD 14)

set(SDL2_DIR "external/SDL2-2.0.14")
find_package(SDL2 REQUIRED)
include_directories(${SDL2_INCLUDE_DIRS})

add_executable(3dviewer src/main.cpp)
target_link_libraries(3dviewer ${SDL2_LIBRARIES})
```

근데 이것만 해서는 안 된다. CMake FindSDL2 모듈이 SDL 폴더 안에 `sdl2-config.cmake`를 만들어달라길래 아래와 같이 넣어줬다.

```
set(SDL2_INCLUDE_DIRS "${CMAKE_CURRENT_LIST_DIR}/include")

# Support both 32 and 64 bit builds
if (${CMAKE_SIZEOF_VOID_P} MATCHES 8)
    set(SDL2_LIBRARIES "${CMAKE_CURRENT_LIST_DIR}/lib/x64/SDL2.lib;${CMAKE_CURRENT_LIST_DIR}/lib/x64/SDL2main.lib")
else ()
    set(SDL2_LIBRARIES "${CMAKE_CURRENT_LIST_DIR}/lib/x86/SDL2.lib;${CMAKE_CURRENT_LIST_DIR}/lib/x86/SDL2main.lib")
endif ()

string(STRIP "${SDL2_LIBRARIES}" SDL2_LIBRARIES)
```

### Troubleshooting

겪은 시행착오 몇 가지를 같이 기록한다.

1. `Process finished with exit code -1073741515 (0xC0000135)`

이건 찾아보면 dll을 못 찾아서 생기는 에러다. CLion에서 실행하는 경우 `cmake-build-debug` (혹은 `cmake-build-release`) 폴더 안에 빌드가 되는데
이 때 dll을 복사해주도록 설정하거나, 직접 이 위치에 `SDL2.dll`을 복사해넣으면 실행이 된다.

2. `Process finished with exit code -1073741701 (0xC000007B)`

1번과 똑같은 에러가 아닌가 했지만 코드가 다르다. 이건 invalid image format 에러라는데, dll의 32bit/64bit가 잘못된 경우였다.
내 경우 CLion의 toolchain 설정에서 기본이 x86으로 되어있었는데, 당연히 64bit인줄 알고 64bit dll을 복사해넣었더니 발생했다.

# Next Step

디펜던시 하나 성공했으니, 이제 나머지 라이브러리들을 연결해서 기본 동작을 테스트해 볼 계획이다.
GLEW와 GLU는 똑같이 쓰지만 `hellovr_opengl`과는 다르게 `Matrices.h` 대신 GLM을 쓸 생각이고
VR 없이 일반 환경에서 OpenGL 동작이 잘 되는 걸 확인한 다음에 OpenVR을 붙일 것이다.
