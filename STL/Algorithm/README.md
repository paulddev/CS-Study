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


