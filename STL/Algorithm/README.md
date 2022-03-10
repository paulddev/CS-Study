# STL의 Algorithm
`#include <algorithm>`

## 원소를 수정하지 않는 작업
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

`순열(permutation)` : 원소들을 어떤 순서로 나열한 것을 말한다.
```cpp
next_permutation
prev_permutation
```

## 원소를 수정하는 작업
`파티션(Partition)` : 특정 조건을 만족하는 원소들은 앞으로 보내고, 나머지는 뒤로 보내는 작업 <br>
퀵 소트에서 pivot 을 기준으로 pivot보다 작은 값은 앞에 배치, pivot보다 큰 값은 뒤에 배치하는 작업을 파티션이라고 한다.

`iter_swap` : 두 반복자를 인수로 넘기면 두 반복자가 가리키는 원소의 값이 swap 된다.
```cpp
iter_swap(str.begin() + i, str.begin() + j); // str[i] <-> str[j]
```

`sort` : 정렬


