---
title: AlgoExpert 041-050
date: 2024-03-21
tags:
  - Algorithm
  - Coding
  - Cplusplus
  - subpage
  - AlgoExpert
---
# AlgoExpert 041-050

---
# 41. Best Seats

![[Pasted image 20240322085231.png]]

문제요약:
- binary int array 를 받아서,
- 제일 넓은 (zero로 표현된) 빈 자리를 찾고,
- 거기에서 가운데, 기왕이면 앞쪽의 index 찾아서 리턴하는 함수 만들자.
- 너비가 같은 빈자리들이 여러개 있으면 앞쪽 index를 우선시하고.
- 한마디로 "제일 좋은 빈자리 찾아 앉기"

```cpp
using namespace std;

int bestSeat(vector<int> seats) {
  // Print out seats
  for (int i : seats){
    cout << i << " ";
  }
  cout << endl;
  // Detect the longest zeros
  int zeros = 0;
  int maxZeros = 0;
  int maxZeroInd = 0;
  int newZeroInd = 0;
  for (int i = 0; i < seats.size(); i++){
    if (seats[i] == 0){
      if (zeros == 0){
        newZeroInd = i;
      }
      zeros++;
      if (zeros > maxZeros){
        maxZeroInd = newZeroInd;
        maxZeros = zeros;
      }
    }
    else { 
      zeros = 0;
    }    
  }
  
  int seatHere;
  if (maxZeros%2==0) seatHere = maxZeroInd + (maxZeros/2) -1;
  else  seatHere = maxZeroInd + (maxZeros/2);

  cout << "zeros:" << maxZeros << " ind:" << maxZeroInd << " here:" << seatHere << endl;

  
  return seatHere;
}

```

풀이요약:
- zeros가 연속해서 제일 많이 나오는 구간을 찾기 위해, 
- 카운터 zeros에 
	- 0 나올 때마다 값 올려주고,
	- 1 나오면 리셋해주고,
- 최대 빈자리수 maxZeros와 그 수열의 시작 위치인 maxZeroInd 정해서
	- zeros가 reset 되었다가 새로 0 카운트 시작할 때, 그 위치인 newZeroInd를 등록해 두고,
	- 새로 카운트하는 zero가 maxZeros를 넘어서면,
	- maxZeroInd를 newZeroInd로 대체해주면
- 가장 넓은 빈자리의 갯수와 그 시작점을 알 수 있음
- 빈자리 갯수가 홀수, 짝수에 따라 최종적으로 앉을 중간점이 달라지므로 주의해서 리턴하자.

---

# 42. Zero Sum Subarray

![[Pasted image 20240322085254.png]]

문제요약:
- nums 배열 받아서, zero-sum subarray 가 있는지 여부를 리턴하는 함수 만들기.
- zero-sum subarray는 값들을 더하면 0이 되는 서브배열.
- 서브배열은 nums 가운데 연속된 숫자들의 집합이니깐 쉽게 생각하면 됨.

```cpp
using namespace std;
#include <unordered_set>

bool zeroSumSubarray(vector<int> nums) {
  // Write your code here.
  unordered_map<int,int> sums;
  int sum = 0;
  int init = 0;
  for (int i = 0 ; i < nums.size() ; i++){
    sum += nums[i];
    if (sum == 0) return true;
    if (sums.find(sum)!=sums.end()){return true;}
    sums.insert({sum, i});
  }
  return false;
}
```

풀이요약:
- for-loop iteration 하면서 accumulate 시킨 sum을 `unordered_set<int,int> sums`에 저장해보자.
- 그리고 새로운 sum이 sums에서 발견된다면? 그 사이 값들의 합이 0 이라는 뜻! 
- 그러므로 true 리턴!

---
# 43. Missing Numbers

![[Pasted image 20240322085309.png]]

문제요약:
- \[1,\n] 구간에서 반복되지 않는 정수로 이뤄진 인 nums 배열 받자.
	- 여기서 크기 n = (nums의 크기 + 2), 
	- 즉 nums는 \[1,n\] 구간에서 숫자 2개를 빼고 남은 숫자를 섞어 만든 배열!
- 빠진 숫자 두개를 sort해서 리턴하는 함수 만들기.

```cpp
using namespace std;

vector<int> missingNumbers(vector<int> nums) {
  // Write your code here.
  int n = nums.size() + 2;
  vector<bool> mask(n,true);
  for (int num : nums){
    mask[num-1] = false;
  }
  vector<int> result;
  for (int i = 0 ; i < n ; i++ ){
    if (mask[i]) result.push_back(i+1);
  }
  return result;
}
```

풀이요약:
- n 크기 정해주고, n 크기의  마스크 만들어서,
- nums 의 값들에 해당하는 마스크의 인덱스를 false로 만들면,
- missing numbers만 true 상태가 될 것.
- 그걸 모아서 리턴하면 됨.

---
# 44. Majority Element

![[Pasted image 20240322085324.png]]

문제요약:
- 양의 정수 배열 받아서, 
- majority element를 리턴하는 함수 만들자.
	- sorting하면 안되고, constant space 보다 많이 쓰면 안되고.
- majority element는 배열 원소 갯수의 반보다 많이 반복되는 숫자.
- 입력되는 배열은 항상 majority element 있음.

```cpp
using namespace std;

int majorityElement(vector<int> array) {
  int freq = 0;
  int major = array[0];
  for (int val : array){
    // if freq becomes zero,
    // major is not majority value so far. 
    // So try new one
    if (freq == 0) 
      major = val;
    // count maj value
    if (val == major) freq++;
    else freq--;
  }
  return major;
}
```

풀이요약: 어려움...
- majority element는 배열의 반 이상 을 차지하는 값이므로,
- majority element 갯수와 아닌 것의 갯수를 카운트 하면, majority element 가 더 많다.
- 그러면.... majority element 인 것 과 아닌 것을 같은 갯수만큼 지우고 남은 배열에서는, majority element 가 여전히 majority element 여야한다.
- 따라서....
	- 첫번째 원소를 majority element 후보 삼아서
	- 배열을 돌면서 majority element 값과 같으면 freq++, 다르면 freq-- 해서
	- freq 가 0이 되면 후보를 교체하면,
- majority element 후보와 아닌 것들을 동수로 배제시키면서,
- 주어진 인풋 배열은 반드시 majority element 를 포함 하기 때문에
- 유의미한 배열만 남게 될 것이 분명하기 때문에
- 적어도 마지막 후보에서 freq > 0 인 majority element 를 찾을 수 있어야 한다.
- 맞나????? 맞지???

---
# 45. Sweet And Savory

![[Pasted image 20240322085348.png]]

문제요약:
- 음수 = 단맛, 양수 = 짠맛, 숫자 크기는 맛의 강도
- 단맛 하나 짠맛 하나 고르고, 두 flavor 더한게 타겟에 가장 가까운 Best possible pairing 찾아라.
- 합한 맛이 target보다 크면 안됨

```cpp
using namespace std;

vector<int> sweetAndSavory(vector<int> dishes, int target) {
  // Write your code here.
  // sort
  if (dishes.size() <= 1) return {0,0};
  int pos_left, neg_right;
  int pos_right = dishes.size()-1;
  int neg_left = 0;
  sort(dishes.begin(),dishes.end());

  // find pos_left, neg_right
  for (int i = 0; i < dishes.size(); i++) {
    if (dishes[i] >= 0) {
      if (i == 0) {
        return {0,0};
      }
      else
        {pos_left = i; neg_right = i-1; break;}
    }
    else {
      if (i == dishes.size()-1){
        return {0,0};
      }
    }
  }

  int neg = neg_right;
  int pos = pos_left;
  int sum, diff;
  int best_pos = pos_left;
  int best_neg = neg_right;
  int best_diff = INT_MAX;

  // find best pair
  while ((pos <= pos_right) && (neg >= neg_left)){
    sum =  dishes[pos] + dishes[neg];
    diff = target - sum;
    if (diff >= 0) {
      if (best_diff > diff) {
        best_diff = diff;
        best_pos = pos;
        best_neg = neg;
      }
      pos++;
    }
    else {
      neg--;
    }
  }
  if (best_diff == INT_MAX) {
    return {0,0};
  }
  else {
    return {dishes[best_neg], dishes[best_pos]};
  }
}

```

풀이요약:
- 생각보다 잘 안 풀렸는데,
- sort 먼저 하고 나서,
- 음수부와 양수부를 나눠주는 index (neg_left, neg_right, pos_left, pos_right) 정해주자
	- 전부 음수거나 양수면 실패 ( {0,0} 리턴)
- 음수와 양수부를 움직이는 두개의 포인터 neg, pos 를 각각 neg_right, pos_left에 배치하자
	- 왜냐면, sum > target 이면 sum을 줄여야 하니깐, neg를 왼쪽으로 보내면 되고,
	- sum < target이면 sum 늘려야 하니깐, pos를 오른쪽으로 보내면 되서,
	- O(n) 시간에 문제를 풀 수 있게 된다!
- 좀 더 구체적으로, 
	- sum > target 이면 pairing이 안되니깐 그냥 neg를 왼쪽으로 보내기만 하고,
	- sum < target 이면 pairing 이 가능하니깐, best pair 정보를 (best_diff, best_pos, best_neg) 기록하자.
- best pair 기록이 
	- 한번이라도 등록됐다면 `{dishes[best_pos], dishes[best_neg]}` 을 리턴하고,
	- 없었으면 `{0,0}`을 리턴

---
# 46. BST Construction

![[Pasted image 20240322085406.png]]
![[Pasted image 20240322085415.png]]

문제요약:
- Binary Search Tree 클래스 만드세요~

```cpp
#include <vector>
using namespace std;

// Do not edit the class below except for
// the insert, contains, and remove methods.
// Feel free to add new properties and methods
// to the class.
class BST {
 public:
  int value;
  BST* left;
  BST* right;

  BST(int val) {
    value = val;
    left = nullptr;
    right = nullptr;
  }

  BST& insert(int val) {
    // Write your code here.
    // Do not edit the return statement of this method.
    if (val < value) {
      if (left != nullptr) left->insert(val);
      else {
        BST* x = new BST(val);
        left = x;
      }
    }
    else {
      if (right != nullptr) right->insert(val);
      else {
        BST* y = new BST(val);
        right = y;
      }
    }
    return *this;
  }

  bool contains(int val) {
    // Write your code here.
    if (val == value) return true;
    else if (val < value) {
      if (left != nullptr) return left->contains(val);
    }
    else {
      if (right != nullptr) return right->contains(val);
    }
    return false;
  }

  BST& remove(int val, BST* parent = nullptr) {
    // Write your code here.
    // Do not edit the return statement of this method.

    // traverse
    if (val < value) {
      if (left != nullptr) left->remove(val, this);
    }
    else if (val > value) {
      if (right != nullptr) right->remove(val, this);
    }
    else {
      // left & right -> get-min, remove min
      if ((left != nullptr) && (right != nullptr)){
        value = right->get_min();
        right->remove(value, this);
      }
      // left or right , parent or not
      else if (parent == nullptr && left != nullptr) {
        value = left->value;
        right = left->right;
        left = left->left;
      }
      else if (parent == nullptr && right != nullptr) {
        value = right->value;
        left = right->left;
        right = right->right;
      }
      else if (parent == nullptr && left == nullptr && right == nullptr) {
        
      }
      else if (parent != nullptr) {
        if (parent->left == this) parent->left = left != nullptr ? left : right;
        else if (parent->right == this) parent->right = left != nullptr ? left : right;
      }
    }
    return *this;
  }

  int get_min() {
    if (left == nullptr) return value;
    else return left->get_min();
  }
};

```

풀이요약:
- insert
	- 작으면 왼쪽으로 / 크면 오른쪽으로
	- nullptr이면 새로 BST instance 만들어서 왼쪽/오른쪽에 달기
- contains
	- 작으면 왼쪽으로 / 크면 오른쪽으로
	- 같은 값 찾으면 true 리턴
- remove
	- 작으면 왼쪽으로 / 크면 오른쪽으로
	- 찾았으면,
		- left, right node 둘 다 달려있으면 $\rightarrow$ right node 쪽에서 최솟값 찾아서 대체하고, 그 값 remove
			- 최솟값 찾는건, left node 쭉 따라가다가 leaf node의 값
		- left나 right node 둘 중 하나만 있으면
			- parent 없는 root node 인 경우 $\rightarrow$ left 또는 right node가 현재 노드를 대체\
			- parent 있는 경우 $\rightarrow$ left 또는 right node를 parent node의 left/right 포인터가 가리키도록 연결

---
# 47. Validate BST

![[Pasted image 20240322085438.png]]

문제요약:
- BST 받아서 valid 여부 판단하는 함수 만들기

```cpp
class BST {
 public:
  int value;
  BST* left;
  BST* right;

  BST(int val);
  BST& insert(int val);
};

bool validateBst_range(BST* tree, int min, int max);

bool validateBst(BST* tree){
  return validateBst_range(tree, INT_MIN, INT_MAX);
}

bool validateBst_range(BST* tree, int min, int max) {
  bool leftValid = true;
  bool rightValid = true;

  if ((tree->value < min) || (tree->value >= max)) return false;
  
  if (tree->left != nullptr) {
    leftValid = validateBst_range(tree->left, min, tree->value);
  }
  if (tree->right != nullptr) {
    rightValid = validateBst_range(tree->right, tree->value, max);
  }
  return leftValid && rightValid;
}

```

풀이요약:
- Valid 여부는 ...
	- 현재 노드의 왼쪽에 있는 모든 노드는, 현재 노드의 값보다 작은 값을 가져야 하고,
	- 현재 노드의 오른쪽에 있는 모든 노드는, 현재 노드의 값보다 크거나 같은 값을 가져야 한다.
- 따라서 한 노드가 valid 하려면...
	- 양쪽 노드가 다 nullptr이거나 valid 해야하는데,
	- 왼쪽 노드를 validate 할 때는, 
		- 현재 노드값을 최대값으로 가져가고, 
		- 현재 노드값의 최소값을 왼쪽 노드의 최소값으로 그대로 전달해서 
		- validate 해야한다.
	- 오른쪽 노드를 validate 할 때는, 
		- 현재 노드값의 최대값을 오른쪽 노드의 최대값으로 그대로 전달하고, 
		- 현재 노드값을 최솟값으로 가져가서 
		- validate 해야한다.
- 예를 들어,
	- 현재 노드의 왼쪽 노드가 다시 오른쪽 노드를 가진다면,
	- 현재 노드값이 왼쪽 노드의 최대값, 그리고 그 노드의 오른쪽 노드값의 최댓값은 여전히 현재 노드값이 되도록 현재 노드값을 전달해야한다.

---
# 48. BST Traversal

![[Pasted image 20240322085504.png]]
![[Pasted image 20240322085520.png]]

문제요약:
- BST 받아서 in-order, pre-order, post-order traverse 하면서 array 에 숫자 적는 함수 세 개 만들기.

```cpp
#include <vector>
using namespace std;

class BST {
 public:
  int value;
  BST* left;
  BST* right;

  BST(int val);
};

void inOrderTraverse(BST* tree, vector<int>& array) {
  if (tree->left != nullptr) inOrderTraverse(tree->left, array);
  array.push_back(tree->value);
  if (tree->right != nullptr) inOrderTraverse(tree->right, array);
}

void preOrderTraverse(BST* tree, vector<int>& array) {
  array.push_back(tree->value);
  if (tree->left != nullptr) preOrderTraverse(tree->left, array);
  if (tree->right != nullptr) preOrderTraverse(tree->right, array);  
}

void postOrderTraverse(BST* tree, vector<int>& array) {
  if (tree->left != nullptr) postOrderTraverse(tree->left, array);
  if (tree->right != nullptr) postOrderTraverse(tree->right, array);  
  array.push_back(tree->value);
}

```

풀이요약:
- in-order는 왼쪽 in-order-traverse + 현재 노드값 + 오른쪽 in-order-traverse
- pre-order는 현재 노드값 + 왼쪽 pre-order-traverse + 오른쪽 pre-order-traverse
- post-order는 왼쪽 post-order-traverse + 오른쪽 post-order-traverse + 현재 노드값

---
# 49. Min Height BST

![[Pasted image 20240322085538.png]]
![[Pasted image 20240322085548.png]]

문제요약:
- 정렬된, 모두 다른 정수로 이루어진 배열을 받아서
- Height가 가장 작은 BST의 root node를 리턴하는 함수 만들기

```cpp
using namespace std;

class BST {
 public:
  int value;
  BST* left;
  BST* right;

  BST(int value) {
    this->value = value;
    left = nullptr;
    right = nullptr;
  }

  void insert(int value) {
    if (value < this->value) {
      if (left == nullptr) {
        left = new BST(value);
      } else {
        left->insert(value);
      }
    } else {
      if (right == nullptr) {
        right = new BST(value);
      } else {
        right->insert(value);
      }
    }
  }
};

BST* minHeightBst_range(vector<int> & array, int lp, int rp);

BST* minHeightBst(vector<int> array) {
  int lp = 0;
  int rp = array.size()-1;
  return minHeightBst_range(array, lp, rp);
}

BST* minHeightBst_range(vector<int> & array, int lp, int rp) {
  int mp = (lp + rp)/2;
  BST* root = new BST(array[mp]);
  if (rp-lp>=2){
    root->left = minHeightBst_range(array, lp, mp-1);
    root->right = minHeightBst_range(array, mp+1, rp);
  }
  else if (rp-lp ==1){
    root->right = minHeightBst_range(array, mp+1, rp);
  }
  return root;
}
```

풀이요약:
- 중간 값으로 root를 만들고 그 왼쪽 노드와 오른쪽 노드는, 중간값의 왼쪽 배열, 중간값으 오른쪽 배열로 만들면 된다.
- 이걸 recursive하게 수행해 줄 함수는 입력 array의 reference와 subarray를 지정해줄 왼쪽, 오른쪽 포인터를 인자로 받아서,
- 반복 실행해주면 됨.
- 이렇게 하면 앞의 BST traversal 문제에서 소개된 pre-order로 traverse 하는 셈.

---
# 50. Find Kth Largest Value In BST

![[Pasted image 20240322085605.png]]
![[Pasted image 20240322085613.png]]


문제요약:
- BST랑 양정수 k 받아서, BST에서 k번째 큰 숫자를 꺼내는 함수 만들기

```cpp
using namespace std;

// This is an input class. Do not edit.
class BST {
 public:
  int value;
  BST* left = nullptr;
  BST* right = nullptr;

  BST(int value) { this->value = value; }
};

void inOrderTraverse(BST* tree, vector<int> & array){
  if (tree->left != nullptr) inOrderTraverse(tree->left, array);
  array.push_back(tree->value);
  if (tree->right != nullptr) inOrderTraverse(tree->right, array);
}

int findKthLargestValueInBst(BST* tree, int k) {
  vector<int> array;
  inOrderTraverse(tree, array);
  int k_th_largest = array.size()-k;
  return array[k_th_largest];
}


```

풀이요약:
- inOrderTraverse 하면서 만들어진 리스트에서 k-번째 큰 숫자 꺼내면 됨. 
- 그러면 O(n) time, O(n) space
- O(1) space 쓰고 싶으면, 반대방향으로 traversing 하면서 count 하면 됨.


---
# Reference

- [AlgoExpert](https://www.algoexpert.io/)
- [Github repository: A collection of solutions for all problem statements on the AlgoExpert Coding Interview platform](https://github.com/das-jishu/algoexpert-data-structures-algorithms)
- [[LeetCode Beginner]]
- [[씹어먹는 C++ 정리노트]]

