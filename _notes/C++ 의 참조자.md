---
title: C++ 의 참조자
date: 2023-05-18
tags:
  - Reference
  - Cplusplus
  - subpage
---

# C++ 의 참조자

```cpp
//참조자 예시

#include <iostream>

int main() {
	int a = 3;
	int& another_a = a; 
	
	another_a = 5; 
	std::cout << "a : " << a << std::endl; 
	// print 5
	std::cout << "another_a : " << another_a << std::endl;
	// print 5 again
	return 0;
}
```

- `int& another_a` : 참조자 `another_a` 정의
	- `double% {variable}` : double 형 참조자
	- `int*& {variable}` : int 포인터의 참조자

- 레퍼런스는 반드시 처음에 누구의 별명이 될 것인지 지정해야 함.
	- `int& another_a = a;` 이런 식으로 가르키는 대상이 있어야 함.

- 레퍼런스가 한 번 별명이 되면 절대로 다른 이의 별명이 될 수 없음.

- 레퍼런스는 메모리 상에 존재하지 않을 수 있음.
	- 일반적인 경우 존재하지 않음.
		- Compiler 단계에서 reference는 참조하는 변수의 별명이니깐, 그냥 변수 이름으로 바꿔주면 됨.
	- 함수 인자로 레퍼런스 받는 경우
		- 함수 인자로 포인터를 전달하는 경우와 마찬가지로, 원래 변수를 copy 하지 않고 그 자레를 사용함. 따라서 변수값을 함수에서 바꿀 수 도 있음.
		- [Does 'passing by reference' allocate memory? from Reddit](https://www.reddit.com/r/cpp_questions/comments/sbdam8/does_passing_by_reference_allocate_memory/)
			- Q. wouldn't passing a pointer reference just do the same thing?
			- A . Yes, ==passing a reference== and ==passing a pointer== are generally going to do the same thing. However, references are not required to be implemented in terms of pointers, so the optimizer has more flexibility to use keep values in registers instead of allocating on the stack.
		- eg. `std::cin >> {variable}`
			- {variable}의 reference를 전달함
			- Copy 도 필요없고, C에서 하는 것 처럼 pointer를 따로 만들어서 전달하지 않아도 되고.

```cpp
// Call by reference 예시

#include <iostream>

int change_val(int &p) {
	p = 3;
	return 0;
}
	int main() { 
	int number = 5; 
	std::cout << number << std::endl; // print 5
	change_val(number); 
	std::cout << number << std::endl; // print 3
}
```

- Reference의 reference를 만들면?
	- eg. `int x = 1; int& y = x; int& z = y;` 
	- y는 x의 reference
	- z는 y의 reference 니깐 x의 reference!

- 상수에 대한 참조자?
	- eg. `int& ref = 4;`
	- 금지!
	- 상수 값 자체는 리터럴, 리터럴은 레퍼런스 금지

---

# 레퍼런스의 배열?

- `int a, b; int& arr[2] = {a, b};`
- 컴파일 오류 남
- 레퍼런스의 레퍼런스, 레퍼런스의 배열 (원소가 레퍼런스 타입인 배열) 은 금지 되있음. 
	- 레퍼런스는 메모리 상에 없어서, `*arr` 혹은 `*(arr+1)` 방식으로 원소에 접근할 수가 없음. 따라서 레퍼런스의 배열을 만든다는 거 자체가 말이 안됨

---

# 배열의 레퍼런스?

- `int arr[3] = {1, 2, 3}; int(&ref)[3] = arr;`
- `ref` 는 `arr` 의 레퍼런스
- 따라서, `ref[2]`는 `arr[2]`의 레퍼런스
- 포인터와 다르게 배열의 레퍼런스를 선언할 때 반드시 **배열의 크기 명시** 해야함.
- 2차원 배열?
- `int arr[3][2] = {1, 2, 3, 4, 5, 6}; int(&ref)[3][2] = arr;`

---

# 레퍼런스를 리턴하는 함수?

- 함수에서, 지역변수를 리턴하면 그 변수를 copy 하면서 리턴한다.
	- 그리고 함수 안에서 정의된 지역변수는 사라짐. (안 없어지면 memory leak!)
- `int& functioin()` 으로 레퍼런스 리턴을 하면, 그 변수의 레퍼런스가 전달되서 copy를 안해도 됨.
- 근데 지역변수의 레퍼런스를 리턴으로 하면, 함수 종료 뒤 그 변수가 사라질거라 "없어진 변수"를 참조해서, Dangling reference 문제가 생겨 segment fault 가 나게 됨! 
- 따라서, 레퍼런스 리턴하려면 외부에서 가져온 변수의 레퍼런스 (그래서 함수 종료 뒤에도 살아남을 변수)를 리턴해야함!!

```cpp
// Local variable의 reference를 리턴해서 Dangling reference로 인해 오류가 나는 경우 

int& function_local_variable_reference() {
	int a = 2;
	return a;
}

int main() { 
	int b = function_local_variable_reference();
	return 0;
}
```


```cpp
// 인자로 받은 reference를 그대로 리턴해서 문제가 없는 경우

int& function(int& a) {
	a = 5;
	return a; 
}

int main() { 
	int b = 2;
	int c = function(b); 
	return 0; 
}
```

---

# 함수의 리턴값을 참조자로 받는 경우?

- eg. `int& c = fuction();` 
- 값을 리턴하는 함수의 리턴값은 일시적으로만 존재하고 사라지는 우측값이라서, 댕글링 레퍼런스 발생. $\rightarrow$ 컴파일 오류
- 예외!! : const 참조자는 받을 수 있다! 상수 레퍼런스는 값이 변할리 없어서 안전하니깐 우측값의 생명이 연장되는 개념.
	- eg. `const int& c = function();`

![[Pasted image 20230511182410.png]]


---

# Reference

- [씹어먹는 C++ - <2. C++ 참조자(레퍼런스)의 도입>](https://modoocode.com/141)