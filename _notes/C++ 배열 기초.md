---
title: C++ 배열 기초
date: 2023-05-18
tags: C++ programmingLanguage array
---

# C++ 배열 기초

- ChatGPT 한테 물어본 C++ 배열의 기초!!

---

# C++에서 int 배열을 쉽게 초기화하는 법을 알려줘

C++에서 int 배열을 쉽게 초기화하는 방법에는 다양한 방법이 있습니다. 
아래에 몇 가지 일반적인 방법을 제시해드리겠습니다.

## 1. 선언과 함께 초기화

   ```cpp
   int numbers[] = {1, 2, 3, 4, 5};
   ```
  
   배열의 크기를 명시하지 않고 중괄호 `{}`를 사용하여 요소를 나열하면, 
   컴파일러가 자동으로 배열의 크기를 할당합니다.

## 2. std::array를 사용하여 초기화

   ```cpp
   #include <array>
   std::array<int, 5> numbers = {1, 2, 3, 4, 5};
   ```
   
   std::array 컨테이너를 사용하여 int 배열을 초기화할 수 있습니다. 
   배열의 크기와 함께 요소를 중괄호 `{}`로 초기화합니다.

## 3. std::vector를 사용하여 초기화

   ```cpp
   #include <vector>
   std::vector<int> numbers = {1, 2, 3, 4, 5};
   ```
   
   std::vector 컨테이너를 사용하여 int 배열을 초기화할 수도 있습니다. 
   배열의 크기를 지정하지 않고 중괄호 `{}`로 요소를 초기화합니다. 
   std::vector는 동적으로 크기가 조정되는 배열을 제공합니다.

## 4. 반복문과 범위 기반 for 루프를 사용하여 초기화

   ```cpp
   int numbers[5];
   for (int i = 0; i < 5; i++) {
       numbers[i] = i + 1;
   }
   ```
   
   배열을 선언한 후, 반복문을 사용하여 인덱스를 순회하면서 값을 할당합니다.


---

# C++ 문자열을 초기화하는 방법 알려줘

C++에서 문자열을 초기화하는 방법에는 여러 가지가 있습니다. 
아래에 몇 가지 일반적인 방법을 제시해드리겠습니다.

## 1. 문자열 리터럴을 사용하여 초기화

   ```cpp
   char* str1 = "hello world"; // 는 오류!! 문자열 리터럴을 "바꿀 수 있는" 변수로 선언하면 안됨
   const char* str1 = "Hello, World!"; // 상수로 선언하면 괜찮아
   ```
   
   문자열 리터럴을 사용하여 문자열 포인터를 초기화합니다. 
   이 방법은 읽기 전용 문자열에 적합합니다.

## 2. std::string을 사용하여 초기화

   ```cpp
   #include <string>
   std::string str2 = "Hello, World!";
   ```
   std::string 클래스를 사용하여 문자열을 초기화합니다. 
   이 방법은 문자열을 동적으로 관리하고 수정할 수 있도록 합니다.

## 3. 문자 배열을 사용하여 초기화

   ```cpp
   char str3[] = "Hello, World!"; 
   char str3_[100] = "Hello, World!";
   // 문자열 리터럴을 가리키는 포인터가 아니라, 
   // 컴파일러가 문자열 리터럴의 길이 알아내서, 
   // 배열 할당하고, 문자 하나씩 복사해줌
   ```

   문자 배열을 선언하고 문자열 리터럴로 초기화합니다. 
   이 방법은 문자열을 수정해야 할 때 유용합니다.


## 4. char 배열을 크기로 초기화하고, strcpy로 채우기

```cpp
#include <cstring>
char str2[100];
std::strcpy(str2, "hello world");
```

## 5.  char 배열을 heap에 만들기

```cpp
char* charArray = new char[100]; // 배열에 데이터 할당
std::strcpy(charArray, "hello world");
delete[] charArray;
```

## 6. std::vector\<char\>를 사용하여 초기화

```cpp
#include <vector>
std::vector<char> str4 = {'H', 'e', 'l', 'l', 'o', ',', ' ', 'W', 'o', 'r', 'l', 'd', '!'};
```

   std::vector 컨테이너를 사용하여 문자열을 초기화합니다. 
   각 문자를 요소로 하는 벡터를 생성하고, 문자열의 각 문자를 개별적으로 할당합니다.

## 7. char 배열을 cin으로 채우기

```cpp
#include <iostream>
char str3[100];
std::cin >> str3; 
```

---

# Reference

- [ChatGPT](https://chat.openai.com/)
- [씹어먹는 C++](https://modoocode.com/135)
	- [[씹어먹는 C++ 정리노트]]