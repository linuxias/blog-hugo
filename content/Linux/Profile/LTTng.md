+++
title = "LTTng:userspace"
+++

LTTng는 리눅스 프로파일링을 위한 오픈소스 트레이싱 프레임워크입니다. LTTng는 `Linux Trace Toolkit: next generation` 의 약자로 리눅스 커널, 사용자 어플리케이션(C/C++, java, python), 라이브러리를 트레이싱할 수 있습니다.

### LTTng 구성

LTTng 는 크게 3가지 모듈로 구성되어 있습니다.
 - lttng-ust : 사용자 어플리케이션을 추적하기 위한 라이브러리
 - lttng-tools : tracing session을 관리, 제어하기 위한 라이브러리들과 명령어 인터페이스
 - lttng-modules : 커널 추적을 위한 리눅스 커널 모듈

각 모듈에 대한 설명과 설치 방법 등은 아래 URL을 참조하면 됩니다.
https://github.com/lttng
https://lttng.org

여기서는 간략하게 설명하고 넘어가겠습니다.

![Components_of_LTTng](https://lttng.org/docs/v2.11/images/plumbing.png)
(src : https://lttng.org/docs/v2.11/images/plumbing.png)

### 사용자 어플리케이션 트레이싱하기

여기의 내용은 LTTng docs 의 [User space instrumentation for C and C++ applications](https://lttng.org/docs/v2.11/#doc-c-application) 기반으로 작성되었습니다.

사용자 어플리케이션 분석을 위해서는 `lttng-ust` 와 `lttng-tools` 컴포넌트가 필요합니다. 
각 설치에 대해서는 여기서 다루진 않겠습니다.

C/C++ 기반 사용자 어플리케이션 트레이싱 절차는 아래와 같습니다.
1. tracepoint provider 패키지의 소스 파일 생성하기
2. 어플리케이션 소스 코드에 tracepoint 추가하기
3. tracepointer provider 패키지와 사용자 어플리케이션을 빌드 및 링크하기

각 순서대로 정리하겠습니다.

#### 1. tracepoint provider 패키지의 소스 파일 생성하기

`tracepoint provider`는 LTTng-UST에 의해 제공되는 tracepoints로 사용자 어플리케이션에 포함된 함수들의 집합입니다. 각 함수들은 사용자가 정의한 필드가 있는 이벤트를 전송하고 해당 이벤트는 LTTng-UST channel 버퍼에 저장됩니다. 저장된 데이터는 consumer daemon으로 전송됩니다.

![User application linked with liblttng-ust](https://lttng.org/docs/v2.11/images/ust-app.png)

이 기능을 사용하기 위한 `tracepoint provider package`는 object 파일이나 shared library 형태입니다. 하나 이상의 `tracepoin provider header (.h)`와 `tracepoint provider pacage source (.c)`를 포함하고 있습니다.


##### Header 파일 템플릿 생성하기.

```c
#undef TRACEPOINT_PROVIDER
#define TRACEPOINT_PROVIDER provider_name

#undef TRACEPOINT_INCLUDE
#define TRACEPOINT_INCLUDE "./tp.h"

#if !defined(_TP_H) || defined(TRACEPOINT_HEADER_MULTI_READ)
#define _TP_H

#include <lttng/tracepoint.h>

/*
 * Use TRACEPOINT_EVENT(), TRACEPOINT_EVENT_CLASS(),
 * TRACEPOINT_EVENT_INSTANCE(), and TRACEPOINT_LOGLEVEL() here.
 */

#endif /* _TP_H */

#include <lttng/tracepoint-event.h>
```

위 템플릿에서 2번 째 라인의 provider_name은 자신의 `tracepoint provider` 이름으로 변경해주면 됩니다. 주의할 점은 `tracepoint provider` 이름은 항상 유니크해야 합니다. 만약 여러 사용자가 개발한 어플리케이션이 하나의 머신에서 동작하는 경우 각각의 네임이 충돌할 수 있으니 본인만을 위한 prefix를 넣어주는 것을 추천합니다. 그 다음 위 Header 파일의 이름을 `*tp.h` 로 변경해주세요.

이제 주석으로 처리된 부분에 `TRACEPOINT`를 정의해 줍니다. `TRACEPOINT()` 매크로는 아래와 같이 구성됩니다.
```c
TRACEPOINT_EVENT(
    /* Tracepoint provider name */
    provider_name,

    /* Tracepoint name */
    tracepoint_name,

    /* Input arguments */
    TP_ARGS(
        arguments
    ),

    /* Output event fields */
    TP_FIELDS(
        fields
    )
)
```

각 항목은 정의는 다음과 같습니다.
- provider_name : `tracepoint provider` 이름
- tracepoint_name : `tracepoint` 이름
- arguments : 입력 아큐먼트, 개수의 제한은 없습니다. 정의 방식은 타입, 네임이며 `,` 를 이용하여 구분합니다.
- fields : 출력 이벤트 필드 정의

```c
#undef TRACEPOINT_PROVIDER
#define TRACEPOINT_PROVIDER linuxias_hello_world

#undef TRACEPOINT_INCLUDE
#define TRACEPOINT_INCLUDE "./test_tp.h"

#if !defined(_TEST_TP_H) || defined(TRACEPOINT_HEADER_MULTI_READ)
#define _TEST_TP_H

#include <lttng/tracepoint.h>

TRACEPOINT_EVENT (
	linuxias_hello_world,
	my_first_tracepoint,
	TP_ARGS(
		int, my_integer_arg,
		char*, my_string_arg
	),
	TP_FIELDS(
		ctf_string(my_string_field, my_string_arg)
		ctf_integer(int, my_integer_field, my_integer_arg)
	)
)

#endif /* _TEST_TP_H */

#include <lttng/tracepoint-event.h>
```

##### Source 파일 생성하기.

Source 파일은 매우 단순합니다. 아래 내용 외에 추가할 부분은 없습니다.

```c
#define TRACEPOINT_CREATE_PROBES
#define TRACEPOINT_DEFINE
#include "test_tp.h"
```

#### 2. 어플리케이션 소스 코드에 tracepoint 추가하기

```c
#include "stdio.h"
#include "test_tp.h"

int main(int argc, char *argv[])
{
	puts("Press Enter to continue");
	getchar();
	tracepoint(linuxias_hello_world, my_first_tracepoint, argc, argv[0]);
	puts("The end");
	return 0;
}
```

#### 3. tracepointer provider 패키지와 사용자 어플리케이션을 빌드 및 링크하기

Makefile을 이용하여 빌드하였습니다.

```Makefile
TARGET = lttng_ust_test

CC = gcc
CFLAGS = -c -g
LDFLAGS = -llttng-ust -ldl

INC+=-I.

OBJS+=tp.o
OBJS+=main.o

all : $(TARGET)

$(TARGET) : $(OBJS)
	$(CC) -o $@ $^ $(LDFLAGS)

.c.o:
	$(CC) $(INC) $(CFLAGS) $<

clean:
	rm -f $(OBJS) $(TARGET)
```

#### 결과 확인하기.
트레이스 이벤트가 등록되었는지 확인합니다.

```bash
$lttng-sessiond --daemonize
$./lttng_ust_test
```

실행하면 정지해 있습니다. 다른 창에서 확인해 보면.. 아래와 같이 이벤트가 등록되어 있음을 확인할 수 있습니다. 만약 프로세스가 종료되면 이벤트들은 모두 사라지면 확인할 수 없습니다.

```bash
$ lttng list --userspace
UST events:
-------------

PID: 17109 - Name: ./lttng_ust_test
      lttng_ust_tracelog:TRACE_DEBUG (loglevel: TRACE_DEBUG (14)) (type: tracepoint)
      lttng_ust_tracelog:TRACE_DEBUG_LINE (loglevel: TRACE_DEBUG_LINE (13)) (type: tracepoint)
      lttng_ust_tracelog:TRACE_DEBUG_FUNCTION (loglevel: TRACE_DEBUG_FUNCTION (12)) (type: tracepoint)
      lttng_ust_tracelog:TRACE_DEBUG_UNIT (loglevel: TRACE_DEBUG_UNIT (11)) (type: tracepoint)
      lttng_ust_tracelog:TRACE_DEBUG_MODULE (loglevel: TRACE_DEBUG_MODULE (10)) (type: tracepoint)
      lttng_ust_tracelog:TRACE_DEBUG_PROCESS (loglevel: TRACE_DEBUG_PROCESS (9)) (type: tracepoint)
      lttng_ust_tracelog:TRACE_DEBUG_PROGRAM (loglevel: TRACE_DEBUG_PROGRAM (8)) (type: tracepoint)
      lttng_ust_tracelog:TRACE_DEBUG_SYSTEM (loglevel: TRACE_DEBUG_SYSTEM (7)) (type: tracepoint)
      lttng_ust_tracelog:TRACE_INFO (loglevel: TRACE_INFO (6)) (type: tracepoint)
      lttng_ust_tracelog:TRACE_NOTICE (loglevel: TRACE_NOTICE (5)) (type: tracepoint)
      lttng_ust_tracelog:TRACE_WARNING (loglevel: TRACE_WARNING (4)) (type: tracepoint)
      lttng_ust_tracelog:TRACE_ERR (loglevel: TRACE_ERR (3)) (type: tracepoint)
      lttng_ust_tracelog:TRACE_CRIT (loglevel: TRACE_CRIT (2)) (type: tracepoint)
      lttng_ust_tracelog:TRACE_ALERT (loglevel: TRACE_ALERT (1)) (type: tracepoint)
      lttng_ust_tracelog:TRACE_EMERG (loglevel: TRACE_EMERG (0)) (type: tracepoint)
      lttng_ust_tracef:event (loglevel: TRACE_DEBUG (14)) (type: tracepoint)
      lttng_ust_lib:unload (loglevel: TRACE_DEBUG_LINE (13)) (type: tracepoint)
      lttng_ust_lib:debug_link (loglevel: TRACE_DEBUG_LINE (13)) (type: tracepoint)
      lttng_ust_lib:build_id (loglevel: TRACE_DEBUG_LINE (13)) (type: tracepoint)
      lttng_ust_lib:load (loglevel: TRACE_DEBUG_LINE (13)) (type: tracepoint)
      lttng_ust_statedump:end (loglevel: TRACE_DEBUG_LINE (13)) (type: tracepoint)
      lttng_ust_statedump:debug_link (loglevel: TRACE_DEBUG_LINE (13)) (type: tracepoint)
      lttng_ust_statedump:build_id (loglevel: TRACE_DEBUG_LINE (13)) (type: tracepoint)
      lttng_ust_statedump:bin_info (loglevel: TRACE_DEBUG_LINE (13)) (type: tracepoint)
      lttng_ust_statedump:start (loglevel: TRACE_DEBUG_LINE (13)) (type: tracepoint)
      linuxias_hello_world:my_first_tracepoint (loglevel: TRACE_DEBUG_LINE (13)) (type: tracepoint)
```

위에 등록된 이벤트를 이용하여 babeltrace로 확인해 봅시다.

```bash
$lttng create
Session auto-20200215-175158 created.
Traces will be output to /home/linuxias/lttng-traces/auto-20200215-175158

$lttng enable-event --userspace linuxias_hello_world:my_first_tracepoint
UST event linuxias_hello_world:my_first_tracepoint created in channel channel0

$lttng start
Tracing started for session auto-20200215-175158
// 예제 프로세스를 종료합니다.

$lttng destroy
Waiting for destruction of session "auto-20200215-175158"...
Session "auto-20200215-175158" destroyed

$babeltrace ~/lttng-traces
[17:52:13.462294498] (+412.893280571) desktop linuxias_hello_world:my_first_tracepoint: { cpu_id = 0 }, { my_string_field = "./lttng_ust_test", my_integer_field = 4 }
```

`babeltrace`를 이용하면 문자열 기반의 로그 형태로 확인을 할 수 있습니다.

### Userspace 친구들

LTTng는 사용자 영역 트레이싱을 위해 추가적인 라이브러리들을 지원합니다.

|Library|Description|
|---|---|
|`liblttng-ust-libc-warrper.so`|C 표준 라이브러리 추적 도구|
|`liblttng-ust-pthread-wrapper.so`|POSIX pthread 함수 추적 도구|
|`liblttng-ust-cyg-profile.so`|함수의 진입과 종료에 대한 추적 도구. 주의할 점은 이 라이브러리를 사용하기 위해서 어플리케이션이 `-finstrument-function` 컴파일 플래그가 적용되어 컴파일 되어야 한다. |
|`liblttng-ust-dl.so`|`dlopen(3)`, `dlclose(3)` 함수 호출 추적 도구|

위 라이브러리들은 어플리케이션 빌드 시 링크될 필요가 없이 `LD_PRELOAD`를 이용하여 어플리케이션에 적용할 수 있다.
```bash
$ LD_PRELOAD=liblttng-ust-dl.so my-app
$ LD_PRELOAD=liblttng-ust-cyg-profile.so:liblttng-ust-libc-warrper.so my-app
```

위 처럼 어플리케이션 실행 시 각 라이브러리의 이벤트가 등록되고 `lttng list --userspace` 명령어를 통해 확인할 수 있다.
아래는 [LTTng userspace helper test](https://github.com/linuxias/Example/tree/master/blog/LTTng/lttng-ust-example/lttng-ust-helper])를 빌드하여 순차적으로 적용한 결과 중 일부분을 가져온 내용이다. 예제 코드 내에 재귀함수가 있기에 `func_entry` 이벤트가 순차적으로 발생한 후 `func_exit` 이벤트가 발생하는 것을 확인할 수 있다.
```bash
$babeltrace ~/lttng-traces/auto-20200218-204301
...
[20:38:11.907388453] (+3.229928201) desktop lttng_ust_libc:malloc: { cpu_id = 1 }, { size = 4, ptr = 0x151BD40 }
[20:38:11.907391355] (+0.000002902) desktop lttng_ust_libc:free: { cpu_id = 1 }, { ptr = 0x151BD40 }
[20:38:11.907395975] (+0.000004620) desktop lttng_ust_cyg_profile:func_entry: { cpu_id = 1 }, { addr = 0x4006B6, call_site = 0x40076D }
[20:38:11.907396463] (+0.000000488) desktop lttng_ust_cyg_profile:func_entry: { cpu_id = 1 }, { addr = 0x4006B6, call_site = 0x4006ED }
[20:38:11.907396778] (+0.000000315) desktop lttng_ust_cyg_profile:func_entry: { cpu_id = 1 }, { addr = 0x4006B6, call_site = 0x4006ED }
[20:38:11.907397090] (+0.000000312) desktop lttng_ust_cyg_profile:func_entry: { cpu_id = 1 }, { addr = 0x4006B6, call_site = 0x4006ED }
[20:38:11.907397410] (+0.000000320) desktop lttng_ust_cyg_profile:func_entry: { cpu_id = 1 }, { addr = 0x4006B6, call_site = 0x4006ED }
[20:38:11.907397722] (+0.000000312) desktop lttng_ust_cyg_profile:func_entry: { cpu_id = 1 }, { addr = 0x4006B6, call_site = 0x4006ED }
[20:38:11.907398032] (+0.000000310) desktop lttng_ust_cyg_profile:func_entry: { cpu_id = 1 }, { addr = 0x4006B6, call_site = 0x4006ED }
...
[20:38:11.907402689] (+0.000000283) desktop lttng_ust_cyg_profile:func_exit: { cpu_id = 1 }, { addr = 0x4006B6, call_site = 0x4006ED }
[20:38:11.907402972] (+0.000000283) desktop lttng_ust_cyg_profile:func_exit: { cpu_id = 1 }, { addr = 0x4006B6, call_site = 0x4006ED }
[20:38:11.907403263] (+0.000000291) desktop lttng_ust_cyg_profile:func_exit: { cpu_id = 1 }, { addr = 0x4006B6, call_site = 0x4006ED }
[20:38:11.907403543] (+0.000000280) desktop lttng_ust_cyg_profile:func_exit: { cpu_id = 1 }, { addr = 0x4006B6, call_site = 0x4006ED }
[20:38:11.907403818] (+0.000000275) desktop lttng_ust_cyg_profile:func_exit: { cpu_id = 1 }, { addr = 0x4006B6, call_site = 0x4006ED }
[20:38:11.907404092] (+0.000000274) desktop lttng_ust_cyg_profile:func_exit: { cpu_id = 1 }, { addr = 0x4006B6, call_site = 0x4006ED }
[20:38:11.907404378] (+0.000000286) desktop lttng_ust_cyg_profile:func_exit: { cpu_id = 1 }, { addr = 0x4006B6, call_site = 0x40076D }
[20:38:11.907404684] (+0.000000306) desktop lttng_ust_cyg_profile:func_exit: { cpu_id = 1 }, { addr = 0x40070D, call_site = 0x7FD716ED8830 }
...
```

### 추적 결과 확인하기

결과를 확인하는 방법은 여러 가지가 있습니다. LTTng docs에서 제시하는 방법은 3가지 입니다.

- babeltrace
- tracecompass
- lttng-analyses

이 중 tracecompass를 사용하려 합니다. tracecompass는 eclipse 기반의 분석 툴로서 lttng-analyses를 External analyses로 플러그인 처럼 추가하여 사용할 수 있습니다. lttng-analyses는 babeltrace를 필요로 하기에 3가지 툴 모두 설치를 하여야 합니다.

tracecompass를 이용하여 lttng-traces 결과를 import 하게되면 아래와 같이 표시됩니다. tracecomposs는 `userspace`보다는 `kernel` 트레이싱 시에 더 큰 효율을 발휘 합니다.

![tracecompass](https://drive.google.com/uc?id=12g6ANZiTl59J3RkiuYoORTZNV1b_n6Db)

사용자 영역에 대해서는 로그 수준의 화면만 확인할 수 있습니다.

감사합니다.