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

## 문제분석 경험

### 1. 새로운 환경에서 재컴파일하지 않은 어플리케이션의 PSS와 SWAP 영역이 증가한 상황
- 기존 환경 대비 새로운 환경에서의 프로세스  `smap`을 비교해본다.
- memps 프로그램 사용하여 결과를 비교한다.

### 2. PSS(Proportinal Set Size)의 감소
 - PSS는 USS + (공유페이지 / 공유하는 프로세스 수) 이다.
 - 프로세스를 실행 한 직후 얻은 결과와 10분 후 재측정한 결과에서 PSS가 다르게 나타날 수 있다.
 - 만약 A프로세스가 6MB 메모리를 사용하고 그 중 2MB가 그 프로세스의 고유 영역이라면, 나머지 4MB는 공유 메모리이다. 4MB의 공유메모리를 4개의 프로세스가 공유하고 있다면 PSS는 2MB + (4MB/4) = 3MB가 된다.
   출처: https://ecogeo.tistory.com/255 [아키텍트를 꿈꾸며 - 에코지오]

### 3. Application의 메모리 증가 (gcc 버전에 따른 메모리 증가)

#### 문제상황
 - 빌드시스템에서 컴파일러 변경 후 메모리 증가
 
#### 문제확인
 - 바이너리 사이즈 확인 시 증가된 상황 확인. /proc/[pid]/smap 을 이용하여 PSS와 다른 메모리 정보 분석.

```bash
RELRO           STACK CANARY      NX            PIE             RPATH      RUNPATH	Symbols		FORTIFY	Fortified	Fortifiable  FILE
No RELRO        Canary found      NX enabled    DSO             No RPATH   RW-RUNPATH   No Symbols

readelf -l elf_example

Elf file type is DYN (Shared object file)
Entry point 0x6554
There are 6 program headers, starting at offset 52

Program Headers:
  Type           Offset   VirtAddr   PhysAddr   FileSiz MemSiz  Flg Align
  EXIDX          0x01cc98 0x0001cc98 0x0001cc98 0x00688 0x00688 R   0x4
  LOAD           0x000000 0x00000000 0x00000000 0x1d324 0x1d324 R E 0x10000
  LOAD           0x01d324 0x0002d324 0x0002d324 0x0072c 0x0078c RW  0x10000
  DYNAMIC        0x01d5bc 0x0002d5bc 0x0002d5bc 0x00130 0x00130 RW  0x4
  NOTE           0x0000f4 0x000000f4 0x000000f4 0x00024 0x00024 R   0x4
  GNU_STACK      0x000000 0x00000000 0x00000000 0x00000 0x00000 RW  0x10

 Section to Segment mapping:
  Segment Sections...
   00     .ARM.exidx 
   01     .note.gnu.build-id .gnu.hash .dynsym .dynstr .gnu.version .gnu.version_r .rel.dyn .rel.plt .init .plt .text .fini .rodata .ARM.extab .ARM.exidx .eh_frame 
   02     .init_array .fini_array .jcr .data.rel.ro .dynamic .got .data .bss 
   03     .dynamic 
   04     .note.gnu.build-id 
   05     
``` 

- 새로 컴파일한 바이너리의 정보 확인
```bash
RELRO           STACK CANARY      NX            PIE             RPATH      RUNPATH	Symbols		FORTIFY	Fortified	Fortifiable  FILE
Partial RELRO   Canary found      NX enabled    DSO             No RPATH   RW-RUNPATH   No Symbols      Yes	2		8	./elf_example

Elf file type is DYN (Shared object file)
Entry point 0x5d78
There are 7 program headers, starting at offset 52

Program Headers:
  Type           Offset   VirtAddr   PhysAddr   FileSiz MemSiz  Flg Align
  EXIDX          0x01d5f4 0x0001d5f4 0x0001d5f4 0x005d0 0x005d0 R   0x4
  LOAD           0x000000 0x00000000 0x00000000 0x1dbc8 0x1dbc8 R E 0x10000
  LOAD           0x01dc4c 0x0002dc4c 0x0002dc4c 0x00700 0x00730 RW  0x10000
  DYNAMIC        0x01ded0 0x0002ded0 0x0002ded0 0x00130 0x00130 RW  0x4
  NOTE           0x000114 0x00000114 0x00000114 0x00024 0x00024 R   0x4
  GNU_STACK      0x000000 0x00000000 0x00000000 0x00000 0x00000 RW  0x10
  GNU_RELRO      0x01dc4c 0x0002dc4c 0x0002dc4c 0x003b4 0x003b4 R   0x1

 Section to Segment mapping:
  Segment Sections...
   00     .ARM.exidx 
   01     .note.gnu.build-id .gnu.hash .dynsym .dynstr .gnu.version .gnu.version_r .rel.dyn .rel.plt .init .plt .text .fini .rodata .ARM.extab .ARM.exidx .eh_frame 
   02     .init_array .fini_array .data.rel.ro .dynamic .got .data .bss 
   03     .dynamic 
   04     .note.gnu.build-id 
   05     
   06     .init_array .fini_array .data.rel.ro .dynamic 
```

위 바이너리 정보를 확인 시 checksec으로 확인 시 `RELRO` 가 달라진 것을 확인할 수 있다. 이전 바이너리는 `NO RELRO`였으나 현재 바이너리는 `Partial RELRO`이다.

프로그램 헤더 정보를 확인해 보니 현재 바이너리에 `GNU_RELRO`란 메모리 영역이 새로 생성되었음을 확인 할 수 있다.

이번에 gcc를 9버전으로 업데이트하여 빌드한 결과물의 차이이다. gcc 9을 살펴보니 relro 관련 옵션이 기본으로 활성화 되어있음을 확인할 수 있다. 이로 인해 새로운 섹션들이 추가되었고 어플리케이션 메모리가 증가함을 확인하였다.

