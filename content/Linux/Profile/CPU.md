+++
title = "CPU Profiling"
draft = true
+++

# CPU Analysis

### uptime

시스템 부하 평균을 표시한다. load average의 값은 앞에서부터 1분, 5분, 15분 동안의 평균 부하이다.

``` bash
uptime
 08:41:55 up 1 day, 17:41,  4 users,  load average: 0.28, 0.24, 0.27
```

부하평균은 CPU 자원에 대한 요구사항으로써, ** 실행 중인 스레드 개수 + 대기열의 스레드 개수 **이다. 만약 부하 평균이 호스트 머신의 CPU 개수보다 크면 스레드를 처리하기에 CPU가 부족한 상황이며, 대기열에 여러 스레드가 CPU를 할당받기 위해 대기하고 있는 상태라고 파악할 수 있다.


### vmstat

vmstat은 가상 메모리 통계 정보를 제공한다. 

```bash
$vmstat
procs -----------memory---------- ---swap-- -----io---- -system-- ------cpu-----
 r  b   swpd   free   buff  cache   si   so    bi    bo   in   cs us sy id wa st
 0  0   1024 12837676 7238384 10378596    0    0    14    39   24   19  2  1 97  0  0
```

위 컬럼에서 아래 정보에서 각 컬럼에 대한 내용은 아래와 같다.

 - r : 실행 중이거나 실행을 위해 기다리는 프로세스 개수
 - us : 사용자 시간
 - sy : 시스템 시간
 - id : 유휴
 - wa : I/O 대기. 스레드가 디스크 I/O를 기다리느라 블록된 것을 표시

