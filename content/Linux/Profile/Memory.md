+++
title = "Memory profiling"
+++

메모리 분석에 사용할 수 있는 다양한 도구에 대해 정리한다.

### vmstat

가상 메모리 통계 정보를 제공한다. 현재 가용 메모리와 페이지 통계 등 메모리에 대한 다양한 정보를 제공한다. `manual` 페이지에는 아래와 같이 필드 설명이 되어 있다.

```bash
   Memory
       swpd: the amount of virtual memory used.
       free: the amount of idle memory.
       buff: the amount of memory used as buffers.
       cache: the amount of memory used as cache.
       inact: the amount of inactive memory.  (-a option)
       active: the amount of active memory.  (-a option)
```

`vmstat`은 실행 시 특별한 권한을 필요로 하지 않는다.
```bash
$vmstat 1 -Sm
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 0  0    908  21698   5833   4774    0    0    14    34   12   42  1  1 98  0  0
 0  0    908  21698   5833   4774    0    0     0     0 69221 138980  1  4 95  0  0
 2  0    908  21698   5833   4774    0    0     0    60 93788 187899  0  5 95  0  0
```

위 내용 중 메모리 분석 관점에서 바라볼 분류는 다음과 같다.
- swpd : 스왑 아웃된 메모리 양
- free : 가용 메모리 양
- buffer : 버퍼 캐시에 있는 메모리
- cache : 페이지 캐시에 있는 메모리
- si : 스왑 인된 메모리
- so : 스왑 아웃된 메모리

`si`, `so` 항목이 0이 아니라면 스왑 장치에 파일에 페이징을 하고 있는 것이므로 시스템에 메모리 부하가 있다고 짐작해 볼 수 있다.
