---
title: AlgoExpert 031-040
date: 2024-03-21
tags:
  - Algorithm
  - Coding
  - Cplusplus
  - subpage
  - AlgoExpert
---
# AlgoExpert 031-040

---
# 31. Semordnilap ( $\leftrightarrow$ Palindromes)

![[Pasted image 20240305202759.png]]

문제요약:
- Unique strings를 받아서, semordnilap pair를 리스트로 반환하기
- Semordnilap pair: 거꾸로 해도 같은 값을 주는 string pair

```cpp
using namespace std;

string make_reverse(string word){
  string reverse_str;
  for (int i = word.length()-1; i >= 0; i--){
    string char_str({word[i]});
    reverse_str += char_str;
  }
  return reverse_str;
}

vector<vector<string>> semordnilap(vector<string> words) {
  // Write your code here.
  vector<vector<string>> result;
  unordered_set<string> reverse;
  for (auto& word : words){
    if(reverse.find(word)!=reverse.end()) {
      // cout << word << make_reverse(word) << endl;
      vector<string> thispair({word, make_reverse(word)});
      result.emplace_back(thispair);
    }
    else {
      cout << word << "  "<< make_reverse(word) << endl;
      reverse.insert(make_reverse(word));
    }
  }
  return result;
}
```

풀이요약:
- `unordered_set<string> reverse` 만들어서,
- `words` 안의 string `word` 를 `reverse`에 없는 경우 뒤집어서 `reverse`에 넣어주자
	- 문장 뒤집는 함수 `make_reverse`는 따로 만들어 주시고.
- `word`가 `reverse` 안에 있다면 그 두 string이 semordnilap pair가 되는거니깐
- 두 단어를 `vector<string> thispair` 에 넣어서 pair를 만들고, 
- 이를 더 큰 `vector<vector<string>> result` 에 넣어주자.

---

# 32. Three Number Sum

![[Pasted image 20240305203356.png]]

문제요약: Array를 받아서 TargetSum으로 더해지는 Triplet 쌍을 구하기.

```cpp
#include <vector>
using namespace std;

vector<vector<int>> threeNumberSum(vector<int> array, int targetSum) {
  // Write your code here.
  vector<vector<int>> result;
  sort(array.begin(),array.end());
  int lp, rp, sum;
  for (int i = 0; i < array.size()-2; i++){
    if (targetSum < array[i]) break;
    sum = targetSum - array [i];
    lp = i + 1;
    rp = array.size() - 1;
    while(lp < rp){
      if ((array[lp]+array[rp])<sum) lp++;
      else if ((array[lp]+array[rp])>sum) rp--;
      else {
        vector<int> triplet = {array[i], array[lp], array[rp]};
        result.push_back(triplet);
        lp++;
        rp--;
      }
    }
  }
  return result;
}
```

풀이요약:
- 일단 array를 sort 하고
- left-pointer, right-pointer, sum 선언하고
- 앞에 있는 숫자 `array[i]` 부터 골라서 target sum에서 빼면 (=`sum`), 남은 값의 two-number-sum pair를 찾는 문제로 바뀜.
- Two-number-sum 풀이방법: lp와 rp 값을 더해서 sum 보다 크면 lp를 오른쪽으로 보내고, 작으면 rp를 왼쪽으로 보내서 O(n)에 해결
- 전체적으로 O(n^2)에 해결!

---

# 33. Smallest Difference

![[Pasted image 20240305210046.png]]

문제요약: 
- Array 두 개 받아서 숫자 하나씩 가져와서 absolute difference가 0에 가장 가까운 pair 찾기.

```cpp
#include <vector>
using namespace std;

vector<int> smallestDifference(vector<int> arrayOne, vector<int> arrayTwo) {
  // Write your code here.
  // sort
  sort(arrayOne.begin(), arrayOne.end());
  sort(arrayTwo.begin(), arrayTwo.end());
  // pointer for each array
  int p1 = 0;
  int p2 = 0;
  int size1 = arrayOne.size();
  int size2 = arrayTwo.size();
  // loop: proceed pointer of smaller value
  int diff = INT_MAX;
  int gap;
  int result1;
  int result2;
  while((p1 < size1) && (p2 < size2)){
    gap = abs(arrayOne[p1]-arrayTwo[p2]);
    if (gap < diff){
      diff = gap;
      result1 = arrayOne[p1];
      result2 = arrayTwo[p2];
    }
    if (arrayOne[p1] < arrayTwo[p2]) p1++;
    else if (arrayOne[p1] > arrayTwo[p2]) p2++;
    else return {result1, result2};
  }
  return {result1, result2};
}
```

풀이요약:
- 일단 두 array를 sort하고,
- 각 array 의 시작점을 pointer `p1`, `p2` 로 잡고,
- 두 숫자의 `gap`이 기존에 등록된 `diff` 보다 작으면 `gap`을 `diff`에 넣어주고, `result1` 과 `result2`를 업데이트해주자.
- `p1`과 `p2`는 경쟁적으로 증가하면서 array를 효율적으로 sweep 하자
	- `arrayOne[p1]` 값이 `arrayTwo[p2]` 값보다 
		- 작으면, `p1`을 한칸 올리는 것이 gap을 줄이면서 pointer를 전진시키는 방법이고,
		- 크면, `p2`을 한칸 올리는 것이 gap을 줄이면서 pointer를 전진시키는 방법이다.
		- 같으면, `gap`이 0이 되는거니깐 그 때의 두 값이 `{result1, result2}` pair가 된다.

---

# 34. Move Element To End

![[Pasted image 20240305211807.png]]

문제요약:
- Array와 정수 하나를 받아서,
- 그 정수와 같은 숫자를 array 맨 뒤로 몰아버리는 함수만들기.

```cpp
#include <vector>

using namespace std;

void swap(vector<int>& array, int i, int j){
  int temp;
  temp = array[i];
  array[i] =  array[j];
  array[j] = temp;
}

vector<int> moveElementToEnd(vector<int> array, int toMove) {
  // Write your code here.
  int i = 0;
  int j = array.size()-1;
  
  while (i<j){
    while (array[j] == toMove && i<j) j--;
    if (array[i] == toMove) swap(array,i,j--);
    i++;
  }
  return array;
}
```

풀이요약
- `vector<int> array` 랑 `int toMove` 받아서,
- 시작 포인터 `i`와 끝 포인터 `j`를 잡아두고,
- `i`를 오른쪽으로 보내면서, `array[i]`가 `toMove`와 같으면 `array[j]`와 `swap`하고 `j`를 왼쪽으로 보내자.
- 반복하면 끝~

---

# 35. Monotonic Array

![[Pasted image 20240305214025.png]]

문제요약
- 정수 배열을 받아서 monotonic한지 판단하는 함수 만들기

```cpp
using namespace std;

// sign function
int sign(int a, int b){
  int x = b - a;
  return (x > 0) - (x < 0);
}

bool isMonotonic(vector<int> array) {
  // Check short array case
  if (array.size() <= 2) return true;
  
  int non_zero_direction = 0;
  int direction = 0;

  for (int i = 0; i < array.size()-1; i++){
    direction = sign(array[i],array[i+1]);
    // Register non_zero_direction for non-zero direction
    if (direction != 0 & non_zero_direction == 0) non_zero_direction = direction;
    if (direction != 0 & direction != non_zero_direction){
      return false;
    }
  }
  return true;
}
```

풀이요약:
- for-loop 돌리기 힘든 코너 케이스 따로 체크하고,
- Monotonic array일 경우의 방향을 non_zero_direction에 등록
- Array 따라가면서 연속된 두 숫자를 골라서, 차이가 없거나, 차이가 등록된 방향과 같아야 함.

참고:
- sign 만들어 주는 함수: `int((x>0)-(x<0))`

---

# 36. Spiral Traverse

![[Pasted image 20240312212936.png]]

문제요약:
- `nxm` 2차원 배열 받아서, 이를 spiral order로 재배열한 1차원 배열을 리턴하는 함수 만들기

```cpp
using namespace std;

vector<int> spiralTraverse(vector<vector<int>> array) {
  // Write your code here.
  vector<int> result;
  int r0 = 0;
  int r1 = array.size()-1;
  int c0 = 0;
  int c1 = array[0].size()-1;

  while (r0 <= r1 && c0 <= c1){ 
    if ( r0 != r1 && c0 != c1){
      for (int j = c0; j < c1; j++){
        result.push_back(array[r0][j]);
      }
      for (int i = r0; i < r1; i++){
        result.push_back(array[i][c1]);
      }
      for (int j = c1; j > c0; j--){
        result.push_back(array[r1][j]);
      }
      for (int i = r1; i > r0; i--){
        result.push_back(array[i][c0]);
      }
    }
    //corner case: when it becomes a 1D array
    else if ( r0 == r1){
      for (int j = c0; j <= c1; j++){
        result.push_back(array[r0][j]);
      }
      return result;
    }
    else if ( c0 == c1){
      for (int i = r0; i <= r1; i++){
        result.push_back(array[i][c0]);
      }
      return result;
    }
    r0++;
    r1--;
    c0++;
    c1--;
  }
  return result;
}
```

풀이요약:
- Spiral 형태로 깎아 나가면서 result 벡터에 push_back 해주고, index 업데이트 해주면 됨.
- 문제는 2D array 형태일 때, 가로로 1D array 일 때, 세로로 1D array 일 때, for-loop의 범위가 달라진다는 점.

---

# 37.  Longest Peak

![[Pasted image 20240317200206.png]]

문제요약
- 정수 array를 받아서 longest peak의 길이를 리턴하는 함수 만들기
- Length of a peak 는 strictly increasing 하다가 strictly decrease하는 subarray의 길이

```cpp
using namespace std;

int longestPeak(vector<int> array) {
  // Check array size
  if (array.size()<3) return 0;

  // iterator, delta states, length counter, max length
  int i = 1;
  bool prevInc, prevDec, nextInc, nextDec, reset, prevEq, nextEq;
  bool peak = false;
  int len = 0;
  int maxLen = 0;
  while (i < array.size()-1){
    // set delta states
    prevInc = (array[i] > array[i-1]);
    nextInc = (array[i] < array[i+1]);
    prevDec = (array[i] < array[i-1]);
    nextDec = (array[i] > array[i+1]);
    prevEq = (array[i] == array[i-1]);
    nextEq = (array[i] == array[i+1]);

    // toggle peak state
    peak = (prevInc & nextDec) ? true : peak;
    
    // check reset condition
    reset = (prevDec & nextInc) | nextEq | (i == array.size()-2);
    if (reset) {
      // if the index reaches to the end of the array, count the last element
      len = (nextDec & (i == array.size()-2) & peak)?  len+1 : len;
      // refresh maxLen when peak is ON
      // +2 comes from the previous and the current element
      maxLen = ((len > maxLen) & peak) ? len+2 : maxLen;
      len = 0;
      peak = false;
    }
    else if (prevInc | prevDec) len++;
    i++;
  }
  return maxLen;
}
```

풀이요약
- 두번째 element부터 인덱싱해서, 각 i-th element 의 앞뒤로 증가하는지 감소하는지 delta state 기록
- peak 맞는지 확인해서 peak state 를 toggle 해주고,
- 순조롭게 증가/감소하면 counter (len) 하나 더 해주고,
- reset 해야하는 경우 (바닥 찍고 올라가는 경우, 다음 element랑 크기 같아서 증감 없는 경우, array 끝에 도달한 경우) 확인해서,
- max length를 업데이트 해주고, peak/len 리셋해주기.

---

# 38. Array of Products

![[Pasted image 20240317200256.png]]

문제요약
- non-empty integer array 받아서, 같은 길이의 output array 를 반환.
- i-th output은 i-th input을 제외한 input element를 다 곱한 값

```cpp
#include <vector>

using namespace std;

vector<int> arrayOfProducts(vector<int> array) {
  // Write your code here.
  vector<int> leftProd(array.size(),1);
  vector<int> rightProd(array.size(),1);
  vector<int> result;

  if (array.size() < 2) return array;
  
  for (int i = 1; i < array.size(); i++){
    leftProd[i] = leftProd[i-1] * array[i-1];
  }
  
  for (int i = array.size()-2; i >= 0 ; i--){
    rightProd[i] = rightProd[i+1] * array[i+1];
  }

  for (int i = 0; i < array.size(); i++){
    result.emplace_back(leftProd[i]*rightProd[i]);
  }
  
  return result;
}
```

풀이요약
- 다 곱해서, 나누면 이상해짐. 
- i-th 원소의 왼쪽 원소들을 곱한 값과, 오른쪽 원소들을 곱한 값을 곱하면 되는거니깐,
- 두 개의 배열 `leftProd`랑 `rightProd`을 만들어서, 
- 하나는 i-th 원소의 왼쪽 값들을 곱한 값을 i-th 원소로 두는 배열, 하나는 오른쪽 값을 곱한 배열을 만든 다음에,
- i-th 결과를 구할 땐, 두 배열의 i-th 원소들을 곱하면 됨.

---

# 39. First Duplicate Value


![[Pasted image 20240317200359.png]]

문제요약:
- 1~n 사이의 값들로 이루어진 크기 n인 정수 배열을 받아서,
- 한 번 이상 반복되는 첫번째 정수를 리턴하는 함수 만들기

```cpp
#include <vector>
#include <unordered_set>
using namespace std;

int firstDuplicateValue(vector<int> array) { 
  unordered_set<int> set;
  for (int elem : array){
    if (set.find(elem) != set.end()) return elem;
    set.insert(elem);
  }
  return -1;
}
```

풀이요약:
- for-loop 돌면서 unordered_set 찾아보고 없으면 등록

---

# 40. Merge Overlapping Intervals

![[Pasted image 20240317200853.png]]

문제요약:
- 두 숫자로 정해지는 구간 (interval)의 배열을 받아서,
- Overlap 되는 interval은 합쳐서 새로운 interval 배열을 리턴하는 함수 만들기.

```cpp
#include <vector>
#include <algorithm>
using namespace std;

bool compareVecs(vector<int> &vec1, vector<int> &vec2){
  return vec1[0] < vec2[0];
}

vector<vector<int>> mergeOverlappingIntervals(vector<vector<int>> intervals) {
  // Sort the intervals with respect to the first element of each interval.
  sort(intervals.begin(),intervals.end(),compareVecs);
  
  vector<bool> mergeNext;
  vector<vector<int>> result;

  // set mergeNext array
  int x = INT_MIN;
  for (int i = 0 ; i < intervals.size()-1 ; i++){
    // set x : interval-end that might be merged or not
    x = max(x, intervals[i][1]);
    if (x >= intervals[i+1][0]) mergeNext.emplace_back(true);
    else mergeNext.emplace_back(false);
  }
  mergeNext.emplace_back(false);

  // build a new interval and push back to the result
  int a, b;
  for (int i = 0 ; i < intervals.size(); i++){
    // need to set a new interval?
    if (i == 0) {a = intervals[i][0]; b = intervals[i][1];}
    else if (!mergeNext[i-1]) {a = intervals[i][0]; b = intervals[i][1];}
    // need to close the new interval?
    if (!mergeNext[i]) {
      b = max(b,intervals[i][1]);
      vector<int> x = {a, b};
      result.emplace_back(x);
    }
    // need to merge?
    else {b = max(b, intervals[i][1]);}
  }
  return result;
}
```

풀이요약:
- 일단 2D vector를 각 interval의 첫번째 element로 sort
- 각 interval의 끝값 (또는 현재 interval의 끝값 보다 더 큰 interval의 끝값)과 다음 Intervale의 시작값을 비교해서 merge할지 판단해서 mergeNext 마스크를 채움
- mergeNext 마스크를 보면서, interval을 새로 만들지, merge 할지 판단하면서 result 2D vector에 새 interval 채워넣기.

---
# Reference

- [AlgoExpert](https://www.algoexpert.io/)
- [Github repository: A collection of solutions for all problem statements on the AlgoExpert Coding Interview platform](https://github.com/das-jishu/algoexpert-data-structures-algorithms)
- [[LeetCode Beginner]]
- [[씹어먹는 C++ 정리노트]]

