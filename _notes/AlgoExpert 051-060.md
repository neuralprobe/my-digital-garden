---
title: AlgoExpert 051-060
date: 2024-04-08
tags:
  - Algorithm
  - Coding
  - Cplusplus
  - subpage
  - AlgoExpert
---
# AlgoExpert 051-060

---
# 51.  Reconstruct BST

![[Pasted image 20240408223454.png]]
![[Pasted image 20240408223504.png]]

문제요약:
- BST를 pre-order traversal 하는 방법: 현재노드값 등록 $\rightarrow$ 왼쪽으로 pre-order traversal  $\rightarrow$ 오른쪽으로 pre-order traversal
- 주어진 배열은 어떤 BST를 pre-order traversal 하면서 만든 배열이다.
- 배열을 보고 원래 BST를 만드는 함수를 짜보자.

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

BST* reconstruct(vector<int>& arr, int& i, int min, int max) {
  if (i >= arr.size()) return nullptr;
  if (arr[i] < min || arr[i] >= max) return nullptr;
  BST* node = new BST(arr[i++]);
  node->left = reconstruct(arr, i, min, node->value);
  node->right = reconstruct(arr, i, node->value, max);
  return node;
}

BST* reconstructBst(vector<int> preOrderTraversalValues) {
  int min = INT_MIN;
  int max = INT_MAX;
  int i = 0;
  BST* node = reconstruct(preOrderTraversalValues, i, min, max);  
  return node;
}
```

풀이요약:
- 주어진 배열 `arr`, counter `i`를 reference 로 받고, 
- BST traversal의 경계조건인 `min`, `max` 를 받는 함수 `reconstruct`를 만들어서,
	- 왼쪽 갈 때는 min은 유지, max 는 current value
	- 오른쪽 갈 때는 min은 current value, max 는 유지
- `nullptr`이 되는 범위조건 정해주고,
- 유형의 node를 만들 수 있는 경우 `arr[i]` 값으로 초기화 해주고 `i`를 증가시켜서
- 왼쪽 pre-order traversal하고 오른쪽 pre-order traversal하면 됨.
- 주의점:
	- `i`  값이 변할 땐, `arr[i]` 체크하기 전에 `i` 범위부터 제한해야함.

---

# 52.  Invert Binary Tree

![[Pasted image 20240408223534.png]]

문제요약:
- BST 뒤집기

```cpp
#include <vector>
using namespace std;

class BinaryTree {
 public:
  int value;
  BinaryTree* left;
  BinaryTree* right;

  BinaryTree(int value);
  void insert(vector<int> values, int i = 0);
  void invertedInsert(vector<int> values, int i = 0);
};

void invertBinaryTree(BinaryTree* tree) {
  BinaryTree* temp = tree->left;
  tree->left = tree->right;
  tree->right = temp;
  if (tree->left != nullptr) invertBinaryTree(tree->left);
  if (tree->right != nullptr) invertBinaryTree(tree->right);  
}
```

풀이요약:
- 걍 뒤집으면 됨

---
# 53.  Binary Tree Diameter

![[Pasted image 20240408223552.png]]

문제요약:
- BST 받아서 diameter 구하는 함수짜기
- Diameter는 현재 트리에서 한 줄 긋기로 가장 길게 연결할 수 있는 길이

```cpp
using namespace std;

// This is an input class. Do not edit.
class BinaryTree {
 public:
  int value;
  BinaryTree* left;
  BinaryTree* right;

  BinaryTree(int value) {
    this->value = value;
    left = nullptr;
    right = nullptr;
  }
};

struct TreeInfo {
  int height;
  int diameter;
};

TreeInfo getTreeInfo(BinaryTree* tree) {
  TreeInfo left, right;
  if (tree == nullptr) return TreeInfo{0,0};
  else {
    left = getTreeInfo(tree->left);
    right = getTreeInfo(tree->right);
  }
  int height = max(left.height, right.height)+1;
  int longestLineViaCurrentRoot = left.height + right.height;
  int diameter = max(max(left.diameter, right.diameter),longestLineViaCurrentRoot);
  return TreeInfo{height,diameter};
}

int binaryTreeDiameter(BinaryTree* tree) {
  return getTreeInfo(tree).diameter;
}
```

풀이요약:
- BST의 Height는? (node의 높이: leaf node's height = 1)
	- 왼쪽 노드 존재할 경우, 왼쪽 노드 height 보다 +1 (nullptr 이면 0)
	- 오른쪽 노드 존재할 경우, 오른쪽 노드 height 보다 +1 (nullptr 이면 0)
	- 둘 중에 큰 것
- BST의 Diameter는?
	- 왼쪽 노드 존재할 경우, 왼쪽노드의 diameter, 
	- 오른쪽 노드 존재할 경우, 오른쪽노드의 diameter, 
	- 왼쪽노드 height 와 오른쪽노드 height의 합,
	- 이 세 숫자중에 가장 큰 수
- 따라서, root의 height 와 diameter를 구해주는 함수를 재귀적으로 호출하면 됨

---
# 54.  Find Successor

![[Pasted image 20240408223613.png]]
![[Pasted image 20240408223626.png]]

문제요약:
- Binary tree (부모 노드를 향한 포인터 포함) 와
- 그 Tree에 들어있는 node 를 받아서,
- 그 노드의 successor를 리턴하는 함수 만들기
	- Successor = In-order tree-traversal 할 때, 현재 노드 다음에 visit하게 되는 노드
	- Successor 없으면 None/null 리턴하면 됨

```cpp
using namespace std;

// This is an input class. Do not edit.
class BinaryTree {
 public:
  int value;
  BinaryTree* left = nullptr;
  BinaryTree* right = nullptr;
  BinaryTree* parent = nullptr;

  BinaryTree(int value) { this->value = value; }
};


void inOrderTraverse(BinaryTree* tree, BinaryTree* node, BinaryTree* & result) {
  if (tree->left != nullptr) inOrderTraverse(tree->left, node, result);
  if (result == node) result = tree;
  if (tree == node) result = node;
  if (tree->right != nullptr) inOrderTraverse(tree->right, node, result);
}

BinaryTree* findSuccessor(BinaryTree* tree, BinaryTree* node) {
  BinaryTree* result = nullptr;
  inOrderTraverse(tree, node, result);
  if (result != node) return result;
  else return nullptr;
}
```

풀이요약:
- inOrderTraverse 시키면서,
- tree랑 node가 같으면, 그 다음 traverse 시킬때의 node를 return하면됨
	- tree랑 node가 같으면, result 레퍼런스를 "준비" 상태로 만들고
	- result 레퍼런스가 "준비" 상태일 때 traverse 되는 node를 return 하면 됨.
- O(n) time, O(1) space
- O(height) time 하고 싶으면, 각 경우 따져서 node에서 내려가거나 올라가야 함
	- node->right가 존재하면 
		- node->right 또는 그보다 왼쪽에 있는 node를 리턴하면 됨 (while문으로 가능)
	- 아닐 경우, 
		- node 보다 더 오른쪽에 있는 첫번째 parent가 존재하면 그 parent를 리턴
		- (node와 그 부모 node 들 중에 하나라도 오른쪽으로 향하는 node 있으면 그 노드, 없으면 nullptr)

---
# 55.  Height Balanced Binary Tree

![[Pasted image 20240408223649.png]]

문제요약:
- Binary tree의 root node 받아서,
- Height balanced 일 경우 true 리턴하는 함수 만들기
- Height balanced?  모든 노드에 대해, 왼쪽 트리와 오른쪽 트리의 높이차이가 1밖에 안나는 경우.

```cpp
using namespace std;

// This is an input class. Do not edit.
class BinaryTree {
 public:
  int value;
  BinaryTree* left = nullptr;
  BinaryTree* right = nullptr;

  BinaryTree(int value) { this->value = value; }
};

struct nodeInfo {
  bool balanced = true;
  int height = 0;
};

nodeInfo get_balanced_height(BinaryTree* tree) {
  nodeInfo leftResult;
  nodeInfo rightResult;
  nodeInfo result;
  int leftHeight = 0;
  int rightHeight = 0;
  // test the left/right node
  if (tree->left != nullptr) leftResult = get_balanced_height(tree->left);
  if (tree->right != nullptr) rightResult = get_balanced_height(tree->right);
  // test the current node
  if (tree->left == nullptr && tree->right == nullptr) result.height = 1;
  if (tree->left != nullptr) leftHeight = leftResult.height + 1;
  if (tree->right != nullptr) rightHeight = rightResult.height + 1;
  result.height = max(leftHeight,rightHeight);
  result.balanced = (abs(leftHeight-rightHeight)<=1) && leftResult.balanced && rightResult.balanced;
  return result;
}


bool heightBalancedBinaryTree(BinaryTree* tree) {
  return get_balanced_height(tree).balanced;
}
```

풀이요약:
- Post-order traversing (left-right-current 순서) 하면서
- 왼쪽 subtree의 높이랑 밸런스 여부 leftResult에 저장하고,
- 오른쪽 subtree의 높이랑 밸런스 여부 rightResult에 저장하고,
- 왼쪽 오른쪽 높이 비교해서, current node의 높이랑 밸런스 여부를 리턴하면 됨.
- 밸런스 여부는 
	- 왼쪽도 밸런스 맞아야하고, 
	- 오른쪽도 밸런스 맞아야하고, 
	- 왼쪽 오른쪽 높이도 같아야함.
- 이런거 하려면, 높이 정보와 밸런스 정보가 같이 재귀적으로 리턴되어야 하니깐 struct 만들어 주면 좋음.

---
# 56.  Merge Binary Trees

![[Pasted image 20240408223710.png]]

문제요약:
- Binary tree 두개 받아서 merge 시켜서 리턴하는 함수 만들기
- 오버랩되는 노드들은,  그 노드값을 더해서 merge된 node의 값으로 삼아야 함.

```cpp
using namespace std;

// This is an input class. Do not edit.
class BinaryTree {
 public:
  int value;
  BinaryTree* left = nullptr;
  BinaryTree* right = nullptr;

  BinaryTree(int value) { this->value = value; }
};

BinaryTree* mergeBinaryTrees(BinaryTree* tree1, BinaryTree* tree2) {
  if (tree1==nullptr && tree2==nullptr) return nullptr;
  if (tree1!=nullptr && tree2!=nullptr) {
    BinaryTree* root = new BinaryTree(tree1->value + tree2->value);
    root->left = mergeBinaryTrees(tree1->left, tree2->left);
    root->right = mergeBinaryTrees(tree1->right, tree2->right);
    return root;
  }
    if (tree1!=nullptr && tree2==nullptr) {
    BinaryTree* root = new BinaryTree(tree1->value);
    root->left = mergeBinaryTrees(tree1->left, nullptr);
    root->right = mergeBinaryTrees(tree1->right, nullptr);
    return root;
  }
    if (tree1==nullptr && tree2!=nullptr) {
    BinaryTree* root = new BinaryTree(tree2->value);
    root->left = mergeBinaryTrees(nullptr,tree2->left);
    root->right = mergeBinaryTrees(nullptr,tree2->right);
    return root;
  }
  return nullptr;
}

```

풀이요약:
- merge함수를 recursive하게 쓰되,
- 두 입력 노드가
	- 왼쪽 노드도 있고 오른쪽 노드도 있으면
		- 새 노드 생성해서 그 값을 두 노드의 합으로 정해주고,
		- 새 노드의 left, right를 왼쪽 노드와 오른쪽 노드의 merge 함수결과로 연결하면 됨.
	- 왼쪽 노드 또는 오른쪽 노드만 있으면
		- 새 노드 생성해서 그 값을 존재하는 기존 노드의 값으로 정해주고
		- 새 노드의 left, right를 존재하는 노드의 merge 함수결과로 연결하면 됨.
	- 둘 다 없으면
		- nullptr 리턴하면 됨

---
# 57.  Symmetrical Tree

![[Pasted image 20240408223727.png]]

문제요약:
- Binary Tree 받아서,
- symmetrical 한지 판단하는 함수 만들기
	- 양쪽 subtree가 서로의 mirror여야 함.

```cpp
using namespace std;

// This is an input class. Do not edit.
class BinaryTree {
 public:
  int value;
  BinaryTree* left = nullptr;
  BinaryTree* right = nullptr;

  BinaryTree(int value) { this->value = value; }
};

void inOrderTraversal_left_to_right(BinaryTree* tree, vector<int> & array){
  if (tree->left != nullptr) inOrderTraversal_left_to_right(tree->left, array);
  array.push_back(tree->value);
  if (tree->right != nullptr) inOrderTraversal_left_to_right(tree->right, array);
}


void inOrderTraversal_right_to_left(BinaryTree* tree, vector<int> & array){
  if (tree->right != nullptr) inOrderTraversal_right_to_left(tree->right, array);
  array.push_back(tree->value);
  if (tree->left != nullptr) inOrderTraversal_right_to_left(tree->left, array);
}

bool symmetricalTree(BinaryTree* tree) {
  // Write your code here.
  vector<int> array1, array2;
  if (tree == nullptr) return false;
  if (tree->left != nullptr)  inOrderTraversal_left_to_right(tree->left,array1);
  if (tree->right != nullptr) inOrderTraversal_right_to_left(tree->right,array2);
  int i = 0;
  if (array1.size()!= array2.size()) return false;
  while(i < array1.size()){
    if (array1[i] != array2[i]) return false;
    i++;
  }
  return true;
}
```

풀이요약:
- 왼쪽 subtree는 오른쪽으로 in-order traversing
- 오른쪽 subtree는 왼쪽으로 in-order traversing
- 기록한 어레이 비교하면 됨. 
- O(n) time, O(n) space.
- O(n) time, O(h) space 하려면,
	- isMirror(left subtree , right subtree) ?
		- left node 값 == right node 값
		- & isMirror(left-left subtree , right-right subtree) 
		- & isMirror(left-right subtree , right-left subtree) 
	- 이렇게 recursion 하면 됨.

---
# 58.  Split Binary Tree

![[Pasted image 20240408223741.png]]

문제요약:
- 최소 node 하나있는 Binary tree 받아서,
- Edge 하나 끊어서 만든 두개의 트리의 합이 같도록 쪼개서, 
- 그 합을 리턴하는 함수 만들기.
- 합이 같은 두 트리로 쪼갤 수 없으면 0 리턴하기

```cpp
using namespace std;

// This is an input class. Do not edit.
class BinaryTree {
 public:
  int value;
  BinaryTree* left = nullptr;
  BinaryTree* right = nullptr;

  BinaryTree(int value) { this->value = value; }
};

void buildSumTree (BinaryTree* & tree){
  int left = 0;
  int right = 0;
  if (tree->left != nullptr) {buildSumTree(tree->left); left = tree->left->value;}
  if (tree->right != nullptr) {buildSumTree(tree->right); right = tree->right->value;}
  tree->value = tree->value + left + right;
}

void inOrderTraversal(BinaryTree* tree, int total, int & result){
  if (tree->left != nullptr & result == 0) inOrderTraversal(tree->left, total, result);
  if (result == 0) result = (tree->value == (total - tree->value))? tree->value : 0;
  if (tree->right != nullptr & result == 0) inOrderTraversal(tree->right, total, result);
}

int splitBinaryTree(BinaryTree* tree) {
  buildSumTree(tree);
  int result = 0;
  inOrderTraversal(tree, tree->value, result);
  return result;
}

```

풀이요약:
- 일단 각 Node는 원래 node value와 subtree 값들의 합을 더한 값이 되도록 업데이트
- 그러면 root node 는 total sum 의미 함
- in-order-traverse 하면서 node.value == (root.value - node.value) 체크해서
- 맞으면 그 노드를 split 해내면 되는 거니깐,
- 그 node.value를 리턴하면 됨
- 레퍼런스 전달 잘 쓰면 좋음.

---
# 59.  Max Subset Sum No Adjacent

![[Pasted image 20240408223756.png]]

문제요약:
- postive array 받아서
- 인접하지 않은 원소의 최대합을 리턴하는 함수 만들기

```cpp
//Without Dynamic Programming 

#include <vector>

using namespace std;

int maxSum(vector<int> & array, int i) {
  if (i < 0) return 0;
  if (i == 0) return array[0];
  if (i == 1) return array[1];  
  int result = max(maxSum(array, i-2), maxSum(array, i-3)) + array[i];
  return result;
}

int maxSubsetSumNoAdjacent(vector<int> array) {
  // Write your code here.
  int result = max(maxSum(array, array.size()-1),maxSum(array, array.size()-2));

  return result;
}
// With Dynamic Programming

#include <vector>

using namespace std;

int maxSum(vector<int> & array, int i, vector<int> & array_result) {
  int result;
  if (i < 0) return 0;
  if (i == 0) {array_result[i] = array[0]; return array[0];}
  if (i == 1) {array_result[i] = array[1]; return array[1];}
  if (array_result[i]!=0){ result = array_result[i];}
  else {
    result = max(maxSum(array, i-2, array_result), maxSum(array, i-3, array_result)) + array[i];
    array_result[i] = result;
  }
  return result;
}

int maxSubsetSumNoAdjacent(vector<int> array) {
  // Write your code here.
  vector<int> array_result(array.size(),0);
  int result = max(maxSum(array, array.size()-1, array_result),maxSum(array, array.size()-2, array_result));

  return result;
}
```

풀이요약:
- 맞닿아 있으면 안된깐,
- 결과 값은 
- array.size()-1 인덱스까지의 최대합이나 array.size()-2 인덱스까지의 최대합 가운데 큰 값.
- 그리고, i-인덱스의 최대합은 (i-2 인덱스까지의 최대합이나 i-3 인덱스까지의 최대합 가운데 큰 값) 더하기 i-인덱스의 값.
	- 붙어있는 원소는 안되니깐 i-1 인덱스는 포함되면 안됨. 
	- i-4는 i-2인덱스 최대합에 포함될 수 있으니깐 판단에 포함되면 안됨.
- 요렇게 큰 문제가 작은문제로 쪼개지는 형식으로 recursion 하면 됨.
- 문제는, 풀었던 작은 문제를 또 풀어야 할 수 있으니깐,
- 이미 풀었던 작은 문제의 답을 array_result에 적어 놓는,
- Dynamic programming 하면 됨.

---
# 60.  Number of Ways to Make Change

![[Pasted image 20240408223809.png]]

문제요약:
- 동전의 종류를 표시하는 다른 양정수로 이루어진 배열과
- 목표 금액을 표시하는 0 이상의 정수를 받아서,
- 주어진 동전 종류를 이용해서 목표금액을 만드는 방법의 갯수를 리턴하는 함수 만들기

```cpp
#include <vector>
using namespace std;

int numberOfWaysToMakeChange(int n, vector<int> denoms) {
  // Write your code here.
  vector<int> ways(n+1,0);
  ways[0] = 1;
  for (int coin : denoms){
    for (int i = 1; i <= n; i++){
      if ((i-coin) >=0) {
        ways[i] += ways[i-coin];
      }
    }
  }
  return ways[n];
}
```

풀이요약:
- n원을 denoms 로 만드는 방법?
- 0원은 동전을 아예 안쓰는 방법 1가지가 있고,
- 첫번째 동전 `coin = denoms[0]`으로 i 원 만드는 방법
	- `(i-coin)>=0` 일 때 `ways[i-coin]` 만큼.
- 두번째 동전 `coin = denoms[1]`으로 i 원 만드는 방법
	- `(i-coin)>=0` 일 때 `ways[i-coin]` 만큼 추가.
- 세번째 동전 `coin = denoms[2]`으로 i 원 만드는 방법
	- `(i-coin)>=0` 일 때 `ways[i-coin]` 만큼 추가.
- 주의점: for loop 순서 바꾸면 안됨.
	- 왜? i-loop 내부에 coin-loop 돌면,  i 원 만드는 방법 셀 때 coin을 조합하는 순서까지 고려해서 over-count 하게 됨.
	- coin-loop 내부에 i-loop 돌면, coin 종류별로 i원 만드는 방법 추가되는거라 coin의 조합순서는 균일해짐.

---
# Reference

- [AlgoExpert](https://www.algoexpert.io/)
- [Github repository: A collection of solutions for all problem statements on the AlgoExpert Coding Interview platform](https://github.com/das-jishu/algoexpert-data-structures-algorithms)
- [[LeetCode Beginner]]
- [[씹어먹는 C++ 정리노트]]

