---
title: "[gcc] 최적화 속성 사용하기"
date: 2019-12-30T23:23:27+09:00
---

gcc를 컴파일러로 사용할 시 -O2, -O3와 같은 최적화 옵션을 자주 사용하게 됩니다.

특별한 경우에 특정 함수나 코드 구간에 대해서 특정 최적화 단계를 적용하고 싶다면 아래와 같이 진행합니다.

#### 함수에 최적화 적용하기

```c
void __attribute__((optimize("O0"))) func(void) {

}
```

#### 코드 범위에 최적화 적용하기

```c
#pragma GCC push_options
#pragma GCC optimize ("O0")

 //Write your code

#pragma GCC pop_options
```

감사합니다.

