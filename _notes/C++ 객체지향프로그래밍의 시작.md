---
title: 객체지향프로그래밍의 시작
date: 2023-05-18
tags:
  - OOP
  - Cplusplus
  - subpage
---

# 객체지향프로그래밍의 시작

- 객체란, 변수들과 참고 자료들로 이루어진 소프트웨어 덩어리 이다.

## Encapsulation

- 외부에서 직접 인스턴스 변수의 값을 바꿀 수 없고 항상 인스턴스 메소드를 통해서 간접적으로 조절하는 것
- "객체가 내부적으 로 어떻게 작동하는지 몰라도 사용할 줄 알게 된다"

## 클래스를 이용해 만들어진 객체(= Instance)의 구성요소

- Instance variable (인스턴스변수)
- Instance method (인스턴스메소드)

##  클래스?

- 객체의 설계도
- 클래스의 구성요소
	- Member variable (멤버변수)
	- Member function (멤버함수)

```cpp
class Animal {
	private:
		int food; 
		int weight;
	public:
		void set_animal(int _food, int _weight){
			food = _food;
			weight = _weight;
		}
		...
}; //세미콜론 꼭 붙이기!!

int main(){
	Animal animal;
	animal.set_animal(100,50);
	...
	return 0;
}
```

## 클래스의 접근 지시자

- Encapsulation 구현하는데 사용
- `private` : 클래스 외부에서 접근이 불가능한 멤버 함수와 변수를 정의
	- 키워드를 명시하지 않았으면 자동으로 `private` 으로 설정
- `public` : 클래스 외부에서도 접근이 가능한 멤버 함수와 변수를 정의
- `protected` : `private`과 유사하지만, 파생 클래스에서는 접근이 가능

---

# 생성자와 함수의 오버로딩

## 함수의 오버로딩

- 함수 이름이 같더라도, 인자가 다르면 다른 함수로 판단!
- 따라서 인자를 달리 하는 여러 함수를 하나의 이름으로 호출 할 수 있음
- 각각의 (인자가 다르고 함수 이름은 같은) 함수를 각각 선언해야함

```cpp
void print(int x) { 
	std::cout << "int : " << x << std::endl; 
} 

void print(double x) {
	std::cout << "double : " << x << std::endl; 
}

int main() {
	int a = 1;
	char b = 'c';
	double c = 3.2f;
	print(a);  // void print(int x) 호출됨
	print(b);  // char 타입이 int로 casting 되서 
			   // void print(int x) 호출됨
	print(c);  // void print(double x) 호출됨
	return 0;
```

## 컴파일러가 함수를 오버로딩하는 과정

- (1단계) 타입이 정확히 일치하는 함수 찾기
- (2단계) 타입이 딱 안맞으면, casting (타입 변환)해서 함수 일치되는지 보기
- (3단계) 또 안되면, 좀 더 포괄적인 타입 변환을 시도 (float $\rightarrow$ int, 포인터 $\rightarrow$ void 포인터)
- (4단계) 유저 정의된 타입변환으로 일치하는 것 찾기 (?)
- 그래도 안되면 "ambiguous  call" 오류!

---

## 클래스의 멤버함수 안쪽에/바깥쪽에 정의하는 법

```cpp
class Date {
	private: // 안 적어도 private 됨.
		int year_; // private 변수
	public:
		// 안에서 함수 정의할거면 세미콜론 하지마
		void AddDay(int ...){
			...
		}
		// 밖에서 함수 정의할거면 선언만 하고 세미콜론 박아
		void SetDate(int ...); 
}; // 클래스 끝에는 꼭 세미콜론 붙이기
// 클래스 안에서 선언만 된 함수는 밖에서
// {클래스이름}::{함수이름}(인자){ ... } 로 함수 정의해야 함
void Date::Setdata(int ...){
	...
}
```

---

## 생성자 (Constructor) 만들기

- 생성자는 기본적으로 "객체 생성시 자동으로 호출되는 함수"
- 객체를 초기화만 하면 되니깐 리턴값 없다!!
- 리턴값이 없으니깐, 함수 이름 앞에 붙이는 타입도 적지 마!!
- 클래스 안에 정의할 때:
	- `{클래스이름}({인자}){ 초기화 내용 }`
	- 명시적 디폴트 생성자 정의 (안해도 됨)
		- `{클래스이름}() = default;`
- 클래스 밖에 정의 할 때:
	- 안에서 선언: `{클래스이름}({인자});`
	- 밖에서 정의: `{클래스이름}::{클래스이름}({인자}){ 초기화 내용 }`

## 생성자 써서 객체 만들기

- 객체 생성하는 방법
- `Date day(2011, 3, 1);` 
	- 객체 이름 뒤의 인자들을, Date 클래스 생성자에 implicitly 전달
	- 암시적 방법 (implicit)	
	- 편해서 더 자주 씀
	- 하지만, 암시적 표현 문법이 함수 선언이랑 헷갈릴 수 있으니 조심!
- `Date day = Date(2011, 3, 1);` 
	- 명시적인 객체 생성 문법
	- "객체 day는 Date 클래스 생성자에 2011, 3, 1 인자들을 전달해서 초기화한 것"
	- 명시적 방법 (explicit) 
	- `new`를 써서 heap에 저장하고 싶으면 명시적 생성 문법 이용
		- `Date day = new Date(2011, 3, 1);` 
		- 꼭 delete 해줘야 함

## 디폴트 생성자 (Default constructor)

- 인자가 없는 생성자
- eg. `Date::Data(){ ... }`
- 생성자를 전혀 정의하지 않으면 아무것도 하지 않는 디폴트 생성자 호출됨.
	- 컴파일러가 마음대로 (아무것도 안하는 방식으로) 초기화해버림
- Implicitly 디폴트 생성자 호출: `Date day;`
	- `Date day()` 는 리턴값이 Date이고 인자가 없는 함수 day를 정의한 것이 되니깐 조심!
- Explicitly 디폴트 생성자 호출: `Date day =  Date();`
- 명시적 디폴트 생성자 정의 (안해도 됨)
	- `{클래스이름}() = default;`

## 생성자 오버로딩

- 생성자 역시 함수 이기 때문에 마찬가지로 함수의 오버로딩 가능!

```cpp
class Date{
	private:
		...
	public:
		Date(){ ... }
		Date(int year, int month, int day){ ... }
		...
};
```

---

# 복사 생성자와 소멸자

## 같은 클래스의 객체 많이 만들어서 관리 해야 할 때?

- 스타크래프트에서 마린이 정말 많아질 경우, 객체의 배열을 만들자
- 포인터로 이뤄진 배열을 stack에 만들고, 각 포인터 원소가 heap 에 저장한 객체를 가리키자
	- `Marine* marines[100]; marine[0] = new Marine(2,3);`
- 각 객체별로 heap에서 지워줘야 해
	-  `delete marine[0];`

```cpp
// Marine 클래스의 생성자는
// Marine::Marrine(int x, int y){ ... } 형태
// Marine 객체를 담는 배열 선언하고 원소를 채워넣자
// 여기선 포인터 배열은 스택에, 원소가 가리키는 객체는 힙에 저장했음
Marine* marines[100];
marine[0] = new Marine(2,3);  // heap에 객체들 하나씩 생성 
marine[1] = new Marine(3,5);  // new 다음에 명시적 생성자 호출

delete marine[0]; // heap에 있는 객체들 하나씩 소멸 
delete marine[1];
```

---

## 소멸자 (Destructor)

- 멤버 변수가 heap에 있는 데이터를 가리키는 포인터 일 때?
	- 멤버 변수 : `char* name;`
	- 생성자에서 초기화할 때 heap에 문자열 저장 : `name = new char[strlen(marine_name)+1];`
- 소멸자에서 delete 해줘야 함!! 안그러면 memory leak 발생!
	- name이 배열을 가리키는 포인터니깐, `delete[]` 해줘야 컴파일러가 배열인줄 알고 잘 지워줌
	- `~Marine(){ delete[] name; }`
- 디폴트 소멸자가 있긴한데, 아무일도 안 함.
- 소멸자 역할 \#1 : 객체가 동적으로 할당받은 메모리를 해제
- 소멸자 역할 \#2 : 쓰레드 사이에서 lock 된 것을 푸는 역할

```cpp
class Marine {
	private:
		...
	public:
		...
		~Marine();
};

Marine::Marine(int x, int y, const char* marine_name){
	name = new char[strlen(marine_name)+1];
	strcpy(name, marine_name);
	...
}

Marine::~Marine(){
	if (name != NULL) {
		delete[] name;
	}
}
```

---

## 복사 생성자 (Copy constructor)

- 스타크래프트에서 포토캐논 겹치기가 하고 싶어!! 
	- 즉, 모든 인스턴스 변수가 같은 객체를 많이 만들고 싶을 때!
- 복사하려는 객체를 상수 레퍼런스로 받아야 해.
	- 클래스 안에서 `Photon_Cannon::Photon_Cannon(const Photon_Cannon& pc);` 로 선언
- 사용할 때는 ..
	- `Photon_Cannon pc1(3,3);` 으로 기본 객체 만들고,
	- `Photon_Cannon pc2(pc1);` 으로 객체를 인자로 받아서 복사!
	- `Photon_Cannon pc3 = pc2` $\rightarrow$ 컴파일러가 `operator=` 오버라이드 해줘서 복사 생성사 호출! 
		- `pc3 = pc2` 는 그냥 대입하는거니깐 복사생성자 안 부름
- 디폴트 복사 생성자?
	- 사실, 복사 생성자 따로 안 만들어도 (!)
	- 인스턴스 변수를 복사해주는 기능을 자동으로 해줌.
	- 한계:
		- 그대로 복사해버리니깐,
		- Heap에 있는 데이터를 가르키는 포인터인 멤버변수도 그대로 복사해버려서 = ==얕은 복사 = Shallow copy==
		- 두 개의 다른 객체가 heap에 있는 하나의 데이터를 가르킴
		- 그러다가 한 객체가 소멸되면, 그 데이터가 delete되면서
		- ==Dangling pointer== 문제 발생 !!
		- 그래서 함수 scope을 벗어날 때 (eg. main 함수) 둘 중 하나가 먼저 소멸 되면서, 남은 객체를 소멸할 때, 소멸 대상이 되는 데이터가 없어서 run time 오류 발생 함.
	- So, 복사 생성자 만들 때는 꼭 멤버변수 포인터가 가리키는 heap의 데이터 복사해주는 ==깊은 복사 (Deep copy)==를 해주자!
		- 복사 생성자 안에서,
		- `name = new char[strlen(pc.name)+1]; strcpy(name, pc.name);`

```cpp
class Photon_Cannon {
	int coord_x, coord_y; 
	char* name;
	...
	public:
	Photon_Cannon(int x, int y, const char* input_name);
	Photon_Cannon(const Photon_Cannon& pc);
	...
};

// 기본 생성자
Photon_Cannon::Photon_Cannon(int x, int y, const char* input_name){ 
	name = new char[strlen(input_name)+1];
	strcpy(name, input_name);
	... 
	}

// 복사 생성자
Photon_Cannon::Photon_Cannon(const Photon_Cannon& pc){
	//일반 변수는 그냥 복사
	{멤버변수} = pc.{멤버변수};
	//포인터 변수는 deep copy
	name = new char[strlen(pc.name)+1]; 
	strcpy(name, pc.name);
	...
}
```

![[Pasted image 20230518074244.png]]

---

# Const, const, const!

## 생성자의 초기화 리스트 (Initializer list)

- 생성자 정의할 때,
- `{클래스이름}::{클래스이름}(인자) : {인자1 이름}({인자1 값}), {인자2 이름}({인자2 값}) {생성자 본문}`
- 즉... 원래 생성자의 이름부분과 함수내용 부분 사이에
- 콜론 + {인자1 이름}({인자1 값}) + 콤마 + {인자2 이름}({인자2 값}) 형태의 
- Initializer list를 삽입하면,
- 생성자의 멤버변수를 조금 더 편하게 초기화 할 수 있음.

```cpp
Marine::Marine() : hp(50), coord_x(0), coord_y(0), damage(5), is_dead(false) {}
Marine::Marine(int x, int y) : coord_x(x), coord_y(y), hp(50), damage(5), is_dead(false) {}
```

## 굳이 초기화 리스트 써야하는 이유?

- 생성과 초기화를 동시에 함!! 
	- `int a = 0;`
	- 멤버변수가 클래스라면, 복사생성자를 호출!
- 보통 생성자는 생성을 하고, 초기화를 함!! 
	- `int a; a = 0;`
	- 멤버변수가 클래스라면, 디폴트 생성자를 호출한 다음에 대입!
- 따라서, 생성을 하면서 초기화해야하는 경우에 Initializer를 써야 함!!
	- 상수 선언
		- `const int a = 10;` 처럼 처음에 상수값 정해야 함
		- 따라서, 예외적으로 클래스의 멤버변수로서의 상수는 상수값없이 선언할 수 있음
			- `private: const int a;` 가능.
			- Q: 원래 에러나는거 아님?
			- A: 객체만들 때, 당연히 그리고 무조건 Initializer list로 상수값을 정하면서 상수가 만들어질테니깐!!
	- 레퍼런스 선언
		- `int& ref = a;` 처럼 처음에 참조하려는 변수 정해야 함
	- 복사 생성자로 객체를 만들고 싶을 때

---

## Static 멤버 변수

- 특정 클래스를 찍어낸 객체의 갯수를 count 하고 싶을 때
	- 사실 std::vector 쓰면 되긴 하지만... ㅋ
- 하나의 객체에 속한 것이 아닌, 
- 같은 클래스로 만든 모든 객체가 공유하는 딱 하나의 변수인 static 변수 (eg. counter)를 써서 기록하자
- eg. `static int total_marine_number;`

```cpp
class Marine {
	static int total_marine_number;
	private:
		int hp;
	public:
		Marine();
		~Marine();
};

Marine::Marine() : hp(50) {total_marine_number++;} //생성할 때 마린숫자 더하기
Marine::~Marine() {total_marine_number--;} // 생성할 때 마린숫자 줄이기
```

## Static 멤버 함수

- 같은 클래스로 만든 모든 객체가 공유하는 딱 하나의 함수인 static 함수
- 객체가 하나도 생성되지 않아도 쓸 수 있다!
- 함수 선언/정의 할 때  `static {함수이름}({인자})` 처럼 static을 붙여주면 됨
- 사용할 때,
	- {객체}.{멤버함수} 형태가 아니고,
	- {클래스}::{static멤버함수} 형태!! 콜론 두개 !!

```cpp
class Marine {
	static int total_marine_number;
	...
	static void show_total_marine(){
		std::cout << "전체 마린 수 : " << total_marine_num << std::endl;
	}
};

int main(){
	Marine marine1(2, 3, 5); // 마린 객체 하나 만듦
	Marine::show_total_marine(); // 마린 객체에 의존하지 않고, static 함수를 호출할 수 있다!!!
	...
}
```

---

## this

- 객체 안에서 자기 자신을 가리키는 포인터
- 포인터의 값, 즉 객체 자신은 `*this` 로 표현
- 객체 자신의 멤버 변수/함수를 가리킬 경우,
	- `this->{멤버변수/함수이름}`
	- 클래스 내에서 그냥 `{멤버변수/함수이름}`을 쓰는것 과 같은 효과
- Static 함수는 this로 접근 $\times$ 

```cpp
// Marine 클래스 안의 be_attacked 멤버함수
// 객체 자신을 상태변화 시킨다음에 레퍼런스로 리턴!
Marine& Marine::be_attacked(int damage_earn) {
	hp -= damage_earn;
	if (hp <= 0) is_dead = true;
	return *this;
}

Marine marine1(2, 3, 5);
Marine marine2(3, 10, 10);
// marine2가 marine1한테 두 번 연속 공격받았을 때,
// ".be_attacked(marine1.attack())"으로 
// 한 번 be_attacked 멤버함수를 호출한 결과가 객체 자신의 레퍼런스기 때문에,
// ".be_attacked(marine1.attack())"을 
// 한 번 더 적용해서 두번의 연속 공격을 표현할 수 있다.
marine2.be_attacked(marine1.attack()).be_attacked(marin1.attack());

// Qustion: 
	// Marine::be_attacked 함수가 레퍼런스 리턴이 아니라, 
	// 그냥 객체 값 리턴하는 함수라면, 
	// 두 번 공격하려고 적은 위 문장은 어떤 결과를 갖는가?
// Answer: 
	// marine2는 한 번 공격당한 상태로만 변하고, 
	// marine2.be_attacked(...)에 의해 생성된 
	// 임시객체(R-value)인 마린객체를 한 번 더 공격하는 셈이 되어서, 
	// 두번쨰 공격이 무의미해진다.
```

## 레퍼런스를 리턴하는 함수?

- 함수 안에 존재하는 변수/객체의 레퍼런스를 반환
- 따라서 레퍼런스 반환하는 변수/객체가 소멸되지 않는 한,
- 주소가 존재하는 L-value
- 따라서, 외부에서 정의한 레퍼런스에 담을 수 있다.
- 반대로, 값을 리턴하는 함수의 값은
	- 값을 복사해서 리턴하기 때문에, 함수 내에서 리턴한 변수/객체에 접근할 수 없고,
	- 함수를 쓰는 문장이 끝나면 사라지는 임시객체 (R-value) 이므로, 외부에서 정의한 레퍼런스에 담을 수도 없다
- [[C++ 의 참조자]] 참고요~

```cpp
// 레퍼런스를 리턴하는 함수 
#include <iostream>
class A { 
	int x;
	public:
		A(int c) : x(c) {} 
		
		// 레퍼런스를 리턴하는 함수? -> 객체 내부의 변수 x의 레퍼런스를 반환
		// 이 때, 함수의 값은 a.x의 레퍼런스는 주소가 존재하는 L-value
		int& access_x() { return x; }  
		
		// 값을 리턴? -> 객체 내부의 변수인 x의 값을 복사해서 반환
		// 이 때, 함수의 값은 임시객체인 R-value
		// 임시 객체는 문장이 끝나면 소멸 됨
		int get_x() { return x; }      
		void show_x() { std::cout << x << std::endl; } 
};

int main(){
	A a(5); //A클래스 객체a를 암시적으로 생성, 
	a.show_x() // Print "5"
	int& c = a.access_x(); 
	// a.x의 레퍼런스를 레퍼런스 c에 전달
	// 따라서 c는 a.x의 레퍼런스
	c = 4;
	a.show_x() // Print "4"

	int d = a.access_x();
	// a.x의 레퍼런스를 보통 변수 d에 복사 전달
	// 따라서 d는 a.x 값이 복사된 별개의 변수
	d = 3;
	a.show_x() // Print "4"
	
	int& e = a.get_x(); 
	// a.x의 값을 레퍼런스 타입 변수 c에 전달?? 
	// 임시객체인 R-value를 레퍼런스하는건 불가능! 컴파일 오류남!

	int f = a.get_x();
	// a.x의 값을 보통 변수 d에 복사 전달
	// 따라서 f는 a.x 값이 복사된 별개의 변수
	// Note: 값이 두번 복사됨!!
	f = 1;
	a.show_x() // Print "4"
}
```

![[Pasted image 20230519050620.png]]

---

## Const 함수

- 변수들의 값을 바꾸지 않고 읽기만 하는 함수 
- 함수 선언 뒤에 `const` 넣으면 됨
- 클래스 내부에서 선언
	- `int attack() const;`
- 클래스 밖에서 정의
	- `int Marine::attack() const { return x; }`

---

# 내가 만들어보는 문자열 클래스

- `MyString` 문자열 클래스 만들기!

- 지원할 내용
	1. 문자(char) 로 부터의 문자열 생성, C 문자열 (char \*) 로 부터의 생성
	2. 문자열 길이를 구하는 함수
	3. 문자열 뒤에 다른 문자열 붙이기
	4. 문자열 내에 포함되어 있는 문자열 구하기
	5. 문자열이 같은지 비교
	6. 문자열 크기 비교 (사전 순)

- C++ 에서는 정말 왠만하면 char 배열을 사용하는 것보다 string 을 사용해서 문자열을 다루는 것을 권장!!

## MyString 멤버변수
 
1. 문자열 데이터의 저장공간을 가리키는 포인터 `char* string_content;`
	- 데이터 자체를 멤버변수로 만들지 않는 이유?
		- 문자열 데이터의 크기가 바뀔 때, 
		- 데이터가 할당된 메모리를 해제했다가 새로 할당해야하는데,
		- 런타임 중간에 멤버변수를 지웠다가 새로 쓰고 할 수가 없음!
		- 그니깐 포인터를 멤버변수로 하고, 동적할당되는 데이터를 가리키게 하자.
	- 포인터가 가리킬 공간 할당
		- `string_conent = new char[string_length];`

2. 문자열 데이터의 길이 `int string_length;`
3. 문자열 데이터 공간의 크기 `int memory_capacity;`

## MyString 생성자/소멸자

1. 문자 하나로 생성 : 인자 =  `char c`
2. 문자열로부터 생성 : 인자 = `const char* str` 
3. 복사생성자 : 인자 = `const MyString& str`
- `MyString` 클래스에는 문자열 끝에 `NULL` 안 넣고, 필요한 정보만 저장하기로. 문자열 끝은 `string_length` 로 판별.
- 소멸자?
	- Heap 에 할당된 문자열 데이터 지우자
	- `delete[] string_content;`

## 각종 함수

- 길이 구하는 `length` 함수
	- 읽기만 하는 함수이므로 const 함수로 만들자.
	- `int MyString::length() const { return string_length; }`

- 출력하는 `print` 함수
	- 리턴할 필요가 없는 함수니깐, `return` 생략!
	- 함수형은 `void`
	- 출력을 마치는 순가은 `string_length` 만큼 `for-loop` 돌려서 판단
	- `void MyString::print(){ ... }`
	- 상수함수!

- 줄바꿔 출력해주는 `println` 함수
	- `std::cout << std::endl;` 넣고 안 넣고 차이
	- 상수함수!

- `assign` 함수
	- `str.assign("abc")` 해서 기존 문자열 지우고, 새 문자열을 대입시키는 함수
	- 인자는 char배열 형태일 수도 있고, MyString 객체일 수도 있음
	- \*this의 레퍼런스를 리턴하면, `str.assign("abc")`  자체가 새로운 `str` 이 되서, 계속 dot-operator (".") 로 멤버변수 호출 가능 
	- 기존 문자열의 memory_capacity와 새 문자열 길이 (혹은 memory capacity) 비교해서, 필요하면 공간 지우고 새로 할당.

```cpp
MyString& assign(const MyString& str); 
MyString& assign(const char* str);
```

- `reserve` 함수
	- 문자열 크기를 미리 예약해 놓는 함수

- `capacity` 함수
	- 문자열의 크기를 알려주는 함수
	- 상수함수!

- `at` 함수
	- Index 써서 random access!
	- 범위 컨디션 체크
	- 상수함수!

- `insert` 함수
	- 인자 = 삽입할 위치, 문자열 (MyString 객체, or char배열, or 문자하나)
		- 따라서, MyString 객체, char배열, 문자하나 별로 함수 하나씩 만들어서 오버로드!!
		- MyString 객체 버전 `insert` 함수 하나 만든 다음에 재활용해서 오버로드된 `insert` 함수들 만들 수 있음
	- 삽입된 객체를 레퍼런스로 리턴하면, dot-operator 연달아 쓸 수 있음
	- 함수 내용?
		- 삽입 위치의 범위 맞는지 확인
		- 메모리 공간이 확보되어있는지 확인. 
			- 부족하면 새로 생성
			- 기존 공간 `delete`
			- 잦은 insert 에 대비해서, 메모리를 미리 할당할 경우 현재 메모리 크기의 두 배 정도를 할당하자
		- 삽입할 위치를 기준으로 문장을 잘라서, 문장 위치를 미뤄버리거나/복사하기
		- `*this` 레퍼런스 리턴!

```cpp
MyString& MyString::insert(int loc, const MyString& str); 
MyString& MyString::insert(int loc, const char* str); 
MyString& MyString::insert(int loc, char c);
```

- `erase` 함수
	- `insert` 함수보다 쉬운데, 메모리 크기가 늘어날 일이 없기 때문!

- `find` 함수
	- 인자 = 찾기 시작할 위치, 찾는 문자열 (MyString 객체, or char배열, or 문자하나)
	- 상수함수!
	- 찾았으면 문장의 위치를 리턴, 못 찾았으면 -1 리턴

- `compare` 함수
	- 문자열간의 크기 비교하는 함수 (`*this` vs `str`)
		- i.e. 사전식으로 배열해서 어떤 문자열이 더 뒤에 오는지 판단
		- "abb" > "acb"
		- "abc" > "abcd"
		- `(*this) - (str)` 을 수행해서 그 1, 0, -1 로 그 결과를 리턴한다 
			- 1 은 (\*this) 가 사전식으로 더 뒤에 온다는 의미
			- 0 은 두 문자열이 같다는 의미
			- -1 은 (\*this) 가 사전식으로 더 앞에 온다는 의미이다.
	- 상수함수!
	- 각 문자를  `>`로 비교하면 됨

![[Pasted image 20230519080023.png]]
![[Pasted image 20230519080039.png]]
![[Pasted image 20230519080055.png]]
![[Pasted image 20230519080118.png]]
![[Pasted image 20230519080134.png]]
![[Pasted image 20230519080152.png]]
![[Pasted image 20230519080217.png]]
![[Pasted image 20230519080228.png]]
![[Pasted image 20230519080236.png]]
![[Pasted image 20230519080246.png]]
![[Pasted image 20230519080259.png]]
![[Pasted image 20230519080312.png]]
![[Pasted image 20230519080321.png]]
![[Pasted image 20230519080332.png]]

---

# 클래스의 explicit 과 mutable 키워드

## explicit

```cpp
void DoSomethingWithString(MyString s) { 
	// Do something...
}

DoSomethingWithString(MyString("abc"))

DoSomethingWithString("abc")
```

- 위 첫번째 함수 호출은, MyString 객체를 명시적으로 생성해서 인자로 전달
- 두번째 함수 호출은, 원래는 안되지만 
	- 컴파일러가 "abc" 를 써서 MyString 객체를 만들어주는 생성자를 찾아서 객체를 생성하고 인자로 전달해 줌
	- 즉, `MyString(const char* str);` 이거를 갖다 씀
	- 이렇게 컴파일러가 알아서 생성자 체크하고 골라주는걸 **암시적 변환 (implicit conversion)** 이라고 함.

- 암시적 변환이 좋은 것 같지만,
	- 프로그래머가 실수로 잘못된 타입을 입력해도
	- 컴파일러가 제멋대로 생성자 골라서 객체 생성하기 때문에
	- 이상한 일이 벌어질 수 있음!!

- explicit 키워드
	- C++가 암시적 변환을 하지 못하도록 해주는 키워드!!
	- 생성자 선언할 때, 앞에 붙여주면 됨

```cpp
explicit MyString(int capacity);
```

## mutable

- const 함수 내부에서 멤버 변수를 바꾸는거 안됨
- 근데 멤버변수를 mutable로 선언 했으면, const 함수에서도 값을 바꿀 수 있음

```cpp
class A {
	mutable int data_;
	public:
		A(int data) : data_(data) {}
		void DoSomething(int x) const { // 상수함수
			data_ = x; // 상수함수지만, data_는 mutable 키워드 달았으니깐 바꿀 수 있음
		}
}

int main(){
	A a(10);
	a.DoSomething(3); // 실행 잘 됨
}
```

- mutable 필요한 이유?
	- 멤버함수에 const를 선언하는건 "이 함수는 객체의 내부 상태를 안 바꿉니다"는 표현
	- 근데, "읽기" 같이 객체 내부를 안바꿔야하는 함수도, 조금은 바꿀 필요가 있다.
	- 예시: **읽기 함수에서 소프트웨어 캐시에 데이터를 저장/삭제 해야할 경우**
		- 서버에서 데이터를 읽는 const 함수가 있는데,
		- 데이터베이스에서 데이터 요청해서 가져오는게 너무 느리니깐,
		- 소프트웨어 캐시를 사용할 때가 있음
		- 그럼, cache miss 가 날 때, cache에 데이터를 저장하는 기능이 함수에 들어가야 함
		- 따라서, cache buffer 역할을 하는 변수나 객체는 mutable 선언을 해야 함!!


---

# Reference

- [씹어먹는 C++ 4 - 1. 이 세상은 객체로 이루어져 있다](https://modoocode.com/172)
- [씹어먹는 C++ 4 - 2. 클래스의 세계로 오신 것을 환영합니다 (함수의 오버로딩, 생성자)](https://modoocode.com/173)
- [씹어먹는 C++ 4 - 3. 스타크래프트를 만들자 ① (복사 생성자, 소멸자)](https://modoocode.com/188)
- [씹어먹는 C++ 4 - 4. 스타크래프트를 만들자 ② (const, static)](https://modoocode.com/197)
- [씹어먹는 C++ 4 - 5. 내가 만드는 String 클래스](https://modoocode.com/198)
- [씹어먹는 C++ 4 - 6. 클래스의 explicit 과 mutable 키워드](https://modoocode.com/253)

