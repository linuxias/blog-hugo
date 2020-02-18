+++
title = "CPU Profiling"
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
 r  b   swpd   free     buff    cache    si   so    bi    bo   in   cs us sy id wa st
 0  0   1024 12837676 7238384 10378596    0    0    14    39   24   19  2  1 97  0  0
```

위 컬럼에서 아래 정보에서 각 컬럼에 대한 내용은 아래와 같다.

 - r : 실행 중이거나 실행을 위해 기다리는 프로세스 개수
 - us : 사용자 시간
 - sy : 시스템 시간
 - id : 유휴
 - wa : I/O 대기. 스레드가 디스크 I/O를 기다리느라 블록된 것을 표시

### mpstat (multiprocessor statistics tool)

CPU 별 통계 확인 가능

```bash
mpstat -P ALL 1
Linux 4.15.0-76-generic (linuxias) 	2020년 02월 17일 	_x86_64_	(8 CPU)

       CPU    %usr   %nice    %sys %iowait    %irq   %soft  %steal  %guest  %gnice   %idle
       all    5.02    0.00    7.78    0.00    0.00    0.00    0.00    0.00    0.00   87.20
         0    0.99    0.00    1.98    0.00    0.00    0.00    0.00    0.00    0.00   97.03
         1    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
         2    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
         3    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
         4   37.62    0.00   59.41    0.00    0.00    0.00    0.00    0.00    0.00    2.97
         5    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00
         6    0.00    0.00    1.01    0.00    0.00    0.00    0.00    0.00    0.00   98.99
         7    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00    0.00  100.00

```

mpstat을 실행한 호스트머신에는 8개의 CPU가 있으며 각 통계정보는 위와 같다. 각 정보가 표시하는 내역은 다음과 같다.
 
 - CPU : 논리적 CPU ID
 - %usr : 사용자 시간
 - %nice : nice가 지정된 프로세스의 사용자 시간
 - %sys : 시스템 시간
 - %iowatit : I/O 대기
 - %irq : 하드웨어 인터럽트 CPU 사용률
 - %soft : 소프트웨어 인터럽트 CPU 사용률
 - %guest : 게스트 가상 머신을 실행하는데 사용한 시간
 - %idle : 유휴 시간

위 정보를 기반으로 현재 호스트머신의 대부분의 CPU는 idle 상태이며, 4번 CPU에서 특정한 작업이 이뤄지고 있고 있음을 알 수 있다. usr / sys / idle 항목을 잘 확인하면 CPU 별 CPU 사용률과 사용자, 커널 사용 시간에 대한 비율을 확인할 수 있다. 사용자 어플리케이션이 코드를 실행하는 데 소모한 CPU 시간이 `usr` 이며, 커널 영역 코드 실행하는데 걸린 시간이 `sys`이다. 커널 시간에는 시스템 콜 처리 시간, 커널 스레드가 보낸 시간, 인터럽트 처리 시간 등이 포함된다.

### sar (system activity reporter)

`sar` 는 `mpstat`와 유사하게 사용되며 현재 시스템 상태를 관찰하고, 과거 통계 정보를 저장하도록 할 수 있다. 각 항목은 위에서 설명한 항목과 유사하다.

```bash
sar -P ALL 1
Linux 4.15.0-76-generic (linuxias) 	2020년 02월 17일 	_x86_64_	(8 CPU)

                CPU     %user     %nice   %system   %iowait    %steal     %idle
                all      5.75      0.00      8.12      0.00      0.00     86.12
                  0      3.00      0.00      5.00      0.00      0.00     92.00
                  1      0.99      0.00      0.99      0.00      0.00     98.02
                  2      7.92      0.00      7.92      0.00      0.00     84.16
                  3      1.00      0.00      0.00      0.00      0.00     99.00
                  4      1.98      0.00      2.97      0.99      0.00     94.06
                  5      0.00      0.00      2.02      0.00      0.00     97.98
                  6     30.69      0.00     44.55      0.00      0.00     24.75
                  7      0.99      0.00      1.98      0.00      0.00     97.03

```

### perf (Performance Counters for Linux)
perf는 다양한 옵션이 존재하지만 그 중 몇가지 항목에 대해서만 정리해보려 한다.

| Command | Description |
|---|---|
|`record`| 명령어를 실행하고 결과를 perf.data 파일에 저장한다. |
|`report`| perf.data 파일을 읽어 결과를 출력한다. |
|`sched`| 스케쥴러 트레이싱을 위한 툴 |
|`stat`| 통계 정보 |

#### 시스템 프로파일링

`perf`는 CPU 호출 경로를 프로파일링 할 때 자주 사용된다. 아래는 시스템 프로파일링에 대한 예시이다.
`record`의 `-a` 옵션은 모든 CPU, `-g`는 호출 스택, `-F 997` 는 샘플링 주기를 997Hz로 `sleep 10`은 10초 간 기록하라라는 의미이다.
`record`로 저장한 데이터를 `report`로 출력하게 되면 트리 형태로 표현된다. 너무 길어 아래 부분은 생략한다.
```bash
$sudo perf record -a -g -F 997 sleep 10
[ perf record: Woken up 1 times to write data ]
[ perf record: Captured and wrote 2.024 MB perf.data (3722 samples) ]
$sudo perf report --stdio
# To display the perf.data header info, please use --header/--header-only options.
#
#
# Total Lost Samples: 15
#
# Samples: 3K of event 'cycles:ppp'
# Event count (approx.): 2728432762
#
# Children      Self  Command          Shared Object                Symbol                                                                           
# ........  ........  ...............  ...........................  ............................
#
    62.00%     0.00%  swapper          [kernel.kallsyms]            [k] secondary_startup_64
            |
            ---secondary_startup_64
               |          
               |--56.13%--start_secondary
               |          cpu_startup_entry
               |          |          
               |           --55.96%--do_idle
               |                     |          
               |                     |--47.86%--call_cpuidle
               |                     |          cpuidle_enter
               |                     |          |          
               |                     |           --47.85%--cpuidle_enter_state
               |                     |                     |          
               |                     |                     |--34.77%--intel_idle
               |                     |                     |          
               |                     |                     |--7.11%--apic_timer_interrupt

.......
```

위 트리구조를 해석하면 전체 정보는 샘플 크기가 작아지는 순서대로 표시된다. 가장 많이 CPU를 사용한 스레드는 swapper(유휴 스레드)이다. 트리의 내부에서 표현되는 %는 CPU 전체가 아니다. 위에서 cpuidle_enter_State가 47.85%이고 자식 중 intel idle이 34.77%라면, intel idle 이 47.85% 중 34.77%를 차지한다는 의미이다.


#### 특정 프로세스 프로파일링

```bash
perf record -g [command] // 프로세스 실행하며 프로파일링 시작
perf record -g -p [pid]  // 실행 중신 프로세스 프로파일링 시작
```

htop 프로세스를 실행하고 `-p` 옵션으로 프로파일링 하는 예시는 아래와 같다.

```bash
$sudo perf record -g -p `pidof htop` sleep 5
$sudo perf report --stdio
# To display the perf.data header info, please use --header/--header-only options.
#
#
# Total Lost Samples: 2
#
# Samples: 50  of event 'cycles:ppp'
# Event count (approx.): 309981392
#
# Children      Self  Command  Shared Object      Symbol                                              
# ........  ........  .......  .................  ....................................................
#
    64.87%    64.87%  htop     [kernel.kallsyms]  [k] syscall_return_via_sysret
            |          
            |--57.82%--0x30d8f70400000006
            |          __close_nocancel
            |          syscall_return_via_sysret
            |          
             --6.93%--__GI___libc_read
                       syscall_return_via_sysret

    62.48%     0.00%  htop     libc-2.23.so       [.] __close_nocancel
            |
            ---__close_nocancel
               |          
               |--57.82%--syscall_return_via_sysret
               |          
                --4.42%--__entry_trampoline_start

    57.82%     0.00%  htop     [unknown]          [k] 0x30d8f70400000006
            |
            ---0x30d8f70400000006
               __close_nocancel
               syscall_return_via_sysret

    25.35%     0.00%  htop     libc-2.23.so       [.] __GI___libc_read
    26....
```

#### 스케줄러 프로파일링
`sched` 옵션을 이용하여 스케쥴러 정보를 확인할 수 있다. 아래에서 사용한 `latency` 옵션은 각 태스크 스케쥴링 레이턴시들에 대해 정보를 제공한다. 그 외에도 `script`, `replay`, `map` 옵션이 있다. 자세한 내용은 `man perf sched` 에서 확인하면 된다.

```bash
$sudo perf sched record sleep 3
[ perf record: Woken up 2 times to write data ]
[ perf record: Captured and wrote 6.090 MB perf.data (40024 samples) ]
$ sudo perf sched latency

 -----------------------------------------------------------------------------------------------------------------
  Task                  |   Runtime ms  | Switches | Average delay ms | Maximum delay ms | Maximum delay at       |
 -----------------------------------------------------------------------------------------------------------------
  perf:2215             |      5.191 ms |        1 | avg:    0.032 ms | max:    0.032 ms | max at:  89323.211980 s
  lttng-sessiond:17440  |      0.101 ms |        3 | avg:    0.032 ms | max:    0.033 ms | max at:  89321.697742 s
  avahi-daemon:1119     |      8.777 ms |       54 | avg:    0.028 ms | max:    0.041 ms | max at:  89322.788970 s
  kworker/1:0:28740     |      0.159 ms |        8 | avg:    0.026 ms | max:    0.033 ms | max at:  89322.372042 s
  kworker/3:0:28203     |      0.028 ms |        1 | avg:    0.025 ms | max:    0.025 ms | max at:  89320.908025 s
  kworker/0:1:30386     |      0.096 ms |        3 | avg:    0.025 ms | max:    0.033 ms | max at:  89322.872069 s
  kworker/7:0:31054     |      0.154 ms |        5 | avg:    0.024 ms | max:    0.031 ms | max at:  89322.780055 s
  NioBlockingSele:1018  |      0.086 ms |        3 | avg:    0.023 ms | max:    0.033 ms | max at:  89320.871888 s
  /usr/bin/x-term:7180  |      1.292 ms |        6 | avg:    0.022 ms | max:    0.037 ms | max at:  89322.676333 s
  Xorg:4299             |      4.470 ms |       35 | avg:    0.021 ms | max:    0.034 ms | max at:  89322.809583 s
  whale:8519            |      0.513 ms |        5 | avg:    0.021 ms | max:    0.033 ms | max at:  89322.219984 s
.....
  sleep:2220            |      0.926 ms |        2 | avg:    0.007 ms | max:    0.010 ms | max at:  89323.211755 s
  ksoftirqd/7:52        |      0.041 ms |        2 | avg:    0.005 ms | max:    0.006 ms | max at:  89320.924052 s
  systemd-journal:340   |      0.043 ms |        1 | avg:    0.005 ms | max:    0.005 ms | max at:  89320.290112 s
  zeitgeist-datah:6956  |      0.209 ms |        4 | avg:    0.005 ms | max:    0.009 ms | max at:  89320.836458 s
  kworker/u16:1:1956    |      6.535 ms |      810 | avg:    0.002 ms | max:    0.037 ms | max at:  89321.088060 s
  kworker/u16:2:583     |     36.616 ms |     5468 | avg:    0.002 ms | max:    0.044 ms | max at:  89322.384987 s
 -----------------------------------------------------------------------------------------------------------------
  TOTAL:                |    163.575 ms |     9981 |
 ---------------------------------------------------

```
위 결과는 트레이싱 하는 동안의 평균과 최대 스케쥴러 지연시간을 나타낸다. 가장 위에 `perf`가 표시되는 이유는 트레이싱 동작 자체가 CPU와 저장장치들을 많이 사용함에 따른 비용이다. `perf sched latency` 뒤에 다양한 옵션이 들어 갈 수 있으므로 그거 또한 직접 확인해 보면 좋다.

#### 통계 정보 확인하기 (stat)


#### Reference
 1. 시스템 성능 분석과 최적화 | 브렌든 그레그 지음, 오현석, 서형국 옮김 | 위키북스
 2. [Perf Manual page](http://man7.org/linux/man-pages/man1/perf.1.html)
