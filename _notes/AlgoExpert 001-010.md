---
title: AlgoExpert 001-010
date: 2023-12-24
tags:
  - Algorithm
  - Coding
  - Cplusplus
  - subpage
  - AlgoExpert
---
# AlgoExpert 001-010

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

- 한줄요약 (sorting): sorting 한 다음에 L-pointer, R-pointer 값을 더해서 target-sum보다 작으면 L-pointer를 오른쪽으로, 크면 R-pointer를 왼쪽으로!

- 한줄요약 (hash-table): 해쉬테이블 (`unordered_set<int>` 타입) 만들고, target-sum과 각 num의 sub이 해쉬테이블에 없으면 num을 넣어주고, 있으면 짝 찾은거임!

---
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

- 한줄요약: array와 subsequence의 포인터 2개 만들고, array 포인터를 진행시키다가 subsequence 포인터와 같은 값 있으면 subsequence 포인터 한 칸 진행시키기 반복!

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

- 한줄요약: 이미 정렬 되있으니깐, L-pointer와 R-pointer의 절대값 중에 큰 녀석을 빼서 output 벡터에 넣어주고, L-pointer 값이 이겼으면 오른쪽,  R-pointer 값이 이겼으면 왼쪽으로 이동!

---
# 4. Tournament Winner

![[Pasted image 20231224155225.png]]
![[Pasted image 20231224155238.png]]

2차원 벡터 타입 competitions과 그 결과인 int 벡터 result를 받아서, 
1. i-th result를 읽어서 winner string을 업데이트 
2. winner string 를 key로, int score를 value로 하는 unordered map에 
	1. `scoreboard.find(game_winner) != scoreboard.end()` 체크 
	2. 이미 등록되어 있으면 score를 3점 추가해서 업데이트하고, 
	3. 등록안되있으면 `scoreboard.insert({game_winner,3})` 으로 3점 주면서 등록 
3. 현재 최고 스코어와 비교해서 더 높은 스코어의 winner 업데이트.

```cpp
#include <vector>
#include <unordered_map>
using namespace std;

string tournamentWinner(vector<vector<string>> competitions,
                        vector<int> results) {
  // Write your code here.
  string current_winner = "";
  int current_winner_score = 0;
  unordered_map<string,int> scoreboard;
  for (int i = 0; i < results.size(); i++){
    string game_winner = results[i]? competitions[i][0] : competitions[i][1];
    if (scoreboard.find(game_winner) != scoreboard.end()){
      scoreboard[game_winner]+=3;
    }
    else {
      scoreboard.insert({game_winner,3});
    }

    if (scoreboard[game_winner] >= current_winner_score){
      current_winner = game_winner;
      current_winner_score = scoreboard[game_winner];
    } 
  }
  
  return current_winner;
}
```

- 한줄요약: `unordered_map<string,int>` 타입 해쉬테이블에 승자들만 (1) 등록 안되어있으면 등록하고 점수 기록하고, (2) 등록 되어있으면 승리 점수 accumulate하고, 최대 점수 쌓은 위너의 점수와 리턴할 위너 이름 계속 업데이트하기.

---
# 5. Non-Constructible Change

![[Pasted image 20231231061837.png]]

일단 `sort(coins.begin(), coins.end())`으로 정렬시킨 다음,

1. i-th max-change 는 “현재까지의 코인 (0-th coin ~ i-th coin)으로 빽빽하게 (positive 정수 하나도 빠짐 없이) 조합가능한 가치의 최댓값”.
	1. 따라서 max-change 이하의 모든 값은 당연히 조합 가능해야 함.
	2. 다시 말해서.. i 개의 코인으로 정한 i-th max-change의 경우, 만들 수 있는 possible-change ≤ i-th max-change 가 되는데..
2. (i+1)-th coin 를 추가로 고려해서 (i+1)-th max_change를 구하려면
    1. (possible-change + ((i+1)-th coin)) 값을 만들 수 있으므로
    2. (i+1)-th coin 부터 max_change + (i+1)-th coin 값을 만들 수 있다. 
    3. 따라서 (i+1)-th max_change = max_change + (i+1)-th coin
3. 하지만… (i+1)-th coin 값이  (i-th max_change+2) 같이 (i-th max_change+1) 보다 크면, max_change+1는 조합할 수 없는 숫자가 되므로 max_change+1을 return!!
4. 모든 어레이가 빽빽하게 max_change 를 증가시켰으면 유지했으면 마지막 max_change+1을 return!!

```cpp
#include <vector>
using namespace std;

int nonConstructibleChange(vector<int> coins) {
  // Write your code here.
  sort(coins.begin(),coins.end());
  int max_change = 0;
  for (int coin : coins){
    if (coin > max_change+1) return max_change+1;
    max_change += coin;
  }
  return max_change+1;
}
```

- 한줄요약: max-change 보다 작거나 같은 possible-change 값이 "촘촘해야"한다는 것에 초점을 두고, i-th possible-change와 (i+1)-th possible-change의 범위를 비교해보자. 

---
# 6. Transpose Matrix

![[Pasted image 20231231091120.png]]
![[Pasted image 20231231091139.png]]

2D array,  즉 `vector<vector<int>>` 타입 인자 받아서 transpose 하려면…

1. transpose할 matrix 선언하고
    1. `vector<vector<int>>` 초기화는 …
    2. `vector<T> 변수명(차원 크기, 원소값)` 형식을 재귀적으로 써서,
    3. `vector<vector<int>> 변수명(2번째차원 크기, vector<int>(1번째차원 크기, 0))`
2. 값은 i, j-index를 j, i-index로 바꿔서 대입

```cpp
using namespace std;

vector<vector<int>> transposeMatrix(vector<vector<int>> matrix) {
  // Write your code here.
  cout << matrix.size() << "," << matrix[0].size() << endl;
  
  vector<vector<int>> transposed(matrix[0].size(), vector<int>(matrix.size(),0));
  
  for (int i=0 ; i < matrix.size(); i++){
    for (int j=0 ; j < matrix[0].size(); j++){
      transposed[j][i] = matrix[i][j];
    }
  }
  return transposed;
}
```

- 한줄요약: 0으로 채워진 2D 벡터 선언은 `vector<vector<int>> matrix_name(rows, vector<int>(cols, 0));`

---
# 7. Find Closest Value In BST

![[Pasted image 20231231091509.png]]

Binary search tree랑 target integer value 받아서, BST에서 target에 가장 가까운 값 찾기!

1. 세 가지 경우
    1. current  node의 값과 target 같으면 문제 끝
    2. current node보다 target값 크면, current node나 right subtree 둘 중 하나에 closest value 존재!
    3. current node보다 target값 작으면, current node나 left subtree 둘 중 하나에 closest value 존재!
2. 두 가지 정보를 트랙해야 하는데
    1. target 값과 여태까지 가장 가까운 값을 갖는 closest node 정보
    2. target 값을 향해서 node 옮겨가면서 closest node 업데이트
3. Target값에 딱 도착할 때까지 Leaf에 도착할 때까지 while 문쓰면 exhaustive search!

```cpp
#include <cmath>

class BST {
public:
  int value;
  BST *left;
  BST *right;

  BST(int val);
  BST &insert(int val);
};

// iterative solution
int findClosestValueInBst(BST *tree, int target) {
  // Write your code here.
  int closestValue = tree->value;
  int closestDistance = std::abs(target - tree->value);
  BST* currentNode = tree;
  while(currentNode != nullptr){
    // Exact match
    if (target == currentNode->value) return target;
    // Register the new closest value and distance
    int currrentDistance = std::abs(currentNode->value - target);
    if (currrentDistance < closestDistance){
      closestDistance = currrentDistance;
      closestValue = currentNode->value;
    }
    // Decide next move
    if (target > currentNode->value) currentNode = currentNode->right; 
    else currentNode = currentNode->left; 
  }
  return closestValue;
}
```

- 한줄요약: target을 (1) 현재 노드 값과 비교해서 closest node 업데이트하고, (2)왼쪽 노드와 오른쪽 노드 중에 어느쪽으로 갈지 결정해서 traversing하면서 closest node 업데이트하면 됨.

---
# 8. Branch Sums

![[Pasted image 20231231091950.png]]

Binary tree  받아서, 왼쪽부터 오른쪽까지 branch sum 해주는 함수 만들기!

1. accumulate 함수에서 ...
	1. 현재 node의 값을 왼쪽 node 값에 더하고, 왼쪽 node를 인자로 넣어서 accumulate 함수 호출하기.
	2. 그 다음 현재 node의 값을 오른쪽 node 값에 더하고, 오른쪽 node를 인자로 넣어서 accumulate 함수 호출하기
	3. 그 다음 Leaf에 도착할 경우, accumulated value를 sum vector에 push_back
2. What happen?
    1. accumulate 된 값을 node value에 더하면서 왼쪽으로 traversing 하고, 
    2. 그 경로에서 traverse되지 않은 마지막 right node 가지고 있는 node에서, right node로 간 다음에 다시 왼쪽으로 traversing 
    3. (1) 과 (2) 반복하면서 전체 tree를 traverse!!!

```cpp
using namespace std;

// This is the class of the input root. Do not edit it.
class BinaryTree {
public:
  int value;
  BinaryTree *left;
  BinaryTree *right;

  BinaryTree(int value) {
    this->value = value;
    left = nullptr;
    right = nullptr;
  }
};

void accumulate(BinaryTree* node, vector<int>& sum){
  if (node->left) {
    node->left->value += node->value;
    accumulate(node->left, sum);
  }
  if (node->right) {
    node->right->value += node->value;
    accumulate(node->right, sum);
  }
  if (!(node->left) && !(node->right)) sum.push_back(node->value);
}


vector<int> branchSums(BinaryTree *root) {
  // Write your code here.
  vector<int> sum;
  accumulate(root, sum);
  return sum;
}
```

- 한줄요약: accumulate 함수를 만들고, 현재 노드의 값을 왼쪽/오른쪽 노드의 값에 더한다음 왼쪽/오른쪽 노드에 accumulate함수 재귀호출하면서, sum 포인터의 값을 계속 더해나가면 됨!

---
# 9. Node Depths

![[Pasted image 20231231155248.png]]

1. 현재 노드 깊이를 depth 라고 하고,
2. 현재 노드를 root로 하는 tree의 node 별  depth를 모두 더하는 함수를 `get_depth_sum(BinaryTree* node, int depth)` 라고 하면,
3. 왼쪽 노드의 `get_depth_sum(node->left, depth+1)` 과 오른쪽 노드의 `get_depth_sum(node->right, depth+1)`을 현재 depth와 더하면 끝!
4. 계산은 `get_depth_sum`이 recursively call 되면서 root의 depth sum 구해줌!

```cpp
using namespace std;

class BinaryTree {
public:
  int value;
  BinaryTree *left;
  BinaryTree *right;

  BinaryTree(int value) {
    this->value = value;
    left = nullptr;
    right = nullptr;
  }
};

// recursive approach
int get_depth_sum(BinaryTree* node, int depth){
  int left_depth_sum = 0;
  int right_depth_sum = 0;
  if (node->left != nullptr)
    left_depth_sum = get_depth_sum(node->left, depth+1);
  if (node->right != nullptr)
    right_depth_sum = get_depth_sum(node->right, depth+1);
  return depth + left_depth_sum + right_depth_sum;
}

int nodeDepths(BinaryTree *root) {
  // Write your code here.
  return get_depth_sum(root, 0);
}
```

- 한줄요약: get_depth_sum함수 시작하면, 인자로 받은 depth에 1을 더해서, 다음 레벨 왼쪽/오른쪽 노드의 get_depth_sum함수에 인자로 넘겨줘서 각 노드가 depth를 인자로 갖게 한 다음 (스택에 저장), depth + left_depth_sum (왼쪽노드의 get_depth_sum함수 결과) + right_depth_sum (오른쪽노드의 get_depth_sum함수)를 재귀적으로 리턴!

---
# 10. Evaluate Expression Tree

![[Pasted image 20231231160436.png]]
![[Pasted image 20231231160446.png]]

1. `tree` 구조
	1. Leaf node가 아니면 음의 정수여야하고,
	2. 음의 정수면 그에 해당하는 operation이 있음
		- {-1 : add, -2: sub, -3: div, -4: mult}
2. `result(node)` 함수 구조
	1. Leaf node가 아닐 경우,
		1. 왼쪽노드의 결과값`result(node->left)`과  오른쪽의 결과값`result(node->right)`을  
		2. node value에 해당하는 operation을 취해서 결과값을 return
	2. Leaf node면
		1. 그냥 그 node value를 return

```cpp
using namespace std;

// This is an input class. Do not edit.
class BinaryTree {
public:
  int value;
  BinaryTree *left = nullptr;
  BinaryTree *right = nullptr;

  BinaryTree(int value) { this->value = value; }
};

int result(BinaryTree *node){
  int val = node->value;
  if (val < 0) {  //operator
    switch (val){
      case -1 :
        return result(node->left) + result(node->right);
        break;
      case -2 :
        return result(node->left) - result(node->right);
        break;
      case -3 :
        return result(node->left) / result(node->right);
        break;
      case -4 :
        return result(node->left) * result(node->right);
        break;
      default :
        throw std::invalid_argument( "received non-operation value" );
    }
  }
  else return val;
}

int evaluateExpressionTree(BinaryTree *tree) {
  // Write your code here.
  return result(tree);
}
```

- 한줄요약: 재귀함수가 (1) leaf가 아닌 경우, 양쪽 노드의 재귀함수결과에 현재 node value 읽어서 얻은 operation 적용하고, (2) leaf인 경우는 값 자체가 재귀함수 결과 값!

---
# Reference

- [AlgoExpert](https://www.algoexpert.io/)
- [Github repository: A collection of solutions for all problem statements on the AlgoExpert Coding Interview platform](https://github.com/das-jishu/algoexpert-data-structures-algorithms)
- [[LeetCode Beginner]]
- [[씹어먹는 C++ 정리노트]]