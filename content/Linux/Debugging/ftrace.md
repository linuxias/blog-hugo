+++
title = "ftrace"
+++


|name|information|note|
|-|-|-|
|`current_tracer`|현재 설정된 tracer 값||
|`available_tracers`|tracer 가능한 정보들, 커널 컴파일 시 설정될 수 있다. 활성화 위해선 current_tracer에 이벤트를 작성한다.||
|`tracing_on`|ftrace ring buffer을 활성화할지 여부를 결정한다. 트레이서를 비활성화하려면 이 파일에 0을, 활성화하려면 1을 각각 설정하십시오. 참고, 이는 링 버퍼에 대한 쓰기를 비활성화할 뿐이며, 추적 오버헤드는 여전히 발생할 수 있다.||
|`trace`|사람 친화적인, 즉 텍스트 형태로 트레이스 결과가 출력된다. 다만 이 파일을 읽는 동안은 트레이스가 일시적으로 중단된다.||
|`trace_pipe`|`trace`와는 달리 실시간 트레이싱 정보를 파일로 확인할 수 있다.||
|`trace_options`|이 파일을 사용하여 `trace`, `trace_pipe`에서 출력되는 데이터 양을 조절할 수 있습니다.|제거시에는 no를 prefix로 설정|
|`options`|출력 파일에 표시되어지는 데이터를 조절할 수 있는 파일들이 모여있다. '1' 또는 '0'으로 각 파일을 설정 할 수 있다.||
|`tracing_max_latency`|||

## ftrace events

### 1. Sched

|name|information|note|
|-|-|-|
|`sched_wakeup`|프로세스가 sleep 상태에서 Wake up 되어 실행 대기가 되는 순간 ||
|`sched_switch`|프로세스가 Wait 상태에서 스케줄링 되어 CPU 자원을 할당 받는 순간||




## Example

### 현재 설정된 tracer 정보 확인 및 새로운 tracer 추가하기.
TAG : `available_tracer`, `current_tracer`

```bash
$cat /sys/kernel/debug/tracing/current_tracer
nop
$cat /sys/kernel/debug/tracing/available_tracer
blk function_graph wakeup_dl wakeup_rt wakeup irqsoff function nop
$echo "function_graph" > /sys/kernel/debug/tracing/current_tracer
$cat current_tracer 
function_graph
```

### Tracing 활성화하기
```bash
#echo 1 > /sys/kernel/debug/tracing/tracing_on
```

### Tracing 결과 확인하기
```bash
#cat /sys/kernel/debug/tracing/trace // 정적으로 확인하기
#cat /sys/kernel/debug/tracing/trace_pipe // 스트림으로 실시간 확인하기
```

### 옵션 확인하기, 활성화 하기.
```bash
#cat trace_options 
print-parent
nosym-offset
nosym-addr
noverbose
...
noevent-fork
function-trace
nofunction-fork
nodisplay-graph
nostacktrace
notest_nop_accept
notest_nop_refuse

//stacktrace 활성화 하기
echo "stracetrace" > trace_options
```


### Scheduler Event 활성화 하기

sched_switch, sched_wakeup을 많이 사용하며 추가적으로 커널 버전에 따라 sched_waking, sched_blocked_reason, sched_cpu_hotplug, sched_process_exit 등 다양한 이벤트를 함께 활용한다.

```bash
#echo 1 > /sys/kernel/debug/tracing/events/sched/sched_switch/enable
#echo 1 > /sys/kernel/debug/tracing/events/sched/sched_wakeup/enable
```

## Reference
[kernel - ftrace](https://www.kernel.org/doc/Documentation/trace/ftrace.txt)

