+++
title = "Process"
+++

# 1. Perf + FlameGraph

Process의 FlameGraph를 뽑아내는 과정을 설명한다. Perf의 `--call-graph` 기능을 이용한다.

### 1) Kernel setting
```bash
echo -1 > /proc/sys/kernel/perf_event_paranoid
echo 0 > /proc/sys/kernel/kptr_restrict
chmod a+r /proc/kallsyms
echo 100 > /proc/sys/kernel/perf_cpu_time_max_percent
echo performance > /sys/devices/system/cpu/cpu0/cpufreq/scaling_governor
```

### 2) Get perf data
```bash
perf record -o perf.data --call-graph=dwarf -F 15000 -p {PID}
perf script --max-stack 1024 --input=perf.data -f > out.perf
```

### 3) Create FlameGraph

```bash
c++filt < out.perf > out.perf_filted
stackcollapse-perf.pl --all out.perf_filted > out.perf_collapsed
flamegraph.pl -color=java --hash out.perf_collapsed > FlameGraph.svg
```

# 2. Count function call using gdb

### 1) Create breakpoint
 ```
 break func_name
 ...
 • [23] break number
```
### 2) Ignore break point
```
ignore 23 1000000   # set ignore count very high.
```
### 3) Run or Continue 
```
run                 # the program will SIGSEGV before reaching the ignore count.
                    # Once it stops with SIGSEGV:
```
### 4) Show count 
```
info break 23       # tells you how many times the breakpoint has been hit, 
                      # which is exactly the count you want
```
