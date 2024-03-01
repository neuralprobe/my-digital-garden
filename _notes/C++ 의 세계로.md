---
title: C++ 의 세계로
date: 2023-05-18
tags:
  - Memory
  - Cplusplus
  - subpage
---

# C++ 의 세계로

---

# 메모리 관리

- 스택 : 컴파일러에 의해 주소값이 확정된 변수들이 모인 메모리 공간
- 힙 : 프로그램 실행 시에 자유롭게 할당하고 해제할 수 있는 메모리 공간

![[Pasted image 20230511201926.png]]

- 힙 관리 함수
	- `malloc()`, `free()`: C 와 C++ 모두 쓸 수 있음
	- `new()`, `delete()` : C++ 에서만 쓸 수 있음

---

# Heap에 메모리 할당/해제하기

- `T* pointer = new T;` 
- `delete pointer;`
	- 지역 변수는 delete 해제할 수 없음

```cpp
// new로 할당
int* p = new int;

//delete로 해제
delete p;
```

---

# new로 배열 할당하기

- `{type} *{array name} = new {type name}[{array size}];`
- 지울 땐 `delete[] {array name};`

```cpp
// new로 배열 할당
int arr_size = 10;
int *list = new int[arr_size];

//delete로 해제
delete[] list;
```

---

# C++ 변수 Scope

- C와 달리 소스의 아무 곳에서나 변수를 선언 할 수 있음
- 변수는 중괄호 범위를 벗어나면 소멸 됨
- 하지만... 다른 중괄호 범위라서 같은 이름의 두 변수를 구별할 수 있더라도, 변수이름을 다르게 쓰는 것이 좋음.

```cpp
int a = 4; 
{
	std::cout << "외부의 변수 1" << a << std::endl; // 4 출력
	int a = 3;
	std::cout << "내부의 변수 " << a << std::endl; // 3 출력
} 
std::cout << "외부의 변수 2" << a << std::endl; // 4 출력
```

---

# 구조체 복습 

```cpp
// 구조체 만들기
typedef struct Animal{
	char name[30];
	int age;
} Animal;

void create_animal(Animal *animal){
	std::cin >> animal->name;
	std::cin >> anima->age;
}

int main(){
	// 구조체 배열
	Animal *list[10];
	
	//구조체 배열에 원소 할당하고 초기화
	int animal_num = 0;
	list[animal_num] = new Animal; 
	create_animal(list[animal_num]);
	
	//구조체 배열의 원소 포인터가 가르키느 구조체의 멤버 변수
	list[animal_num]->age = 1;  
}
```

---

# 구조체의 한계와 클래스의 장점

- 구조체를 가지고 어떤 일을 하려면 구조체 포인터를 인자로 받는 함수를 만들어서, 구조체 포인터의 멤버 변수에 접근하기 위한 `->` operator 를 계속 써야함
	- $\rightarrow$ 클래스에 멤버함수를 포함시켜서, 멤버 변수를 쉽게 접근
- 구조체는 `new`로 메모리 할당하고나서 초기화 함수를 직접 해줘야 함.
	- $\rightarrow$  클래스의 Constructor: `new`로 메모리 할당할 때, 자동으로 초기화 해주는 함수
- 구조체는 조금만 확장하려고 해도 코드전체를 복붙하고 수정해야함
	- $\rightarrow$ 클래스의 상속: 복붙을 안해도 구조체를 확장할 수 있게 하자
- 원래 구조체 (Animal) 파생된 다른 구조체 (Fish, Bird ... )를 위해, 포인터도 따로 만들어야 하고 (`Fish* fish, Bird* bird`), 
	- = 클래스에서는 상속된 클래스의 객체를 부모 클래스의 포인터에 담을 수 있음
- 하나의 배열에 담을 수도 없고 (`Animal *animals[10] `에 Fish, Bird 구조체 담을 수 없음 .. )
	- = 클래스에서는 상속된 클래스의 객체를 부모 클래스의 배열에 담을 수 있음
- 구조체 멤버함수를 인자로 하는 함수가 있으면 다 따로 만들어야 한다.
	- = 클래스에서 멤버함수의 오버라이드를 하면 선택적으로 함수를 수정할 수 있음.
	- + 클래스의 멤버함수를 오버로딩하면 더 다양한 함수형태를 지원할 수 있음.

---

# Char 배열 초기화하는 법

- [[C++ 배열 기초]] 에 좀 더 다양한 방법이 정리 되어있음

```cpp
// char 포인터로 문자열 초기화하기 ??
char* str1 = "hello world"; // 는 오류!! 문자열 리터럴을 "바꿀 수 있는" 변수로 선언하면 안됨
const char* str1 = "hello world"; // 상수로 선언하는건 괜찮아~

// char 배열을 문자열로 초기화하기
char str1[] = "hello world"; 
char str1[100] = "hello world"; 
// 문자열 리터럴을 가리키는 포인터가 아니라, 
// 컴파일러가 문자열 리터럴의 길이 알아내서, 
// 배열 할당하고, 문자 하나씩 복사해줌

// char 배열을 크기로 초기화하고, 문자열로 채우기
#include <cstring>
char str2[100];
std::strcpy(str2, "hello world");

// char 배열을 cin으로 채우기
#include <iostream>
char str3[100];
std::cin >> str3; 

// char 배열 출력
std::cout << str1 << str2 << std::endl;

// char 배열을 heap에 만들기
char* charArray = new char[100]; // 배열에 데이터 할당
std::strcpy(charArray, "hello world");
delete[] charArray;

```

---

# Reference

- [씹어먹는 C++ - <3. C++ 의 세계로 오신 것을 환영합니다. (new, delete)>](https://modoocode.com/169)
- [씹어먹는 C 언어 - <17. 변수의 생존 조건 및 데이터 세그먼트의 구조>](https://modoocode.com/83)
