+++
title = "[Memory] heap"
+++

C언어로 프로그래밍 할 때, 동적메모리 사용을 위해 많이 사용하는 **malloc()**은 glibc 내에 구현되어 있고, UNIX multi-thread 환경을 고려하여 설계된 **ptmalloc2**를 기반으로 작성되었다.

어플리케이션 내의 메모리 할당/해제 동작원리는 간단하게 설명하면 다음과 같다.

어플리케이션에서 **malloc()** 함수 호출 시 처음 linux kernel에 가상메모리를 요청(brk 또는 mmap을 이용하여)하게 되고, 그 영역은 어플리케이션의 가상메모리에 매핑된다. 그 이후에 **free()**를 호출하여 해제된 메모리는 커널에 반환되는 것이 아닌 glibc 내에서 free-list 로 관리되고, 어플리케이션에서 재 요청시 관리하던 free-list에서 재 할당하게 된다.

### 히스토리

메모리 동적할당을 위해 다양한 라이브러리가 존재한다. 아래 작성된 내용 외에도 다양한 메모리 할당자들이 존재한다. 다양한 메모리 할당자가 존재하는 이유가 각기 다른 이유로 좀 더 빠르게, 효율적으로 메모리를 할당하기 위한 방법을 제공하려고 한다. 이 중 우리가 작성하는 어플리케이션에 가장 알맞은 메모리 할당자가 무엇인지는 좀 더 살펴볼 필요가 있다. 이 글에서는 따로 설명하지 않으려 한다.

- dlmalloc : General purpose allocator
- ptmalloc2 : glibc
- jemalloc : FreeBSD and Firefox
- tcmalloc : Google
- libumem : Solaris

앞서 간단히 설명했듯이 이 중 우리가 자주사용하는 glibc에 존재하는 **ptmalloc2**는 **dlmalloc**로 부터 시작되었다. **ptmalloc2**는 멀티스레드 환경을 고려하였다. 처음 메모리를 커널로부터 할당받아 여러 스레드가 사용하게 되면 임계영역에 대해 락 기능이 필요로 하게된다. 즉 하나의 스레드가 메모리 할당을 요청하여 할당이 완료되기 전까지는 다른 스레드는 대기하고 있어야한다는 의미이다. 이러한 문제를 해결하기 위해 각 스레드 별로 메모리 영역을 할당해주고 사용할 수 있도록 한다.

### 시스템콜 : brk, mmap

시작하기 앞서, **brk**와 **mmap** 시스템 콜 정리를 하고 시작하려 한다. 어플리케이션 개발자는 메모리 할당을 위해서 **malloc()** 함수를 호출하지만 그 내부에서는 **brk** 또는 **mmap** 중 하나를 호출하게 된다.

![brk_mmap_tree](https://docs.google.com/drawings/d/105HDvkEvIW2lsyaQjj758Lbyx6A-_K7jviheyzeAwl8/pub?w=480&h=238)

**brk()** 와 **mmap()**의 차이를 정리하고 넘어간다.

##### brk
**brk()**는 **brk** 위치에 커널로 부터 메모리를 할당받아 사용한다. **brk**는 힙 세그먼트의 끝 주소이며 시작 주소는 **start_brk**이다. 처음 프로그램이 시작되어 heap 영역을 할당 받지 않았다면, **brk**와 **start_brk**는 동일한 주소이다.

#### 용어정리
##### Arena
 - 하나 이상의 힙에 대한 참조와 "Free" 힙 내에 연결된 Chunk list(Linked List 구조)를 포함하는 하나 이상의 스레드 간 에 공유되는 구조체(?). 각 Arena에 할당된 스레드는 해당 Arena의 'Free List'에서 메모리를 할당한다. 멀티 스레드 응용 프로그램을 효율적으로 처리하기 위해 glibc의 malloc을 사용하면 한 번에 둘 이상의 메모리 영역을 활성화 할 수 있다. 따라서, 서로 다른 스레드는 서로 간섭하지 않고 서로 다른 메모리 영역에 액세스 할 수 있다. 이러한 메모리 영역을 통칭하여 **Arena**라고 한다. 어플리케이션의 초기 **heap** 영역을 **main arena**라고 한다.

##### Heap
 - 할당할 **Chunk**로 세분화되는 메모리의 연속적인 영역으로 각 **Heap**은 정확히 하나의 **Arena**에 속한다.

##### Chunk
 - 할당 (Application 소유), 해제 (glibc 소유) 또는 인접한 **chunk**와 더 큰 범위로 결합 할 수있는 작은 범위의 메모리. **chunk**는 어플리케이션에 제공되는 메모리 블록 주위의 래퍼입니다. 각 청크는 하나의 힙에 존재하며 하나의 경기장에 속한다.

##### Memory
 - 일반적으로 RAM 또는 swap 영역에 위치하는 Application 주소 영역의 부분.

위에서 정리한 용어 중 가장 중점적으로 봐야할 부분이 **chunk** 이다. **chunk**는 오직 메모리만 있는 것이 아닌, 몇 가지 정보를 보유한다. 그렇기에 우리가 **free()** 함수 호출 시 **malloc()**과 달리 메모리 사이즈를 전달하지 않아도 해당 메모리가 해제된다. 그 말은 사이즈 정보를 **chunk**에서 저장하고 있다고 예상할 수 있다. 즉 metadata를 가지고 있는 구조이다.

### Chunk
**chunk**의 구조를 그림으로 표현하면 아래와 같다. 

![chunk](http://pds16.egloos.com/pds/200912/25/35/c0098335_4b34adcad42cd.png)
(source : http://pds16.egloos.com/pds/200912/25/35/c0098335_4b34adcad42cd.png)

위 그림에서 할당된 메모리 영역의 헤더 정보는 아래와 같다.

```C
struct malloc_chunk {
  INTERNAL_SIZE_T      mchunk_prev_size;  /* Size of previous chunk (if free).  */
  INTERNAL_SIZE_T      mchunk_size;       /* Size in bytes, including overhead. */
  struct malloc_chunk* fd;                /* double links -- used only if free. */
  struct malloc_chunk* bk;
  /* Only used for large blocks: pointer to next larger size.  */
  struct malloc_chunk* fd_nextsize; /* double links -- used only if free. */
  struct malloc_chunk* bk_nextsize;
};

typedef struct malloc_chunk* mchunkptr;
```

**malloc_chunk** 자료구조의 필드를 항상 사용하는 것은 아니다. 메모리 할당된 경우와 해제된 메모리에 대해 사용하는 필드의 차이가 있다. 차이는 아래의 표로 정리하였다. 메모리가 할당되면 **mchunk_size** 만 사용된다. 나머지 필드는 usaable area로 사용된다.

||Allocated|Freed|
|---|---|---|
|mchnk_prev_size|X|O|
|mchunk_size|O|O|
|fd|X|O|
|bk|X|O|
|fd_nextsize|X|O|
|bk_nextsize|X|O|

위 필드 중 mchunk_size는 이름에서 알 수 있듯이 현재 chunk의 크기를 나타내는데, malloc() 호출 시 설정된다. 여기서 주의해야 할 점은 하위 3bit는 다른 용도로 사용된다는 것이다. 위의 그림에서 볼 수 있듯이 ** N, M, P ** 로 나뉜 3개의 bit의 역할은 다음과 같다.
 - N (Non Mained Arena) : 이 비트는 이 **chunk**가 **thread arena**에 속하는 경우에 설정된다.
 - M (MMap'd chunk) : 이 **chunk**가 mmap으로 설정된 경우 설정된다.
 - P (Previous chunk is in use) : 이 비트가 설정되어있다면 이전 chunk가 사용중이라는 의미이다. 따라서 prev_size 필드는 유효한 값이 아니다. 왜냐하면 prev_size는 메모리 Free 상태에서만 유용하기 때문이다.

**malloc()** 함수 호출 시 Application에서 할당에 요청하는 메모리 영역외에 크기를 저장하는 metadata 까지 할당된다. 그 외에서 해제된 **free-list**에 존재하는 chunk를 관리하기 위해 double linked list와 인접한 이전 **chunk**를 병합할 때 사용하는 필드까지 포함하여 다양한 정보가 구조체 내에 존재한다.

**malloc_chunk** 자료구조는 이해하기 쉽지 않다. 처음에 매우 혼란스러웠다. 하지만 꼭 필요한 이해과정이라 최대한 정리해두려 한다. 이 자료구조는 Application에 의해 할당 되었을 때와 해제 된 경우로 나눠서 살펴봐야 한다. **malloc_chunk** 자료구조에는 다양한 필드가 존재하지만, 메모리가 할당 되었을 때 사용하는 필드는 오직 **mchunk_size** 하나 뿐이다. 나머지 필드는 메모리가 해제되어 glibc에서 의해 free-list 내에서 관리될 때 사용하게 된다.

glibc의 malloc은 chunk를 중심으로 동작한다고 볼 수 있다. 


### Arena

Arena는 위에서 설명하였듯, 멀티 스레드 응용 프로그램을 효율적으로 처리하기 위해 glibc의 malloc을 사용하면 한 번에 둘 이상의 메모리 영역을 활성화 할 수 있다. 따라서, 서로 다른 스레드는 서로 간섭하지 않고 서로 다른 메모리 영역에 액세스 할 수 있다. 이러한 메모리 영역을 통칭하여 **Arena**라고 한다.

#### Arena의 개수는?

간단히 생각하면, 스레드 하나 당 **Arena**가 하나 씩 매핑된다고 생각할 수 있다. 하지만, 그 말은 틀린 맛이다. 바보같이 어플리케이션의 스레드 수를 현재 시스템의 코어 수보다 무수히 많이 만드는 멍청한 짓은 하지 않겠지만, 만약 그런 경우가 있다 하더라도 Arena를 스레드 마다 하나씩 생성하여 매핑하는 것은 바보 같은 짓이다.

어플리케이션의 **Arena** 제한은 현재 시스템 코어 수를 기반으로 정해진다. 조건은 아래와 같다.
```
For 32 bit systems:
     Number of arena = 2 * number of cores.
For 64 bit systems:
     Number of arena = 8 * number of cores.
```

만약 스레드가 4개라 하자. 메인스레드 하나와 사용자 스레드 3개로 구성된 어플리케이션이 있다. 32비트이며, 싱글코어인 시스템에는 해당 어플리케이션에서 **Arena**가 몇개 생성되는가? 위 계산식을 이용하면 2개가 맞다. 메인스레드는 **main arena**를 가지고 있으므로 별개로 고려하고, 사용자 스레드 1,2번이 생성되어 메모리 할당 요청 시 **Thread arena** 1, 2가 할당될 것이다. 그럼 마지막 스레드 3이 생성되면 어떤 **Arena**를 사용하게 되는가? 이런 경우 **Maina Arena**와 **Thread Arena**1, 2 중 하나를 사용하려고 시도한다. 시도는 해당 **Arena**를 락을 시도하여 성공한다면 사용자에게 그 **Arean**가 반환되고, 아니라면 다음 **Arena**에 대해 락을 시도한다. 스레드 3은 해당 **Arena**를 재사용하려고 계속 시도를 하게된다.


#### Reference
- http://studyfoss.egloos.com/5206220
- https://sourceware.org/glibc/wiki/MallocInternals
- https://sploitfun.wordpress.com/2015/02/10/understanding-glibc-malloc/
- https://heap-exploitation.dhavalkapil.com/diving_into_glibc_heap/
- https://sploitfun.wordpress.com/2015/02/11/syscalls-used-by-malloc/
