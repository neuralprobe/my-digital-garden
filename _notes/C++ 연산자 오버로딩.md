---
title: 연산자 오버로딩
date: 2023-05-25
tags:
  - OOP
  - OperatorOverloading
  - Cplusplus
  - subpage
---

# 연산자 오버로딩

- 앞선 [[C++ 객체지향프로그래밍의 시작]]에서 만든 MyString 클래스의 멤버함수 중 
	- `compare` 함수를 `==` operator로 바꾸자!
	- `insert` 함수를 `+` operator로 바꾸자!
	- `at` 함수를 `[]` operator로 바꾸자!
```cpp
	if(str1.compare(str2)==0) { ... }
->  if(str1 == str2) { ... } // 더 자연스러운 표현!!

	str1.insert(str1.length(), str2)
->  str1 = str1 + str2; // 더 자연스러운 표현!!

	x = str1.at(10);
->  x = str1[10]; // 더 자연스러운 표현!!
```

- 어떤 연산자가 오버로딩 할 수 있냐면 ....
	- `::`연산자 (범위 지정), `.` 연산자 (멤버 지정) , `*` (멤버 포인터로 멤버 지정) 을 제외한 모든 연산자를 오버로딩 할 수 있음.

![[Pasted image 20230524122457.png]]

---

## MyString의 \=\= 연산자 오버로딩

- 오버로딩할 연산자 함수 선언 형식
	- `{리턴타입} operator{연산자}({연산자가 받는 인자})`
	- eg. `bool operator==(MyString& str)`
		- 그러면 .. `if (str1 == str2)` 처럼 `==` 연산자 사용가능
		- 비교하는 함수인 `compare`도 만들어 놨으니깐, 아래처럼 재활용하자

```cpp
class MyString {
	public:
		...
		bool operator==(MyString& str);
};

bool MyString::operator==(MyString& str) { 
	return !compare(str); 
	// *this와 str이 같으면 compare 에서 0 을 리턴
}	
```

---

## 복소수 클래스 만들기

- 갑자기 복소수는 왜?
	- Complex Number 에서는 [사칙연산](https://j1w2k3.tistory.com/1003)이 모두 재정의되므로, C++로 구현하려면 연산자 오버로딩을 해줘야 함

![[Pasted image 20230524135559.png]]

- 일단 하나의 복소수를 하나의 객체로 다룰거니깐, 복소수 클래스를 만들고, 복소수 관련 함수를 멤버함수로 정의해줘야 함.

```cpp
class Complex {
	private:
		double real, img;
	public:
		//생성자 with initializer list
		Complex(int real,int img): real(real), img(img) {}
}
```

- 사칙연산 함수 정의하기
	- $+, -, \times, \div$ 모두, 
	- 하나의 복소수 객체가 또 다른 복소수 객체를 (안바꿀꺼니깐) 상수 레퍼렌스 인자로 받아,  
	- 임시 복소수 객체를 리턴해야 함.

```cpp
class Complex {
	...
	Complex plus(const Complex& c);
	Complex minus(const Complex& c);
	Complex times(const Complex& c);
	Complex divide(const Complex& c);
}

Complex Complex::plus(const Complex& c){
	Complex temp(real+c.real, img+c.img);
	return temp;
}
```

- 연산자 오버로딩 없이 사칙연산 사용할 때, 
	-  `a + b / c + d` 는 ...
	- `a.plus(b.divide(c)).plus(d)` 라고 써야함
	- 더 간단하게 하고 싶으면 ? 
		- plus $\rightarrow$ $+$, minus $\rightarrow$ $-$, times $\rightarrow$ $*$, divide $\rightarrow$ $/$ 로 연산자 오버로딩 해주자!
		- `plus`의 연산내용을 `operator+` 함수로 정의해도 되고,
		- `plus` 함수 만들고나서, `operator+` 함수에서 호출해도 됨

```cpp
// 클래스 안에서 선언
Complex operator+(const Complex& c);
Complex operator-(const Complex& c);
Complex operator*(const Complex& c);
Complex operator/(const Complex& c);

// 클래스 밖에서 정의
Complex Complex::operator+(const Complex& c){
	return plus(c);
}
// 나머지도 같은 식으로~
```

---

## 대입 연산자 오버로딩

- `=` 연산자는 어떻게 만들지?
	- \*this에 우측 Complex 객체의 내용을 assign 하는거니깐,
	- 우측 Complex 객체의 내용으로 \*this 내용을 업데이트 하고,
	- \*this를 레퍼런스 리턴해서 기존객체의 내용이 업데이트 되도록 만들자.
	- 만들기 귀찮으면?
		- 따로 연산자 오버로딩 안 한 경우, 
		- 복사생성자를 컴파일러가 호출해주니깐, 
		- 복사생성자만 잘 만들어주면 돼

- Tip: 깊은 복사가 필요한 클래스의 복사연산자나 `operator=` 는 주의해서 만들자!
	- 동적할당 되는 메모리 있으면,
	- 그 부분을 복사해서 따로 만들고 포인터로 가리키게 하기!

```cpp
Complex& Complex::operator=(const Complex& c){
	real = c.real;
	img = c.img;
	return *this;
}
```

- `operator+=`, `operator-=`, `operator*=`, `operator/=` 에 같은 방법 적용!

---

# 잡다한 연산자의 오버로딩


## 이항 연산자 함수의 두 가지 형태

```cpp
// string으로 복소수 객체만드는 생성자와, 
// string으로 바로 + 연산 할 수 있는 +연산자 오버로딩을 한 경우,
Complex::Complex(const char* str) {...} 
Complex& Complex::operator=(const char* str){...}

// 이건 되는데,
a = a + "-1.1 + i3.929";
// 이건 안된다!
a = "-1.1 + i3.929" + a;
```

이렇게 이항 연산자 (두개의 항을 갖는 연산자인) 의 앞에 있는 인자가, 연산자 오버로딩 되어 있는 클래스의 객체가 아닌 경우, 어떡하지?
- 컴파일러는 이항 연산자를 두 가지 방법으로 해석한다
	- `*a.operator@(b);` 
		- 이 방식은 연산자 앞에 있는 인자가 operator@함수의 정의를 포함하는 클래스의 객체여야 한다 
	- `*operator@(a,b);` 
		- 이 방식은 operator@함수가 특정 클래스에 포함되어 있지 않은 일반 함수다.

```cpp
// 아래같이 operator+를 인자가 두 개인 일반함수로 정의하면 문제 해결
Complex operator+(const Complex& a, const Complex& b) { 
	Complex temp(a);
	return temp.operator+(b); 
}
// 이제 이거 됨!
a = "-1.1 + i3.929" + a;
// 질문!! 왜 아래처럼 앞 인자를 str 인자로 안받아도 되는거지?
Complex operator+(const char* s, const Complex& b) { ... }
// 이유는 아래처럼, 컴파일러가 앞 인자를 해석할 때, 생성자를 호출하기 때문!!
operator+(Complex("-1.1 + i3.929"), a);
```

---

## "상수 레퍼런스 인자 객체"에 들어있는 "상수가 아닌 멤버함수" 호출?

```cpp
Complex operator+(const Complex& a, const Complex& b) { 
	Complex temp(a);
	return temp.operator+(b); 
}

```

 위의 덧셈 오버로딩 할 때, 왜 굳이 `a`를 `temp`로 복사해서 그 멤버함수를 호출하지?
- `return a+b` 하면 무한루프를 돌거고,
- `return a.operator+(b);` 를 하면 상수레퍼런스 a를 호출한 다음에 상수함수가 아닌 멤버함수인 a.operator+를 부르니깐 컴파일 오류 발생!!
- 따라서,
	- 느린 방법: 상수레퍼런스a를 복사한 temp (하지만 상수변수는 아닌!) 를 이용해 멤버함수를 호출해야한다.
	- 빠른 방법: `real = a.real + b.real;` 처럼 private변수에 접근해서 직접 더해주기!
		- 발생하는 문제? `a.real`은 private 변수라서 외부함수는 접근할 수가 없네? 
		- `friend` 키워드를 써주자!!

---

## friend 는 모든 것을 공유한다

```cpp
class Complex {
	...
	public:
		...
		friend Complex operator+(const Complex& a, const Complex& b);
};
// Complex 클래스안에서 fried로 선언해줬으니깐, 이제 private 변수에 접근할 수 있다!!
Complex operator+(const Complex& a, const Complex& b){
	Complex temp(a.real + b.real, a.img + b.img);
	return temp;
}
```

위처럼 외부함수를 friend 선언할 수도 있고, 다른 클래스를 friend 선언 할 수도 있다.

```cpp
class A {
	...
	friend B;
}
class B {
	...
}
// 이러면, class B는 class A의 private 변수에 접근할 수 있게 됨!
```

---

## 입출력 연산자 오버로딩 하기

```cpp
std::cout << a;
```

입출력에서 사용하는 \<\< (insertion) 연산자는 사실 `std::cout.operator<<(a)` 를 수행한다. 따라서 우리가 만든 Complex 객체를 ostream 클래스의 연산자 operator\<\<() 의 인자로 쓰고 싶으면, ostream 레퍼런스를 리턴하는 operator\<\<() 함수를 오버로드 해줘야 한다.
- Complex 객체의 private 멤버에 접근해야할 경우엔, friend 선언을 해줘야 하고.
- `std::cin` 에 사용하려면 \>\> (extraction) 연산자를 오버로딩해주자.

```cpp
// operator<<() 함수 오버로딩
std::ostream& operator<<(std::ostream& os, const Complex& c) {
	os << "( " << c.real << " , " << c.img <<" ) ";
	return os;
}

// Complex 클래스에서 friend 선언해주기
friend ostream& operator<<(ostream& os, const Complex& c);
```

---

## 첨자 연산자 오버로딩 하기

MyString 문자열 객체에서 원소를 지정할 때 사용하는 첨자 연산자 \[ \] (square bracket operator) 를 오버로딩해보자. 
- 특히 레퍼런스를 리턴해서 문자열의 한 문자 내용을 수정할 수 있도록 만들려면 ...
```cpp
char& MyString::operator[](const int index){
	return string_conent[index];
}
```


---

## int Wrapper 클래스 - 타입 변환 연산자

기본 자료형의 wrapper 클래스?
- 기본 자료형 변수들을 객체로써 다루어야 할 때가 있음
- 예를 들어, int 자료형을 감싸는 int Wrapper 클래스 Int 를 만들고
- `Int x = 3; int a = x + 4` 처럼 int wrapper 클래스를 int operator랑 잘 섞이게 하고 싶으면,
- 일단 기본 생성자 잘 만들어주고,
	- int $\rightarrow$ Int : int를 인자로 받는 생성자 `Int::Int(int data)` 만들자.
	- 디폴트 대입연산자를 사용하면 이 생성자가 호출됨
- 타입 변환 연산자를 오버로딩하면 됨!
	- `Int::operator int() {return data;}`
	- 컴파일러가 Int를 int로 타입변환 할 때 이 타입변환 연산자가 호출됨

![[Pasted image 20230604234452.png]]

---

## 전위/후위 증감 연산자

전위(++a)/후위(a++) 연산자인 operator++를 구분하는 법? 
- 컴파일러가 연산자 오버로딩 선언 형태를 보고 결정함.
- `operator++();`: 전위 연산자를 오버로딩
	- 더하고 사용하고 저장
	- i.e. 값이 바뀐 자신을 리턴
- `operator++(int x);`: 후위 연산자를 오버로딩
	- 사용하고 더하고 저장
	- i.e. 값이 바뀌기 이전의 객체를 리턴하고 자신의 값을 바꾸기

```cpp
A& operator++() { // 전위 연산자
	// A ++ 을 수행한다. 
	return *this; 
}

A operator++(int) { // 후위 연산자
	A temp(A); 
	// A++ 을 수행한다. 
	return temp; 
}
```

---

# N 차원 배열 만들기 프로젝트

## C++ 스타일의 캐스팅

**N 차원 배열**은 **(N-1)단계의 Address의 배열**과 **마지막 단계의 int 배열**들로 구성되므로, 하나의 포인터가 여러 타입에 재활용될 수 있도록 캐스팅해 줄 필요가 있다. 그래서 캐스팅 방법을 먼저 살펴보자!

**C 에서 캐스팅하는 두 가지 방법?**
- 컴파일러가 알아서 캐스팅하는 Implicit 캐스팅
	- int + double $\rightarrow$ double + double (by compiler)
- 프로그래머가 직접 명시하는 Explicit 캐스팅
	- void \* 타입의 주소를 특정 구조체 포인터 타입의 주소로 바꾸는 경우

```cpp
// 명시적 캐스팅 예시
ptr = (Something *)other_ptr;
int_variable = (int)float_variable;
```

하지만, 위 두가지 방법 모두 큰 문제가 있는데, 
- 프로그래머나 컴파일러가 잘못된 타입으로 캐스팅을 해버려도, 프로그램이 오류를 발생시키지 않고 실행되서 이상한 결과가 나오는 경우도 있고,
- 괄호안에 타입을 넣어서 코드 가독성이 떨어진다. `function((int)variable);` 이런 식으로..

C++ 에서 제공하는 4가지 캐스팅 방법!!
- static_cast: 일반적인 타입 변환
- const_cast: 객체의 상수성을 없애는(!) 타입 변환
- dynamic_cast: 파생 클래스 사이에서의 다운 캐스팅
- reinterpret_cast: 위험을 감수하고 (!!) 서로 관련없는 포인터들 사이의 캐스팅

```cpp
// 캐스팅 사용 방법
(원하는 캐스팅 종류)<바꾸려는 타입>(무엇을 바꿀 것인가?)

// float -> float_variable
static_cast<int>(float_variable);

```

---

## N 차원 배열 만들기

N차원 배열 만드는 두가지 방법!
- **커다란 1차원 배열**
	- N차원 배열의 크기만큼 1차원 배열을 할당하고, 접근할 때 정확한 위치를 찾아주는 방법
- **배열의 배열**
	- 2차원 배열의 예시: 더블 포인터 int** arr를 정의하고, arr에 int* 타입의 1차원 배열을 할당하고, 각 원소에 int 타입의 1차원 배열을 한번 더 할당하기 
	- 100차원이면 \* 백개 붙어야 하나?

```cpp
// 더블포인터를 이용한 2차원 배열
int** arr;
arr = new int*[x1];
for (int i = 0; i < x1; i++){
	arr[i] = new int[x2];
}

// N차원 배열을 만들기 위한 Address 클래스
struct Address {
	int level;
	void* next;
}
```

배열의 배열 방법을 간결하게 하기 위해, **배열을 가리키는 포인터를 담고있는 객체**를 만들어서 연결하면 됨!! (이름은 **Address** 클래스!)
- 포인터 타입: 가리키는 배열의 타입이 그때 그때 다르므로 (N-1차원 int 배열일 수도 있고, int 일 수도 있음) void* 로 선언하고 캐스팅으로 사용해야 함.

**N차원 배열 구성하는 아이디어:**
- 레벨0-어드레스-객체는 레벨1-어드레스-객체 배열을 가리키고,
- 그 배열의 원소인 레벨1-어드레스-객체는 레벨2-어드레스-객체의 배열을 가리키고,
- 그 배열의 원소인 레벨2-어드레스-객체는 레벨3-어드레스-객체의 배열을 가리키고,
- ...
- 그 배열의 원소인 레벨(N-2)-어드레스-객체는 레벨(N-1)-어드레스-객체를 가리키는 **것이 아니라!!!**
- **int 배열을 가리키도록 만들자!!** 
- 즉, 마지막 레벨 직전 (레벨 N-2) 의 원소들은 Address 객체의 배열를 포인팅하는게 아니라 int 배열을 포인팅해야한다!!!
- 그리고, operator\[\] 를 잘 오버로딩해서 N차원 주소를 표현할 수 있게 만들자!
![[Pasted image 20230704083824.png]]
<figcaption> Address 객체를 이용해 N차원 배열 만드는 방법! level은 배열의 차원, next는 배열의 원소가 가리키는 배열의 포인터. 그리고, Leaf node 들은 Address 객체가 아닌 Int 배열이어야 한다! </figcaption>

```cpp
class Array;
class Int; // 미리 선언해놓아야 friend 선언 할 수 있음!!

class Array{
  friend Int; // []호출하면 배열도 될 수 있고, int도 될 수 있는 데이터 타입의 wrapper class
  private: 
	const int dim; // 차원수
	int* size; // N차원배열의 크기배열
	// Address 클래스 선언
	struct Address {
		int level;
		void* next;
	}
	// N차원 배열에 접근하기 위한 첫 어드레스 노드
	Address* top;
  public:
	//생성자
	Array(ind dim, int* array_size): dim(dim) {
		size = new int[dim];
		for (int i = 0; i < dim; i++){
			size[i] =  array_size[i];
		}
		top = new Address;
		top->level = 0;
		initialize_address(top);
	}
	//복사 생성자
	Array(const Array& arr) : dim(arr.dim) {
		size = new int[dim];
		for (int i = 0; i < dim; i++){
			size[i] = arr.size[i];
		}
		top = new Address; 
		top->level = 0; 
		initialize_address(top); }
	//소멸자
	~Array(){
		delete_address(top);
		delete[] size;
	}
	// 생성자에 쓸 재귀적으로 배열 만들어 주는 함수
	void initialize_address(Address *current); 
	// 소멸자에 쓸 재귀적으로 배열 지워주는 함수
	void delete_address(Address *current); 
	//[]로 배열원소에 접근하기 위한 함수
	Int operator[](const int index); 
};
```

---

## Array 객체를 initialize 하기 

- void initialize_address(Address \*current) 함수를 만들고,
- `current->level < (dim-1)` 
	- $\rightarrow$ next 포인터가 레벨이 (level+1)인 어드래스 객체의 배열을 가리키게 하고,
- `current->level == (dim-1)`
	- $\rightarrow$ next 포인터가  int 어레이를 가리키게 하자.
- 다음 레벨의 배열 (원소타입이 Address 혹은 int 배열인..) 을 만들었으니깐,
	- 각 원소에 접근해서 initialize_address() 를 재귀호출 하자
	- 이때 각 원소의 타입이 레벨에 따라 다르므로 꼭 static_cast<...>를 해줘야 함

```cpp
void Array::initialize_address(Address *current){
	if (!current) return;
	// Leaf address의 next 포인터는 int 배열을 가리키도록~
	if (current->level == dim-1){
		current->next = new int[size[current->level]];
		return;
	}
	// Leaf가 아니면 next 포인터가 Address 배열을 가리키도록~
	current->next = new Address[size[current->level]];
	// Recursive하게 다음레벨로 가서 initialize 시키자!
	for (int i = 0; i != size[current->level]; i++){
		// 포인터가 void 타입이니깐, 접근할 땐 타입을 캐스팅으로 명시해야함!!
		(static_cast<Address *>((current->next)+i)->level = current->level + 1;
		// 배열의 각 원소에 접근하고 next포인터를 void 타입에서 Address* 타입으로 바꿔준 다음에,
		// initialize_address를 재귀호출하자! (Depth first search)
		initialize_address(static_cast<Address *>(current->next)+i);
	}
}
```

---

## 소멸자는 반대방향!

- (current->next)가 포인팅하는 배열을 지우기 전에, 그 아래 레벨을 먼저 지워야하니깐,
- delete_address() 먼저 재귀호출 한다음에 delete\[\] 명령해야함!!
```cpp
void Array::delete_address(Address *current){
	if (!current) return;
	if (current->level < dim - 1) {
		for (int i=0; i < size[current-level]; i++){
			delete_address(static_cast<Address *>(current->next) + i);
		}
	}
	delete[] current->next;
}
```

---

## Operator\[\] 문제

\[\]를 써서 N차원 배열에 접근하려면?
- `arr[1][2][3][4]` 형식으로 접근하도록 만들어 보자!
- 근데 사실 어려워.... 왜?
	- operator\[\] 가 Array& 타입이면..
	- `arr[1][2][3][4]`의 경우,
	- 4차원 어레이에  operator\[\] 를 적용해 3차원 어레이를 리턴해서
	- `arr'[2][3][4]`로 바꾸는 건 되겠지만,
	- 마지막 `arr'''[4]`는 리턴타입이 int 여야하므로,
	- 오버로딩할 때 operator[] 의 리턴 타입을 하나로 정할 수 가 없다!!!
- 즉... 문제는 ...  operator\[\] 가 상황에 따라선 Array 타입을 리턴해야하고, 맨 끝 차원에선 int 타입을 리턴해야 함!!
- 해결 방법!!!
	- $\rightarrow$ **Int wrapper 클래스를 정의해서 그 안 Array와 int 변수를 담을 수 있게** 하면, operator\[\] 는 Int 하나만 리턴타입으로 정할 수 있음!! 그리고 상황에 따라 Int 안에 Array를 담을지 int를 담을지 정하면 됨.

```cpp
class Int{
  private:
	void* data; // 레벨에 따라 Array을 가리킬지, int를 가리킬지 정해짐
	int level; 
	Array* array; // 어떤 N차원 배열에 접근 중인지 가리킴.
  public:
	Int(int index, int _level = 0, void* _data = NULL, Array* _array = NULL) 
		: level(_level), data(_data), array(_array) {
			//오류 처리
			if (_level < 1 || index >= array->size[_level - 1]) {
				data = NULL; 
				return; 
			} 
			//레벨에 따라 data 정하기 
			if (level == array->dim) { 
				// 이제 data 에 우리의 int 자료형을 저장하도록 해야 한다.
				data = static_cast<void*>((static_cast<int>(
						  static_cast<Array::Address*>(data)->next) + index)); 
			} 
		    else { // 그렇지 않을 경우 data 에 그냥 다음 addr 을 넣어준다.
			    data = static_cast<void*>(static_cast<Array::Address*>(
			              static_cast<Array::Address*>(data)->next) + index);
			}
	}
	Int(const Int& i): data(i.data), level(i.level), array(i.array){}
	Int operator[](const int index);
	operator int();
	Int& operator=(const int&a);
};
```

Int wrapper 클래스를 만들었으니깐, operator\[\] 정의하자

```cpp
Int Array::operator[](const int index){
	return Int(index, 1, static_cast<void *>(top), this);
	// 리턴된 Int는 level = 1, data = top 를 인자로 갖는 생성자에 의해,
	// top->next가 가리키는 배열의 index번째 원소 (그게 Array이든 int든...)를 data로 가질 수 있게 된다!!
}

Int Int::operator[](const int index){
	if (!data) return 0;
	return Int(index, level + 1, data, array);
}

//타입변환 연산자
Int::operator int() {
	if (data) return *static_cast<int *>(data);
	return 0;

Int& Int::operator=(const int&a){
	if (data) *static_cast<int*>(data) = a;
	return *this;
}
```

Array 클래스 사용하는 main 함수!!

```cpp
int main() {
	int size[] = {2, 3, 4};
	Array arr(3, size);
	for (int i = 0; i < 2; i++) {
		for (int j = 0; j < 3; j++) {
			for (int k = 0; k < 4; k++) {
				arr[i][j][k] = (i + 1) * (j + 1) * (k + 1);
	}}}
	for (int i = 0; i < 2; i++) {
		for (int j = 0; j < 3; j++) {
			for (int k = 0; k < 4; k++) {
				std::cout << i << " " << j << " " << k << " " 
				<< arr[i][j][k] << std::endl;
	}}}
}
```


---

# Reference

- [씹어먹는 C++ 5 - 1. 내가 만든 연산자 - 연산자 오버로딩](https://modoocode.com/202)
- [씹어먹는 C++ 5 - 2. 입출력, 첨자, 타입변환, 증감 연산자 오버로딩](https://modoocode.com/203)
- [씹어먹는 C++ 5 - 3. 연산자 오버로딩 프로젝트 - N 차원 배열](https://modoocode.com/204)

