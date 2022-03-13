# STL의 Algorithm
`#include <algorithm>`

선행 공부 `함수 포인터` 학습은 https://boycoding.tistory.com/233

# 원소를 수정하지 않는 작업

## for_each
`for_each` : 처음과 끝 반복자 범위 내 원소들에 대해 함수를 실행시킨다.
- 원소들에 대해 함수를 실행하지만, 원소를 수정하지 않는다.
- 원소들을 순차적으로 접근하여 함수를 실행시킨다.

인수
- begin()
- end()
- 함수 포인터 : 람다 함수

리턴값
- 인수로 전달받은 함수 객체를 리턴

```cpp
// vector는 처음부터 끝까지 모든 원소들에 대해 function 함수를 수행한다.
for_each(v.begin(), v.end(), function);
```

## Permutation (순열)
`순열(permutation)` : 원소들을 어떤 순서로 나열한 것을 말한다.

`next_permutation` : 다음 순열을 리턴한다.
- 다음 순열이 없다면 false 리턴, => 더 이상 수열에서 더 큰 수를 만들 수 없다면 false 리턴
```cpp
vector<int> v = {1,2,3};

do
{
  for(var item : v)
  {
    cout << item << ' ';
  }
  cout << endl;
} while(next_permutation(v.begin(), v.end());

1 3 2
2 1 3
2 3 1
3 1 2
3 2 1
=> 3P3
nPr = n! / (n - r)!
nCr = n! / (n - r)! * r!
```

`순열` : 순서를 정해서 나열하는 것! <br>
`조합` : 순서를 생각하지 않는다. (A,B) == (B,A) 같은 취급한다. 그래서 r을 나눠주는 것이다. (중복 제거)

`prev_permutation` : 이전 순열을 리턴한다.
- 이전 순열이 없다면 false 리턴, => 더 이상 이 수열에서 더 작은 수를 만들 수 없을 때 false

# 원소를 수정하는 작업

## Partition (파티션)
`파티션(Partition)` : 특정 조건을 만족하는 원소들은 앞으로 보내고, 나머지는 뒤로 보내는 작업 <br>
퀵 소트에서 pivot 을 기준으로 pivot보다 작은 값은 앞에 배치, pivot보다 큰 값은 뒤에 배치하는 작업을 파티션이라고 한다.

## iter_swap
`iter_swap` : 두 반복자를 인수로 넘기면 두 반복자가 가리키는 원소의 값이 swap 된다.
```cpp
iter_swap(str.begin() + i, str.begin() + j); // str[i] <-> str[j]
```

## Sort
```cpp
sort(v.begin(), v.end());

//default 는 오름차순이다.
```

`<` 연산자를 사용하여 정렬하며, 사용자의 입맛에 따라 정렬하고 싶으면 `<`연산자를 오버로딩 해야 된다.
```cpp
bool compare(const int a, const int b) 
{
  return a < b; (오름차순)
  return a > b; (내림차순)
}
```

클래스 내에서 연산자 오버로딩을 만들게 되면 인수를 1개로 선언해도 되지만, 전역함수로 만들게 된다면 <br>
인수는 반드시 2개여야 한다.

비교 함수 내부에서는 인자로 받은 원소를 수정하면 안되기 때문에 `const&`로 해주자.

`람다식`도 가능하다.
```cpp
sort(v.begin(), v.end(), [](int a, int b) { return a > b; });
```

`include <functional>`에서 `greater<type>()` 으로 내림차순을 정렬할 수 있다. default는 `less<type>()` 이다.

`stable_sort` : 정렬은 하되 원소들간의 순서를 보전한다. (중요하다.)
- 일반 정렬의 경우는 들어온 순서가 (10, 2), (10, 1) 의 경우 정렬을 하게 된다면 (10, 1), (10, 2)가 된다.
- 안정 정렬의 경우는 들어온 순서를 보장하여 (10, 2), (10, 1)로 유지가 된다.
- 일반 정렬보다는 느리다. (아무래도 안정적으로 정렬을 해야되기 때문에)
- 일반 정렬과 마찬가지로 비교를 위한 비교 함수 포인터도 인자로 넣어줄 수 있다.

`partial_sort` : 배열의 일부분만 정렬한다.
```cpp
partial_sort(s, m, e)
```
- [start, end) 에서 [start, middle) 자리에만 전체 정렬을 적용한다.
- 5 4 3 2 1 2 3 -> (앞에 3자리만 한다면) 3 4 5 2 1 2 3
- 일반 정렬과 마찬가지로 비교를 위한 비교 함수 포인터도 인자로 넣어줄 수 있다.

## Binary Search (이진 탐색)
주의할 점 : 원소들이 정렬되어 있다는 전제가 있어야 한다.

```cpp
// key 가 해당 범위 내에 있다면 true, 아니면 false
bool ok = binary_search(v.begin(), v.end(), 5);

// 보통은 직접 구현하는 경우도 많다. (재귀 혹은 반복문)
```

`lower_bound` : 어떤 값의 하한선
- lower_bound(v.begin(), v.end(), 100)
  * 100과 일치하면 그 Key의 반복자를 리턴
  * 일치하지 않는다면 100을 초과하는 것 중 가장 작은 것의 반복자를 리턴

- [1, 10, 20, 40, 50, 60, 70]
  * lower_bound(v.begin(), v.end(), 50) => 50의 반복자 리턴
  * lower_bound(v.begin(), v.end(), 65) => 70의 반복자 리턴
  * lower_bound(v.begin(), v.end(), 80) => end() 리턴

핵심은 두 반복자로 나타낸 해당 범위 안의 원소들 중 `그 값보다 크거나 같은 첫번째 원소의 반복자를 리턴한다.` <br>
없다면 `end()`를 리턴하게 된다.

```cpp
int start = 0;
int end = n - 1;

while(start < end)
{
  int mid = (start + end) / 2; // overflow 조심
  
  if(arr[mid] < key)
    start = mid + 1;
  else
    end = mid;
}

return end;
```
- Key보다 작은 범위는 답이 될 수 없기 때문에 start = mid + 1
- Key보다 크거나 같은 범위는 답이 될 수 있다. 현재의 mid가 또 답 후보가 될 수 있다. end = mid
- lower_bound 는 일치하는 것도 답이 될 수 있다.

`upper_bound` : 어떤 값의 상한선
- upper_bound(v.begin(), v.end(), 100)
  * 100을 `초과하는 것 중 가장 작은 것의 반복자를 리턴한다.`
  * upper_bound 는 lower_bound 와는 다르게 일치하는 것은 찾지 않는다.
    1. lower_bound -> 일치 or 초과하는 것 중 가장 작은 것
    2. upper_bound -> 초과하는 것 중 가장 작은 것

- [1, 10, 20, 40, 50, 60, 70]
  * upper_bound(v.begin(), v.end(), 50) => 60의 반복자 리턴
  * upper_bound(v.begin(), v.end(), 65) => 70의 반복자 리턴
  * upper_bound(v.begin(), v.end(), 100) => end() 반복자 리턴

핵심은 두 반복자로 나타낸 해당 범위 안의 원소들 중 `그 값보다 큰 첫번째 원소의 반복자를 리턴한다.` <br>
없다면 `end()`를 리턴하게 된다.

일반 정렬과 마찬가지로 비교를 위한 비교 함수 포인터도 인자로 넣어줄 수 있다.
```cpp
int start = 0;
int end = n - 1;

while(start < end)
{
  int mid = (start + end) / 2; // overflow 조심
  
  if(arr[mid] <= key)
    start = mid + 1;
  else
    end = mid;
}

return end;
```
- Key보다 작거나 같은 범위는 답이 될 수 업기 때문에 start = mid + 1
- Key보다 큰 범위는 답이 될 수 있기 때문에 현재의 mid 또한 답 후보가 될 수 있다 end = mid

`equal_range` : (lower_bound, upper_bound)의 pair 객체를 리턴한다.
```cpp
// 해당 범위 내에서 처음으로 5와 같거나 큰 원소의 반복자, 해당 범위 내에서 처음으로 5를 초과한 원소의 반복자 쌍을 리턴받는다.
// [1 2 3 4 5 5 5 6 7]
//          a     b
// a : lower_bound, b : upper_bound
// upper_bound - lower_bound 하면 해당 key가 몇 개 있는지 구할 수 있다!
auto p = equal_range(v.begin(), v.end(), 5);
```

## max, min
```cpp
max(1, 5) => 5
min(1, 5) => 1

max_element(v.begin(), v.end()) => 해당 범위 내에서 최대값의 반복자 리턴
min_element(v.begin(), v.end()) => 해당 범위 내에서 최소값의 반복자 리턴
값을 얻고 싶다면 반복자에 시작 반복자를 빼주면 된다.
```

## fill
배열을 초기화할 때 자주 사용하는 테크닉이다.

해당 값으로 모두 채워준다.
```cpp
fill(v.begin(), v.end(), 5);

2차원 배열인 경우
int board[50][50];
fill(board[0], board[0] + 50 * 50, 5);
```
