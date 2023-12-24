---
title: AlgoExpert 001-010
date: 2023-12-24
tags:
  - Algorithm
  - Coding
  - Cplusplus
---
# 1. Two Number Sum

![[Pasted image 20231223231623.png]]
![[Pasted image 20231223231634.png]]

Naive 하게 각 숫자별로 target sum을 만들어주는 숫자를 찾으면 ...

```cpp
// Double loops 
// O(n^2) time, O(1) space
#include <vector>
using namespace std;

vector<int> twoNumberSum(vector<int> array, int targetSum) {
  // Write your code here.
  for (vector<int>::size_type i=0; i<(array.size()-1); i++){
    for (vector<int>::size_type j=i+1; j<(array.size()); j++){
      if (targetSum == array[i]+array[j]){
        return vector<int>{array[i],array[j]};
      }
    }
  }
  return {};
}
```

$O(n^2)$ 가 너무 비싸니깐 $O(nlogn)$ sorting과 $O(n)$ LR pointer를 곁들여 써보자. 
1. `sort(array.begin(), array.end())` 로 벡터 정렬하기
2. L-pointer와 R-pointer 값을 더했을 때
	1. target sum과 같으면 결과 벡터에 추가
	2. target sum보다 작으면 L-pointer를 오른쪽을 한 칸 이동
	3. target sum보다 크면 R-pointer를 왼쪽으로 한 칸 이동

```cpp

// Sort and LR pointer
// O(nlogn)
#include <vector>
#include <algorithm>
using namespace std;

vector<int> twoNumberSum(vector<int> array, int targetSum) {
  // Write your code here.
  sort(array.begin(), array.end());
  int left = 0;
  int right = array.size()-1;
  while(left<right){
    int sum = array[left]+array[right];
    if (sum == targetSum){
      return vector<int>{array[left],array[right]};
    }
    else if (sum > targetSum) right -= 1;
    else left += 1;
  }
  return {};
}
```

$O(nlogn)$ 도 비싸면 해쉬 테이블 써서 $O(n)$으로 줄여보자.
1. `unordered_set<int>`타입 해쉬 테이블 만들고,
2. 각 숫자 num에 대해서 target sum 과의 차이 값 sub이 
	1. 해쉬 테이블에 없으면 num을 해쉬 테이블에 넣고, 
	2. 해쉬 테이블에 있으면 (num, sub) pair를 결과 벡터에 넣자 
3. 문제는 하나의 pair만 찾으면 됐지만, 여러개 찾아야 하면 map으로 된 벡터를 만들어서 emplace_back시키자

```cpp
// Hash table
// O(n) time, O(n) space
#include <vector>
#include <unordered_set>
using namespace std;

vector<int> twoNumberSum(vector<int> array, int targetSum) {
  // Write your code here.
  unordered_set<int> ht;
  for (int num : array){
    int sub = targetSum - num;
    if (ht.find(sub)!=ht.end()){
      return vector<int>{num, sub};
    }
    else {ht.insert(num);}
  }
  return {};
}

```

# 2. Validate Subsequence

![[Pasted image 20231224145948.png]]
![[Pasted image 20231224145958.png]]

Sequence가 Array의 subsequence인걸 확인하는 방법.
1. 각 Array의 값이 Sequence의 0-th element 인지 확인 (여기서 0 = subsequence element id)
	1. 아니면 다음 값 체크
	2. 맞으면 subsequence element id를 하나 올리면서 Sequence의 첫 값이 Array에 들어있음 표시
2. 다음 순서의 Array 값들 체크하면서 id 올리다가
3. Array의 끝에서 도달하기 전에 id가 Sequence 크기와 같아지면, Sequence가 Array의 subsequence 라는 뜻!

```cpp
//O(n) time, O(1) space
//for version

using namespace std;

bool isValidSubsequence(vector<int> array, vector<int> sequence) {
  // Write your code here.
  int id = 0;
  for (int val : array){
    if (val == sequence[id]) id++;
  }
  if (id == sequence.size()) return true;

  return false;
}
```

똑같은걸 `while`문으로 만들어보면 ...

```cpp
//O(n) time, O(1) space
//while version
#include <iostream>
using namespace std;

bool isValidSubsequence(vector<int> array, vector<int> sequence) {
  // Write your code here.
  int arr_idx = 0;
  int seq_idx = 0;
  while (arr_idx < array.size() && seq_idx < sequence.size()) {
    if (array[arr_idx]==sequence[seq_idx]){
      seq_idx++;
    }
    arr_idx++; 
  }
  if (seq_idx == sequence.size())
      return true;
  return false;
}
```


---

# 3. Sorted Squared Array

![[Pasted image 20231224152828.png]]

그냥 생각하면 제곱하고 정렬하면 됨.

```cpp
//O(nlogn) time
#include <vector>
using namespace std;

vector<int> sortedSquaredArray(vector<int> array) {
  // Write your code here.
  vector<int> output;
  for (int num : array){
    output.push_back(pow(num,2));
  }
  sort(output.begin(),output.end());
  
  return output;
}
```

Sorting 을 한번이라도 쓰면... $O(nlogn)$ time을 쓰게 되는데, 이미 정렬되어있는 녀석을 잘 활용하면 다시 sorting 하지 않아도 됨
1. array가 이미 sorting 되어 있기 때문에
2. negative integer는 제곱하면 큰 숫자가 맨 왼쪽, non-negative integer는 제곱하면 큰 숫자가 오른쪽에 있게 됨
3. 그래서 양 쪽 끝에 L-pointer, R-pointer 두고 제곱해서 큰 값을 결과 벡터에 맨 뒤부터 넣어주고,
4. 소비된 값의 pointer는 
	1. L-pointer는 오른쪽으로,
	2. R-pointer는 왼쪽으로 옮기면 됨.
5. 모든 값이 소비될 때까지 (결과 벡터의 포인터인 O-pointer 가 0 아래가 될 때까지) `while` loop으로 반복

```cpp
//O(n) time
#include <vector>
#include <iostream>
using namespace std;

vector<int> sortedSquaredArray(vector<int> array) {
  // Write your code here.
  int Lp = 0;
  int Rp = array.size()-1;
  int Op = array.size()-1;
  vector<int> output(array.size(),0);
  while(Op>=0){
    if (abs(array[Lp]) > abs(array[Rp])) output[Op] = pow(array[Lp++],2);
    else output[Op] = pow(array[Rp--],2);
    Op--;
  }
  for (int num : output) cout<<num<<endl;
  return output;
}
```

---





---
# Reference

- [AlgoExpert](https://www.algoexpert.io/)
	- [github](https://github.com/das-jishu/algoexpert-data-structures-algorithms)
	- 
- [[LeetCode Beginner]]
