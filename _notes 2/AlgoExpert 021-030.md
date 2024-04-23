---
title: AlgoExpert 021-030
date: 2024-01-20
tags:
  - Algorithm
  - Coding
  - Cplusplus
  - subpage
  - AlgoExpert
---
# AlgoExpert 021-030

---
# 21. Find Three Largest Numbers

![[Pasted image 20240119231432.png]]

```cpp
#include <vector>
using namespace std;

vector<int> findThreeLargestNumbers(vector<int> array) {
  // Write your code here.
  vector<int> threes ;
  for (int i = 0; i<array.size() ; i++ ){
    int el = array[i];
    if (threes.size() == 0) threes.emplace_back(el);
    else if (threes.size() == 1) {
      if (threes[0] <= el) threes.emplace_back(el);
      else {threes.emplace_back(threes[0]); threes[0] = el;}
    }
    else if (threes.size() == 2) {
      if (threes[1] <= el) threes.emplace_back(el);
      else if (threes[0] <= el) {threes.emplace_back(threes[1]); threes[1] = el;}
      else {threes.emplace_back(threes[1]); threes[1] = threes[0]; threes[0] = el;}
    }
    else if (threes.size() == 3){
      if (threes[2]<=el) {
        threes[0] = threes[1];
        threes[1] = threes[2];
        threes[2] = el;
      }
      else if (threes[1]<=el){
        threes[0] = threes[1];
        threes[1] = el;
      }
      else if (threes[0]<=el) threes[0] = el;
    }

  }
  return threes;
}

```

- 한줄요약: 비어있는 벡터를 만들고 array를 iteration하면서 (1) 벡터를 세칸 채우는 과정 정하고, (2) 세칸이 채워지면 새로운 큰 숫자왔을 때 promotion/reduce 시큰 방법 정하면 됨

---
# 22. Bubble Sort

![[Pasted image 20240119231502.png]]

```cpp
#include <vector>
using namespace std;

void swap(int& a, int& b){
  int temp;
  temp = a;
  a = b;
  b = temp;
}

vector<int> bubbleSort(vector<int> array) {
  // Write your code here.
  int s = array.size();
  for (int i = 0; i < (s-1) ; i++){
    for (int j = 0; j < (s-1-i) ; j++){
      if (array[j] > array[j+1]) swap(array[j], array[j+1]);
    }
  }
  return array;
}

```

- 한줄요약: 버블소트는 (1) j-iteration 하면서 붙어있는 두 숫자 비교해서 큰거를 뒤로 swap해주면, 맨 뒤에 가장 큰 숫자가 fix되고, (2) 이 방식으로 i-iteration해주면서 맨 뒤부터 가장 큰 숫자를 채워나가면 됨. 

---
# 23.  Insertion Sort

![[Pasted image 20240119231526.png]]

```cpp
#include <vector>
using namespace std;

vector<int> insertionSort(vector<int> array) {
  // Write your code here.
  int n = array.size();
  for (int i = 1; i < n; i++){
    int key = array[i];
    int j = i-1;
    while( (j>= 0) && ( key < array[j])) {
      array[j+1] = array[j];
      j--;
    } 
    array[j+1] = key;
  }
  return array;
}

```

- 한줄요약: 인설션 소트는 (1) 두번째 칸 (i=1) 부터 i-forward-iteration 하면서 i-th value를 key로 지정하고 (2) 그 앞에 있는 value 들을 j-reverse-iteration 하면서 삽입해서 (3) i 번째 값까지 정렬되게 하는 방식.


---
# 24. Selection Sort

![[Pasted image 20240119231604.png]]

```cpp
#include <vector>
using namespace std;

void swap(int& a, int&b){
  int t;
  t = a;
  a = b;
  b = t;
}

vector<int> selectionSort(vector<int> array) {
  // Write your code here.
  int s = array.size();
  for (int i = 0; i < s-1 ; i++){
    int min = i;
    for (int j = i+1; j < s ; j++){
      if (array[j] < array[min]) {min = j;}
    }
    swap(array[i],array[min]);
  }
  return array;
}
```

- 한줄요약: 셀렉션 소트는.... (1) 첫번째 값 (i=0) 부터 i-forward-iteration 하면서 (2) i번째 값보다 작은 값을 발견하면 swap 해서 맨 앞부터 정렬되게 하는 방식. 
- Sort 알고리즘 비교:
	- 버블 소트:  맨 뒤부터 정렬.  이웃한 숫자를 비교하면서 swap.
	- 인설션 소트: 맨 앞부터 정렬. key 값을 중간에 삽입하면서 정렬.
	- 셀렉션 소트: 맨 앞부터 정렬. 가장 작은 값을 정렬되지 않은 부분의 맨 앞으로 보내기.

---
# 25. Palindrome Check

![[Pasted image 20240119231626.png]]

```cpp
using namespace std;

bool isPalindrome(string str) {
  // Write your code here.
  int len = str.length();
  int mid = len/2;
  for (int i = 0; i < mid; i++){
    if (str[i] !=  str[len-1-i]) return false;
  }
  return true;
}

```

- 한줄요약: 왼쪽, 오른쪽 포인터 같이 가운데로 움직이면서 비교

---
# 26. Caesar Cipher Encryptor

![[Pasted image 20240119231652.png]]

```cpp

using namespace std;

string caesarCypherEncryptor(string str, int key) {
  // Write your code here.
  cout << str << endl;
  for (auto& ch : str){
    cout << "key:" << key <<endl;
    cout << ch << 
      "->" << static_cast<int>(ch) << 
      "->" << static_cast<char>(97+(static_cast<int>(ch)+key-97)%26) << endl;
    ch = static_cast<char>((97+(static_cast<int>(ch)+key-97)%26));
  }
  cout << str << endl;
  return str;
}

```

- 한줄요약: static_cast 로 int 와 char 왔다갔다하면서 ascii decode 하면 됨.
- ascii 정보: 소문자 알파벳은 97번부터 시작, 알파벳 갯수는 26개

---
# 27. Run Length Encoding

![[Pasted image 20240119231723.png]]


```cpp
using namespace std;

string runLengthEncoding(string str) {
  // Write your code here.
  cout << str << endl;
  string result;
  char key;
  int count = 0;
  for (char ch : str){
    if (count == 0){
      key = ch;
      count++;
    }
    else if (key == ch && count < 9){
      count++;
    }
    else {
      result += to_string(count) + (key);
      key = ch;
      count = 1;
    }
    
  }
  result += to_string(count) + (key);
  cout << result << endl;
      
  
  return result;
}

```

- 한줄요약: 같은 숫자 카운트해가면서 encoding

---
# 28. Common Character

![[Pasted image 20240119231748.png]]


```cpp
using namespace std;

vector<string> commonCharacters(vector<string> strings) {
  // Write your code here.
  unordered_set<char> common(strings[0].begin(), strings[0].end());
  vector<char> to_erase;
  for (auto &s : strings){
    for (auto elem : common){
      if (s.find(elem)== std::string::npos) to_erase.emplace_back(elem);
    }
  }

  for (auto elem : to_erase){
    common.erase(elem);
  }

  vector<string> result;
  for (auto  elem : common){
    string str;
    str = elem;
    result.emplace_back(str);
  }
  return result;
}

```

- 한줄요약: 첫번째 string의 문자를 unordered set에 넣고, string 하나씩 보면서 없는 문자 지우기

---
# 29. Generate Document

![[Pasted image 20240119231905.png]]


```cpp
using namespace std;

void make_hash(unordered_map<char, int>& doc, string& str){
  for (auto& elem : str){
    if (doc.find(elem) == doc.end()){
      doc.insert({elem, 1});
    }
    else {
      doc.find(elem)->second++;
    }
  }
}

bool generateDocument(string characters, string document) {
  // Write your code here.
  int csize = characters.length();
  int dsize = document.length();
  
  unordered_map<char, int> chars;
  unordered_map<char, int> doc; 
  make_hash(chars, characters);
  make_hash(doc, document);

  for (auto& elem : doc){
    if (chars.find(elem.first)!=chars.end()){
      if (chars.find(elem.first)->second < elem.second) { 
        cout << "key:" << elem.first << endl;
        cout << "chars:" << chars.find(elem.first)->second 
             << "docs:" << elem.second << endl;
        return false;
      }
    }
    else return false;
  }
  return true;
}

```

- 한줄요약: docuement와 characters 각각 들어있는 문자들을 해쉬맵에 등록시키고, 해쉬맵끼리 비교하면 끝! 

---

# 30. First Non Repeating Character

![[Pasted image 20240119231927.png]]


```cpp
using namespace std;

int firstNonRepeatingCharacter(string string) {
  // Write your code here.
  unordered_map<char,int> map;
  int order = 0;
  for (auto& elem : string){
    if (map.find(elem) == map.end()){
      map.insert({elem,order});
    }
    else {
      map.find(elem)->second = -1;
    }
    order++;
  }
  char min_char;
  int min_order = string.length();
  
  for (auto& char_and_order : map){
    if (char_and_order.second > -1){
      if(char_and_order.second < min_order){
        min_char = char_and_order.first;
        min_order = char_and_order.second;
      }
    }
  }

  if (min_order < string.length())
    return min_order;
  else
    return -1;
}

```

- 한줄요약: unordered_map\<char,int> map 에다가 ... string을 forward-iteration 하면서 (1) map에 문자 없으면 문자와 위치 등록, (2) 문자 있으면 int에 -1 넣어서 탈락한거 표시해주면 됨.

---
# Reference

- [AlgoExpert](https://www.algoexpert.io/)
- [Github repository: A collection of solutions for all problem statements on the AlgoExpert Coding Interview platform](https://github.com/das-jishu/algoexpert-data-structures-algorithms)
- [[LeetCode Beginner]]
- [[씹어먹는 C++ 정리노트]]

