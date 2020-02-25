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

#### /proc/[pid]

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

#### /proc/[pid]/maps

maps 파일은 프로세스의 메모리 주소공간의 정보를 제공합니다. 모든 프로세스는 각각 가상 메모리 관리자(Virtual memory manager)에 의해 제공되는 메모리를 가지고 있는거 아시죠? maps는 그 정보들을 확인할 수 있습니다. 현재 프로세스의 maps를 확인해봅시다. 저는 기본 shell로 zsh을 사용하고 있기때문에 /proc/self 는 zsh입니다.

```bash
cat maps
559896121000-5598961e4000 r-xp 00000000 08:01 14946279                   /bin/zsh
5598963e4000-5598963e6000 r--p 000c3000 08:01 14946279                   /bin/zsh
5598963e6000-5598963ec000 rw-p 000c5000 08:01 14946279                   /bin/zsh
5598963ec000-559896400000 rw-p 00000000 00:00 0 
5598964f0000-5598978fc000 rw-p 00000000 00:00 0                          [heap]
7f54c0314000-7f54c0394000 rw-p 00000000 00:00 0 
7f54c0bff000-7f54c0c0f000 r-xp 00000000 08:01 12982839                   /usr/lib/x86_64-linux-gnu/zsh/5.4.2/zsh/computil.so
7f54c0c0f000-7f54c0e0e000 ---p 00010000 08:01 12982839                   /usr/lib/x86_64-linux-gnu/zsh/5.4.2/zsh/computil.so
7f54c0e0e000-7f54c0e0f000 r--p 0000f000 08:01 12982839                   /usr/lib/x86_64-linux-gnu/zsh/5.4.2/zsh/computil.so
7f54c0e0f000-7f54c0e10000 rw-p 00010000 08:01 12982839                   /usr/lib/x86_64-linux-gnu/zsh/5.4.2/zsh/computil.so
7f54c0e10000-7f54c0e12000 r-xp 00000000 08:01 12982856                   /usr/lib/x86_64-linux-gnu/zsh/5.4.2/zsh/regex.so
7f54c0e12000-7f54c1011000 ---p 00002000 08:01 12982856                   /usr/lib/x86_64-linux-gnu/zsh/5.4.2/zsh/regex.so
....
```

maps에 관련되서는 추가로 살펴볼 내용들이 많습니다. 이 다음 글에서 maps 파일에 관련된 내용만을 살펴볼 예정이니 지금은 여기에서 정리하고 다음으로 넘어가겠습니다.

#### /proc/[pid]/cmdline

cmdline 파일은 프로세스의 전체 argv를 포함하고 있습니다. 해당 프로세스가 실행 될 때 어떤 Argument가 전달되었는지 판단하기 유용합니다. 

#### /proc/[pid]/environ

environ 파일은 프로세스의 현재 environment를 확인할 수 있습니다. 프로세스의 스택 메모리와 바로 연결되어 있습니다. environ 파일은 프로세스의 현재 환경변수 설정값을 알아야 할 때 유용하게 사용할 수 있습니다. getenv()나 putenv(), setenv()와 같은 environment와 관련된 함수들을 이용해 프로그래밍 할 때 관련된 문제점을 확인하는데 필요합니다.

#### /proc/[pid]/mem

mem 파일은 프로세스의 매핑된 메모리 정보를 보여줍니다. 메모리 주소가 프로세스에서 매핑되지 않은 경우 EIO가 반환됩니다. 또한 /proc/<pid>/mem 파일의 권한은 r--/---/--- 이기에 다른 프로세스에서 mem 파일을 읽으려 한다면 ESRCH 에러가 발생합니다. 


#### /proc/[pid]/fd

fd 디렉토리는 해당 프로세스가 현재 가지고 있는 File descriptor를 가리키는 심볼릭 링크들이 있습니다. 파일의 이름은 File descriptor의 번호로써 stdin은 0, stdout은 1, stderr는 2란 이름의 파일로 존재할 겁니다. File descriptor Leak 문제는 흔히 발생하지만 쉽게 해결하지 못하는 문제인데요, 디버깅 하는 프로세스가 종료되는 시점까지 fd가 close 되지 않는 문제들에 대해 /proc/<pid>/fd 를 이용하면 좀 더 문제 해결에 도움이 될 것 같습니다. 

