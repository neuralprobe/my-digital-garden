---
title: C++ 시작하기
date: 2023-05-18
tags:
  - Cplusplus
  - subpage
---

# C++ 시작하기

---

# C++의 역사 

-  C++의 아버지, Bjarne Stroustrup
	- 1979년, C 언어에 클래스 라는 개념을 적립한 C with Classes 라는 언어가 C++ 의 전신
	- 1982년, 해당 언어를 조금 발전 시켜서 C++ 라고 부르기 시작
- 1998년, C++ 의 첫 번째 표준 = C++ 98 
- 2003년 표준안 = 씹어먹는 C++ 강좌 초반에 9 장 까지 내용
- C++ 11 = 2011년 표준안. 이 후 버전을 ==Modern C++== 라고 부름
- C++ 14, C++17, C++20 ...

---

# Hello World

```cpp
#include <iostream> 

int main() { 
	std::cout << "Hello, World!!" << std::endl; 
	return 0; 
}
```

- `#include` : 헤더파일 include
- `<iostream>` : 표준 입출력 라이브러리 
- `int main()` : 프로그램에서 가장 먼저 실행되는 함수
- `std::cout`
	- `std` : 표준 라이브러리의 namespace
	- `std::cout`: ostream 클래스의 cout 객체
- `<<` : ostream 클래스에 정의된 `operator<<` 멤버함수 (insertion operator)
	- 함수 선언 : `std::ostream& operator<< (int val);`
	- `(*this)`의 레퍼런스 리턴
- `std::endl`
	- 조작자 함수 (Manipulator: `<<` operator 에서 쓰이면 호출되는 함수)
	- `std::cout<<std::endl` $\rightarrow$ `std::endl(*this)` 함수 호출

---

# Name space

- 어떤 정의된 객체에 대해 어디 소속인지 지정
- 사용법:
	- `{namesapce이름}::{소속함수/변수이름}` 으로 변수/함수 호출
	- `using namespace {namesapce이름}` 으로 `{namesapce이름}::` 생략 가능 $\rightarrow$ 추천하지 않음

```cpp
namespace header1{
	int foo();
	void bar();
}

int main(){
	header1::foo();
	return 0;
}

```

---

# 이름 없는 이름공간

```cpp
namespace {
// 이 함수는 이 파일 안에서만 사용할 수 있습니다.
// 이는 마치 static int OnlyInThisFile() 과 동일합니다. 
int OnlyInThisFile() {}

// 이 변수 역시 static int x 와 동일합니다. 
int only_in_this_file = 0;
}
```

---

# C 문법 재활용하기

- 변수형 똑같음

```cpp
int main(){
	int i;
	char c;
	double d;
	float f;
	return 0;
}
```

- 포인터, 배열 똑같음

```cpp
int arr[10]; 
int *parr = arr;

int i; 
int *pi = &i;
```

- 조건문 (if, else, switch), 제어문 (for, while, break, continue 등등) 똑같음
	- 좋아진 것: 코드 중간에 변수 선언해도 좋음.

```cpp
//for-loop
for (int i=0; i < 10; i++){
	foo();
}

//while-loop
int j = 0
while (j <= 10){
	foo();
	j++;
}

//if-else
int k = ?;
if (k == 0){
	foo();
}
else {
	bar();
}

//switch
int user_input = ?;
switch (user_input) {
	case 1:	
		cout << "Psi ! " << endl; 
		break;
	case 2:
		cout << "99 살" << endl; 
		break;
	default:
		cout << "궁금한게 없군요~" << endl; 
		break;
}
```

- 입출력은 `std::cin`, `std:cout` 사용

---

# Reference

- [씹어먹는 C++ - <1. 자~ C++ 의 세계로>](https://modoocode.com/134)
- [씹어먹는 C++ - <1 - 2. 첫 C++ 프로그램 분석하기>](https://modoocode.com/136)
- [씹어먹는 C++ - <1 - 3. C++ 은 C 친구 - C 와 공통점>](https://modoocode.com/138)
