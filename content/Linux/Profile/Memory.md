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


### slabtop

`slabtop` 명령어는 실시간으로 커널의 슬랩 캐시 정보를 확인할 수 있다. `top` 명령어와 유사한 형태의 출력을 보인다. 이 명령어는 `/proc/slabinfo` 정보를 기반으로 데이터를 생성핸다.

```bash
 Active / Total Objects (% used)    : 5076057 / 5081816 (99.9%)
 Active / Total Slabs (% used)      : 165982 / 165982 (100.0%)
 Active / Total Caches (% used)     : 102 / 147 (69.4%)
 Active / Total Size (% used)       : 1964230.97K / 1965949.51K (99.9%)
 Minimum / Average / Maximum Object : 0.01K / 0.39K / 16.81K

  OBJS ACTIVE  USE OBJ SIZE  SLABS OBJ/SLAB CACHE SIZE NAME                   
1554579 1554579 100%    0.10K  39861       39    159444K buffer_head
1397319 1397172   0%    0.19K  66539       21    266156K dentry
1332690 1332690 100%    1.05K  44423       30   1421536K ext4_inode_cache
378420 378420 100%    0.04K   3710      102     14840K ext4_extent_status
 53872  53314   0%    0.57K   1924       28     30784K radix_tree_node
 47970  47048   0%    0.20K   1230       39      9840K vm_area_struct
 39750  39750 100%    0.13K   1325       30      5300K kernfs_node_cache
 30592  30265   0%    0.06K    478       64      1912K anon_vma_chain
 25472  24610   0%    0.06K    398       64      1592K kmalloc-64
 21924  21924 100%    0.58K    812       27     12992K inode_cache
 16813  16813 100%    0.69K    731       23     11696K squashfs_inode_cache
 ...
```
각 컬럼은 다음과 같다.
- OBJS : 각 객체 개수
- ACTIVE : 활성화 횟수
- USE : 사용된 퍼센트 비율
- OBJ SIZE : 바이트 단위로 객체의 크기
- CACHE SIZE : 바이트 단위로 개시 전체 크디

해당 정보는 `vmstat -m` 명령을 이용하여 동일하게 확인 가능하다.


### pmap
`pmap`은 프로세스의 메모리 맵을 확인할 수 있다. 

```bash
pmap -x 4441
4441:   man pmap
Address           Kbytes     RSS   Dirty Mode  Mapping
000055d7beef4000     100     100       0 r-x-- man
000055d7beef4000       0       0       0 r-x-- man
000055d7bf10c000       4       4       4 r---- man
...
00007f0e35828000       0       0       0 ----- libseccomp.so.2.4.1
00007f0e368a2000       0       0       0 rw--- ld-2.27.so
00007f0e368a3000       4       4       4 rw---   [ anon ]
00007f0e368a3000       0       0       0 rw---   [ anon ]
00007ffdee1bd000     132      20      20 rw---   [ stack ]
00007ffdee1bd000       0       0       0 rw---   [ stack ]
00007ffdee1e4000      12       0       0 r----   [ anon ]
00007ffdee1e4000       0       0       0 r----   [ anon ]
00007ffdee1e7000       4       4       0 r-x--   [ anon ]
00007ffdee1e7000       0       0       0 r-x--   [ anon ]
ffffffffff600000       4       0       0 --x--   [ anon ]
ffffffffff600000       0       0       0 --x--   [ anon ]
---------------- ------- ------- ------- 
total kB           29812    4224    1204

```
