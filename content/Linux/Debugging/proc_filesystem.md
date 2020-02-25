+++
title = "Proc Filesystem"
+++

#  The /Proc Filesystem for Debugging
## proc - process infomation pseudo-filesystem

(Process infomation)

리눅스가 인기 있는 이유 중 하나는 UNIX 시스템의 좋은 feature들을 결합시킨 점입니다. Proc 파일시스템은 그 좋은 feature들 중 한가지 인데요, 대다수의 Linux 배포판에 포함되어 있습니다. proc 파일시스템은 /proc에 마운트되어 있으며 kernel data 구조에 대해 인터페이스를 제공하고 있습니다. proc 파일시스템의 파일들은 READ-ONLY 이지만, 몇몇의 파일들은 커널 변수를 변경하여 적용할 수 있도록 WRITE가 가능합니다. proc 파일시스템의 구성요소를 하나하나 살펴보도록 하시죠.

이번 글에서는 /proc file system 중 process 정보에 관련된 내용을 살펴보겠습니다.

### /proc/self

/proc/self는 현재 실행중인 프로세스의 정보를 확인할 수 있는 특별한 링크입니다. 터미널에서 cd로 /proc/self로 이동하게 되면, 현재 shell 프로세스의 정보를 포함하는 디렉토리로 이동할 수 있습니다. 이 때 /proc/self가 cd 커맨드가 아닌 shell의 정보인 이유는 cd는 shell에 의해 제공되는 명령어 이기 때문이죠. 여러분이 만약 ls -l /proc/self 를 수행하게 되면 이 디렉토리는 ls 프로세스에 연결되게 됩니다. 그럼 여러분이 작성한 프로그램에서 pid를 모르는 상황에서 자신의 어떠한 정보를 알고싶다면 /proc/self로 접근하면 됩니다.

```bash
linuxias@desktop:/proc/self$ ls -al
total 0
dr-xr-xr-x   9 linuxias linuxias 0  7월  9 22:20 .
dr-xr-xr-x 260 root     root     0  7월  9 21:59 ..
dr-xr-xr-x   2 linuxias linuxias 0  7월  9 22:20 attr
-rw-r--r--   1 linuxias linuxias 0  7월  9 22:20 autogroup
-r--------   1 linuxias linuxias 0  7월  9 22:20 auxv
-r--r--r--   1 linuxias linuxias 0  7월  9 22:20 cgroup
--w-------   1 linuxias linuxias 0  7월  9 22:20 clear_refs
-r--r--r--   1 linuxias linuxias 0  7월  9 22:20 cmdline
-rw-r--r--   1 linuxias linuxias 0  7월  9 22:20 comm
-rw-r--r--   1 linuxias linuxias 0  7월  9 22:20 coredump_filter
-r--r--r--   1 linuxias linuxias 0  7월  9 22:20 cpuset
lrwxrwxrwx   1 linuxias linuxias 0  7월  9 22:20 cwd -> /proc/5666
-r--------   1 linuxias linuxias 0  7월  9 22:20 environ
lrwxrwxrwx   1 linuxias linuxias 0  7월  9 22:20 exe -> /bin/bash
dr-x------   2 linuxias linuxias 0  7월  9 22:20 fd
dr-x------   2 linuxias linuxias 0  7월  9 22:20 fdinfo
-rw-r--r--   1 linuxias linuxias 0  7월  9 22:20 gid_map
-r--------   1 linuxias linuxias 0  7월  9 22:20 io
-r--r--r--   1 linuxias linuxias 0  7월  9 22:20 limits
-rw-r--r--   1 linuxias linuxias 0  7월  9 22:20 loginuid
dr-x------   2 linuxias linuxias 0  7월  9 22:20 map_files
-r--r--r--   1 linuxias linuxias 0  7월  9 22:20 maps
-rw-------   1 linuxias linuxias 0  7월  9 22:20 mem
-r--r--r--   1 linuxias linuxias 0  7월  9 22:20 mountinfo
-r--r--r--   1 linuxias linuxias 0  7월  9 22:20 mounts
-r--------   1 linuxias linuxias 0  7월  9 22:20 mountstats
dr-xr-xr-x   5 linuxias linuxias 0  7월  9 22:20 net
dr-x--x--x   2 linuxias linuxias 0  7월  9 22:20 ns
-r--r--r--   1 linuxias linuxias 0  7월  9 22:20 numa_maps
-rw-r--r--   1 linuxias linuxias 0  7월  9 22:20 oom_adj
-r--r--r--   1 linuxias linuxias 0  7월  9 22:20 oom_score
-rw-r--r--   1 linuxias linuxias 0  7월  9 22:20 oom_score_adj
-r--------   1 linuxias linuxias 0  7월  9 22:20 pagemap
-r--------   1 linuxias linuxias 0  7월  9 22:20 patch_state
-r--------   1 linuxias linuxias 0  7월  9 22:20 personality
-rw-r--r--   1 linuxias linuxias 0  7월  9 22:20 projid_map
lrwxrwxrwx   1 linuxias linuxias 0  7월  9 22:20 root -> /
-rw-r--r--   1 linuxias linuxias 0  7월  9 22:20 sched
-r--r--r--   1 linuxias linuxias 0  7월  9 22:20 schedstat
-r--r--r--   1 linuxias linuxias 0  7월  9 22:20 sessionid
-rw-r--r--   1 linuxias linuxias 0  7월  9 22:20 setgroups
-r--r--r--   1 linuxias linuxias 0  7월  9 22:20 smaps
-r--r--r--   1 linuxias linuxias 0  7월  9 22:20 smaps_rollup
-r--------   1 linuxias linuxias 0  7월  9 22:20 stack
-r--r--r--   1 linuxias linuxias 0  7월  9 22:20 stat
-r--r--r--   1 linuxias linuxias 0  7월  9 22:20 statm
-r--r--r--   1 linuxias linuxias 0  7월  9 22:20 status
-r--------   1 linuxias linuxias 0  7월  9 22:20 syscall
dr-xr-xr-x   3 linuxias linuxias 0  7월  9 22:20 task
-r--r--r--   1 linuxias linuxias 0  7월  9 22:20 timers
-rw-rw-rw-   1 linuxias linuxias 0  7월  9 22:20 timerslack_ns
-rw-r--r--   1 linuxias linuxias 0  7월  9 22:20 uid_map
-r--r--r--   1 linuxias linuxias 0  7월  9 22:20 wchan

```

### /proc/[pid]

/proc/self는 실제로 현재 실행중인 프로세스의 PID에 맞는 /proc/<pid>로 연결되어 있습니다. 예를 들어 shell의 PID가 1000이라고 하면 현재 shell에서 /proc/self는 /proc/1000 디렉토리로 연결되어 있는거죠. 이렇게 /proc/ 디렉토리를 확인해보면 굉장히 많은 숫자의 디렉토리르 볼 수 있습니다. 각 디렉토리르 현재 실행중인 프로세스들의 PID들인거죠. 

```bash
ls -al
total 0
dr-xr-xr-x   9 linuxias linuxias 0  2월 25 08:07 .
dr-xr-xr-x 316 root     root     0  2월 25 08:06 ..
-r--r--r--   1 linuxias linuxias 0  2월 25 09:19 arch_status
dr-xr-xr-x   2 linuxias linuxias 0  2월 25 09:19 attr
-rw-r--r--   1 linuxias linuxias 0  2월 25 09:19 autogroup
-r--------   1 linuxias linuxias 0  2월 25 09:19 auxv
-r--r--r--   1 linuxias linuxias 0  2월 25 09:19 cgroup
--w-------   1 linuxias linuxias 0  2월 25 09:19 clear_refs
-r--r--r--   1 linuxias linuxias 0  2월 25 08:08 cmdline
-rw-r--r--   1 linuxias linuxias 0  2월 25 09:19 comm
-rw-r--r--   1 linuxias linuxias 0  2월 25 09:19 coredump_filter
-r--r--r--   1 linuxias linuxias 0  2월 25 09:19 cpuset
lrwxrwxrwx   1 linuxias linuxias 0  2월 25 09:19 cwd -> /proc/2174
```

특이한 부분을 하나 더 살펴보죠, /proc/self의 파일들의 사이즈가 모두 0인 걸 확인할 수 있습니다. 그럼 저 파일이 그럼 빈 파일인걸까요? 만약 여러분들이 저 파일 내부의 내용을 확인하려고 하면 정보를 확인할 수 있습니다. 파일의 크기가 0인 이유는 저 파일들이 기본적으로 커널의 자료구조와 직접 연결된 상태이며 실제 진짜 파일들은 아니기 때문입니다. 만약 파일 오퍼레이션(open, read, write... 등) 가 사용자에 의해 요청되면 커널은 동적으로 그 내용을 인식하여 처리하게 되죠.

### /proc/[pid]/maps

maps 파일은 프로세스의 메모리 주소 공간을 보여줍니다. 모든 프로세스는 가상 메모리 매니저에 의해 제공 또는 다뤄지는 가장 주소공간을 가지게되며, maps라는 이름으로 proc 파일시스템에서 확인할 수 있도록 제공됩니다. 먼저 저는 64bit 환경임을 말씀드리며, 32bit 환경에서 확인 시 다르게 나타남을 미리 말씀드립니다. 64bit 환경만 제대로 숙지하고 있어도 32bit 환경에서는 주소 공간의 크기 차이만 있기에 쉽게 파악하실 수 있습니다.

```bash
linuxias@desktop:/proc/self$ cat maps 
00400000-004f4000 r-xp 00000000 08:01 786932                             /bin/bash
006f3000-006f4000 r--p 000f3000 08:01 786932                             /bin/bash
006f4000-006fd000 rw-p 000f4000 08:01 786932                             /bin/bash
006fd000-00703000 rw-p 00000000 00:00 0 
00c90000-00f17000 rw-p 00000000 00:00 0                                  [heap]
7f989e760000-7f989e76b000 r-xp 00000000 08:01 6822306                    /lib/x86_64-linux-gnu/libnss_files-2.23.so
7f989e76b000-7f989e96a000 ---p 0000b000 08:01 6822306                    /lib/x86_64-linux-gnu/libnss_files-2.23.so
7f989e96a000-7f989e96b000 r--p 0000a000 08:01 6822306                    /lib/x86_64-linux-gnu/libnss_files-2.23.so
7f989e96b000-7f989e96c000 rw-p 0000b000 08:01 6822306                    /lib/x86_64-linux-gnu/libnss_files-2.23.so
7f989e96c000-7f989e972000 rw-p 00000000 00:00 0 
7f989e972000-7f989e97d000 r-xp 00000000 08:01 6822310                    /lib/x86_64-linux-gnu/libnss_nis-2.23.so
7f989e97d000-7f989eb7c000 ---p 0000b000 08:01 6822310                    /lib/x86_64-linux-gnu/libnss_nis-2.23.so
7f989eb7c000-7f989eb7d000 r--p 0000a000 08:01 6822310                    /lib/x86_64-linux-gnu/libnss_nis-2.23.so
7f989eb7d000-7f989eb7e000 rw-p 0000b000 08:01 6822310                    /lib/x86_64-linux-gnu/libnss_nis-2.23.so
7f989eb7e000-7f989eb94000 r-xp 00000000 08:01 6822290                    /lib/x86_64-linux-gnu/libnsl-2.23.so
7f989eb94000-7f989ed93000 ---p 00016000 08:01 6822290                    /lib/x86_64-linux-gnu/libnsl-2.23.so
7f989ed93000-7f989ed94000 r--p 00015000 08:01 6822290                    /lib/x86_64-linux-gnu/libnsl-2.23.so
7f989ed94000-7f989ed95000 rw-p 00016000 08:01 6822290                    /lib/x86_64-linux-gnu/libnsl-2.23.so
7f989ed95000-7f989ed97000 rw-p 00000000 00:00 0 
7f989ed97000-7f989ed9f000 r-xp 00000000 08:01 6822301                    /lib/x86_64-linux-gnu/libnss_compat-2.23.so
7f989ed9f000-7f989ef9e000 ---p 00008000 08:01 6822301                    /lib/x86_64-linux-gnu/libnss_compat-2.23.so
7f989ef9e000-7f989ef9f000 r--p 00007000 08:01 6822301                    /lib/x86_64-linux-gnu/libnss_compat-2.23.so
7f989ef9f000-7f989efa0000 rw-p 00008000 08:01 6822301                    /lib/x86_64-linux-gnu/libnss_compat-2.23.so
7f989efa0000-7f989f3b8000 r--p 00000000 08:01 12584711                   /usr/lib/locale/locale-archive
7f989f3b8000-7f989f578000 r-xp 00000000 08:01 6822293                    /lib/x86_64-linux-gnu/libc-2.23.so
7f989f578000-7f989f778000 ---p 001c0000 08:01 6822293                    /lib/x86_64-linux-gnu/libc-2.23.so
7f989f778000-7f989f77c000 r--p 001c0000 08:01 6822293                    /lib/x86_64-linux-gnu/libc-2.23.so
7f989f77c000-7f989f77e000 rw-p 001c4000 08:01 6822293                    /lib/x86_64-linux-gnu/libc-2.23.so
7f989f77e000-7f989f782000 rw-p 00000000 00:00 0 
7f989f782000-7f989f785000 r-xp 00000000 08:01 6822295                    /lib/x86_64-linux-gnu/libdl-2.23.so
7f989f785000-7f989f984000 ---p 00003000 08:01 6822295                    /lib/x86_64-linux-gnu/libdl-2.23.so
7f989f984000-7f989f985000 r--p 00002000 08:01 6822295                    /lib/x86_64-linux-gnu/libdl-2.23.so
7f989f985000-7f989f986000 rw-p 00003000 08:01 6822295                    /lib/x86_64-linux-gnu/libdl-2.23.so
7f989f986000-7f989f9ab000 r-xp 00000000 08:01 6820485                    /lib/x86_64-linux-gnu/libtinfo.so.5.9
7f989f9ab000-7f989fbaa000 ---p 00025000 08:01 6820485                    /lib/x86_64-linux-gnu/libtinfo.so.5.9
7f989fbaa000-7f989fbae000 r--p 00024000 08:01 6820485                    /lib/x86_64-linux-gnu/libtinfo.so.5.9
7f989fbae000-7f989fbaf000 rw-p 00028000 08:01 6820485                    /lib/x86_64-linux-gnu/libtinfo.so.5.9
7f989fbaf000-7f989fbd5000 r-xp 00000000 08:01 6822291                    /lib/x86_64-linux-gnu/ld-2.23.so
7f989fdad000-7f989fdb1000 rw-p 00000000 00:00 0 
7f989fdcd000-7f989fdd4000 r--s 00000000 08:01 12857131                   /usr/lib/x86_64-linux-gnu/gconv/gconv-modules.cache
7f989fdd4000-7f989fdd5000 r--p 00025000 08:01 6822291                    /lib/x86_64-linux-gnu/ld-2.23.so
7f989fdd5000-7f989fdd6000 rw-p 00026000 08:01 6822291                    /lib/x86_64-linux-gnu/ld-2.23.so
7f989fdd6000-7f989fdd7000 rw-p 00000000 00:00 0 
7ffe9814f000-7ffe98171000 rw-p 00000000 00:00 0                          [stack]
7ffe981dd000-7ffe981e0000 r--p 00000000 00:00 0                          [vvar]
7ffe981e0000-7ffe981e2000 r-xp 00000000 00:00 0                          [vdso]
ffffffffff600000-ffffffffff601000 r-xp 00000000 00:00 0                  [vsyscall]
....
```

위에 출력된 내용들을 하나씩 살펴봅시다. 32비트와는 다르게 64비트는 총 16EB크기만큼 주소를 표현할 수 있습니다. 32비트 아키텍쳐에서 확인한 결과와는 다른 주소공간의 크기를 가지기 때문에 표현되는 내용도 다릅니다. 그 점 꼭 유의해주시고 이 글에서는 64비트를 기준으로 설명한다는 점 다시 한번 말씀드립니다.

위 파일은 /bin/bash 프로세스의 maps 파일임을 확인할 수 있습니다. 일부분만 살펴보죠. 가장 앞에서부터 순서대로 address, perms, offset, dev(major:minor), inode, pathname 순서로 표기됩니다.
```bash
linuxias@desktop:/proc/self$ cat maps 
00400000-004f4000 r-xp 00000000 08:01 786932                             /bin/bash
...
```

정리하면 다음과 같습니다. perms에 표기된 p는 private이란 의미로 copy on write입니다.

<table class="txc-table" width="537" cellspacing="0" cellpadding="0" border="0" style="border: none; border-collapse: collapse; width: 537px;" 맑은="" 고딕",="" sans-serif;font-size:16px"=""><tbody><tr><td style="width: 195px; height: 24px; border-width: 1px; border-style: solid; border-color: rgb(204, 204, 204);"><p><span style="font-size: 10pt;">&nbsp;address</span><span style="font-size: 10pt;">&nbsp;</span></p></td><td style="width: 341px; height: 24px; border-bottom: 1px solid rgb(204, 204, 204); border-right: 1px solid rgb(204, 204, 204); border-top: 1px solid rgb(204, 204, 204);"><p><span style="font-size: 10pt;">&nbsp;00400000-004f4000</span></p></td></tr><tr><td style="width: 195px; height: 24px; border-bottom: 1px solid rgb(204, 204, 204); border-right: 1px solid rgb(204, 204, 204); border-left: 1px solid rgb(204, 204, 204);"><p><span style="font-size: 10pt;">&nbsp;perms</span></p></td><td style="width: 341px; height: 24px; border-bottom: 1px solid rgb(204, 204, 204); border-right: 1px solid rgb(204, 204, 204);"><p><span style="font-size: 10pt;">&nbsp;r-xp</span></p></td></tr><tr><td style="width: 195px; height: 24px; border-bottom: 1px solid rgb(204, 204, 204); border-right: 1px solid rgb(204, 204, 204); border-left: 1px solid rgb(204, 204, 204);"><p><span style="font-size: 10pt;">&nbsp;offset</span></p></td><td style="width: 341px; height: 24px; border-bottom: 1px solid rgb(204, 204, 204); border-right: 1px solid rgb(204, 204, 204);"><p><span style="font-size: 10pt;">&nbsp;00000000</span></p></td></tr><tr><td style="width: 195px; height: 24px; border-bottom: 1px solid rgb(204, 204, 204); border-right: 1px solid rgb(204, 204, 204); border-left: 1px solid rgb(204, 204, 204);"><p><span style="font-size: 10pt;">&nbsp;dev(major:minor)</span></p></td><td style="width: 341px; height: 24px; border-bottom: 1px solid rgb(204, 204, 204); border-right: 1px solid rgb(204, 204, 204);"><p><span style="font-size: 10pt;">&nbsp;08:01&nbsp;</span></p></td></tr><tr><td style="width: 195px; height: 24px; border-bottom: 1px solid rgb(204, 204, 204); border-right: 1px solid rgb(204, 204, 204); border-left: 1px solid rgb(204, 204, 204);"><p><span style="font-size: 10pt;">&nbsp;inode</span></p></td><td style="width: 341px; height: 24px; border-bottom: 1px solid rgb(204, 204, 204); border-right: 1px solid rgb(204, 204, 204);"><p><span style="font-size: 10pt;">&nbsp;786932&nbsp;</span></p></td></tr><tr><td style="width: 195px; height: 24px; border-bottom: 1px solid rgb(204, 204, 204); border-right: 1px solid rgb(204, 204, 204); border-left: 1px solid rgb(204, 204, 204);"><p><span style="font-size: 10pt;">&nbsp;pathname</span></p></td><td style="width: 341px; height: 24px; border-bottom: 1px solid rgb(204, 204, 204); border-right: 1px solid rgb(204, 204, 204);"><p><span style="font-size: 10pt;">&nbsp;/bin/bash</span></p></td></tr></tbody></table>

0x400000-0x4f4000는 해당 entry가 사용중인 주소 공간입니다. r-xp는 mapping된 영역이 readable, executable, private 퍼미션임을 알 수 있습니다. 08:01은 디바이스의 주번호(major)와 부번호(minor)번호를 알려주고 있습니다. 786932는 inode를 가리키는 값입니다. 

위에서 출력한 maps 파일의 각 라인을 자세히 살펴보면 /bin/bash 실행파일의 주소 매핑에 대한 출력임을 알 수 있습니다. 세세하게 분석하게 되면 다양한 정보를 알 수 있게됩니다.

#### Code segment

code segment는 text segment라고도 불리며 많은 사람들이 text segment가 더 익숙한 용어일 수 있습니다. text segment는 text section을 포함하고 있는 segment입니다. Code segment에는 실행할 수 있는 모든 코드들이 모여있는 메모리 공간입니다. 

```bash
00400000-004f4000 r-xp 00000000 08:01 786932                             /bin/bash
```

실행할 수 있는 코드가 저장된 메모리 공간은 읽고, 실행할 수 있는 권한은 필요하나, 쓰기 권한은 필요없습니다. 조금만 생각해 보시면 왜 그런지 쉽게 이해 되실텐데요, 실행되는 코드가 변경되어 버린다면 여러 다른 문제들이 발생할 수 있습니다. 

gbd와 같은 디버거를 사용하여 프로그램을 디버깅 할 때 이런 메모리 구조를 이해하는 건 매우 중요합니다. 디버깅 시에 다양한 메모리 주소에 대해 살펴볼 것이고 그때 해당 주소가 어느 메모리 특성을 가진 세그먼트, 섹션에 해당하는지 빠르게 파악할 수 있기 때문이죠.

#### Data Segment

우리는 지금 /bin/bash 프로그램을 이용한 예제를 살펴보고 있습니다.  그 중 Data segment에 대해 조금 살펴보겠습니다.

```bash
006f4000-006fd000 rw-p 000f4000 08:01 786932                             /bin/bash
```

위의 mapping 정보를 보면 code segment와 매우 유사함을 알 수 있습니다. 차이를 보면 매핑되는 주소와 퍼미션이 다르네요. data 영역은 읽고 쓸수는 있지만, 실행할 수 있는 영역은 아닙니다. 즉 실행 가능한 Instruction을 메모리 상에 작성한다 하여도 실행할 수 없습니다. 데이터는 계속 변해야하므로 쓰기 권한도 가지게 됩니다. Data segments에 위치하는 변수들은 초기화된 전역변수들입니다. 간단한 프로그램을 작성하여 전역변수를 주소를 출력해 보면 위와 같은 데이터 영역 내에 존재할 것 입니다. 변수, 데이터가 위치하는 또다른 곳은 stack와 heap 영역입니다. 각 영역은 나중에 살펴볼 예정입니다.

#### Mapped Base / Shard Libraries

계속해서 maps 파일에서 살펴볼 부분은 mapped based address라고 불리워지는 부분입니다. 이 영역은 실행파일의 공유 라이브러리가 로드되는 위치를 정의하고 있습니다.

```bash
7f989fdd4000-7f989fdd5000 r--p 00025000 08:01 6822291                    /lib/x86_64-linux-gnu/ld-2.23.so
7f989fdd5000-7f989fdd6000 rw-p 00026000 08:01 6822291                    /lib/x86_64-linux-gnu/ld-2.23.so
```

/lib/x86_64-linux-gnu/ld-2.23.so 는 프로세스가 시작할 때 처음으로 로드되어지는 공유 라이브러리입니다. 링커 자기 자신이죠. 기본적으로 하나 이상의 공유 라이브러리에서 링크되는 실행 파일을 만들 때 링커는 실행 파일에 암시 적으로 링크됩니다. 링커가 모든 외부 요소를 해결해야하기 때문에 심볼을 링크 된 공유 라이브러리에 저장하려면 먼저 메모리에 매핑해야하며, 이것이 링커가 항상 실행 파일에 표시되는 첫 번째 공유 라이브러리가되는 이유입니다. 링커가 매핑된 이후 실행파일에 연관된 모든 공유 라이브러리가 maps 파일에 나타납니다. maps 파일은 보시면 다양한 공유 라이브러리들이 매핑된 것을 볼 수 있습니다. ldd 명령어를 보면 더 정리된 내용으로 한눈에 보실 수 있습니다.

```bash
linuxias@desktop:~/privateProject/Example/linux/proc (master)$ ldd /bin/bash
        linux-vdso.so.1 =>  (0x00007ffddbae1000)
        libtinfo.so.5 => /lib/x86_64-linux-gnu/libtinfo.so.5 (0x00007fb113665000)
        libdl.so.2 => /lib/x86_64-linux-gnu/libdl.so.2 (0x00007fb113461000)
        libc.so.6 => /lib/x86_64-linux-gnu/libc.so.6 (0x00007fb113097000)
        /lib64/ld-linux-x86-64.so.2 (0x00007fb11388e000)
```


### /proc/[pid]/cmdline

cmdline 파일은 프로세스의 전체 argv를 포함하고 있습니다. 해당 프로세스가 실행 될 때 어떤 Argument가 전달되었는지 판단하기 유용합니다. 

### /proc/[pid]/environ

environ 파일은 프로세스의 현재 environment를 확인할 수 있습니다. 프로세스의 스택 메모리와 바로 연결되어 있습니다. environ 파일은 프로세스의 현재 환경변수 설정값을 알아야 할 때 유용하게 사용할 수 있습니다. getenv()나 putenv(), setenv()와 같은 environment와 관련된 함수들을 이용해 프로그래밍 할 때 관련된 문제점을 확인하는데 필요합니다.

### /proc/[pid]/mem

mem 파일은 프로세스의 매핑된 메모리 정보를 보여줍니다. 메모리 주소가 프로세스에서 매핑되지 않은 경우 EIO가 반환됩니다. 또한 /proc/<pid>/mem 파일의 권한은 r--/---/--- 이기에 다른 프로세스에서 mem 파일을 읽으려 한다면 ESRCH 에러가 발생합니다. 


### /proc/[pid]/fd

fd 디렉토리는 해당 프로세스가 현재 가지고 있는 File descriptor를 가리키는 심볼릭 링크들이 있습니다. 파일의 이름은 File descriptor의 번호로써 stdin은 0, stdout은 1, stderr는 2란 이름의 파일로 존재할 겁니다. File descriptor Leak 문제는 흔히 발생하지만 쉽게 해결하지 못하는 문제인데요, 디버깅 하는 프로세스가 종료되는 시점까지 fd가 close 되지 않는 문제들에 대해 /proc/<pid>/fd 를 이용하면 좀 더 문제 해결에 도움이 될 것 같습니다. 

### /proc/[pid]/statm

`statm` 항목은 프로세스의 메모리 사용 정보를 확인할 수 있다.

```bash
$cat statm
14536 1689 1136 195 0 555 0
```
앞에서부터 순차적으로 7개의 값을 표기한다.

1. 프로그램 총 크기 (KB)
2. 메모리 영역 크기 (KB)
3. 공유 페이지 수
4. 코드 페이지 수
5. 데이터/스택 페이지 수
6. 라이브러리 페이지 수
7. Dirty 페이지 수

위 결과를 보면 프로그램의 총 크기는 14MB이며 메모리영역은 1.6MB이다. 

