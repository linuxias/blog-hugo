+++
title = "[Memory] heap chunk"
+++

C언어로 프로그래밍 할 때, 동적메모리 사용을 위해 많이 사용하는 **malloc()**은 glibc 내에 구현되어 있고, UNIX multi-thread 환경을 고려하여 설계된 ptmalloc2를 기반으로 작성되었다.

어플리케이션 내의 메모리 할당/해제 동작원리는 간단하게 설명하면 다음과 같다.

어플리케이션에서 **malloc()** 함수 호출 시 처음 linux kernel에 가상메모리를 요청(brk 또는 mmap을 이용하여)하게 되고, 그 영역은 어플리케이션의 가상메모리에 매핑된다. 그 이후에 **free()**를 호출하여 해제된 메모리는 커널에 반환되는 것이 아닌 glibc 내에서 free-list 로 관리되고, 어플리케이션에서 재 요청시 관리하던 free-list에서 재 할당하게 된다.

#### 용어정리
##### Arena
 - 하나 이상의 힙에 대한 참조와 "Free" 힙 내에 연결된 Chunk list(Linked List 구조)를 포함하는 하나 이상의 스레드 간 에 공유되는 구조체(?). 각 Arena에 할당된 스레드는 해당 Arena의 'Free List'에서 메모리를 할당한다.

##### Heap
 - 할당할 **Chunk**로 세분화되는 메모리의 연속적인 영역으로 각 **Heap**은 정확히 하나의 **Arena**에 속한다.

##### Chunk
 - 할당 (Application 소유), 해제 (glibc 소유) 또는 인접한 **chunk**와 더 큰 범위로 결합 할 수있는 작은 범위의 메모리. **chunk**는 어플리케이션에 제공되는 메모리 블록 주위의 래퍼입니다. 각 청크는 하나의 힙에 존재하며 하나의 경기장에 속한다.

##### Memory
 - 일반적으로 RAM 또는 swap 영역에 위치하는 Application 주소 영역의 부분.

위에서 정리한 용어 중 가장 중점적으로 봐야할 부분이 **chunk** 이다. **chunk**는 오직 메모리만 있는 것이 아닌, 몇 가지 정보를 보유한다. 그렇기에 우리가 **free()** 함수 호출 시 **malloc()**과 달리 메모리 사이즈를 전달하지 않아도 해당 메모리가 해제된다. 그 말은 사이즈 정보를 **chunk**에서 저장하고 있다고 예상할 수 있다. 즉 metadata를 가지고 있는 구조이다.

#### Chunk
**chunk**의 구조는 아래와 같다. 

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

**malloc()** 함수 호출 시 Application에서 할당에 요청하는 메모리 영역외에 크기를 저장하는 metadata 까지 할당된다. 그 외에서 해제된 **free-list**에 존재하는 chunk를 관리하기 위해 double linked list와 인접한 이전 **chunk**를 병합할 때 사용하는 필드까지 포함하여 다양한 정보가 구조체 내에 존재한다.

**malloc_chunk** 자료구조는 이해하기 쉽지 않다. 처음에 매우 혼란스러웠다. 하지만 꼭 필요한 이해과정이라 최대한 정리해두려 한다. 이 자료구조는 Application에 의해 할당 되었을 때와 해제 된 경우로 나눠서 살펴봐야 한다. **malloc_chunk** 자료구조에는 다양한 필드가 존재하지만, 메모리가 할당 되었을 때 사용하는 필드는 오직 **mchunk_size** 하나 뿐이다. 나머지 필드는 메모리가 해제되어 glibc에서 의해 free-list 내에서 관리될 때 사용하게 된다.



![chunk](http://pds16.egloos.com/pds/200912/25/35/c0098335_4b34adcad42cd.png)


#### Reference
- http://studyfoss.egloos.com/5206220
- https://sourceware.org/glibc/wiki/MallocInternals
- https://sploitfun.wordpress.com/2015/02/10/understanding-glibc-malloc/
- https://heap-exploitation.dhavalkapil.com/diving_into_glibc_heap/
