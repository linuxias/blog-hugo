+++
title = "Resource Limitation"
+++

 컨테이너는 기본적으로 리소스 제한이 없으며 호스트의 커널 스케쥴러에 의해 허용되는 주어진 리소스를 사용할 수 있습니다. 가끔 각 컨테이너 별로 리소스(CPU, Memory 등)를 제한할 필요가 생길 수 있습니다. 이런 경우를 위해 도커에서는 컨테이너 별로 어느 정도의 CPU, Memory 자원을 사용할 지에 대해 제한할 수 있는 방법을 제공하고 있습니다. 

 이 기능들이 지원되기 위해서는 커널이 필요로 합니다. 커널에서 지원하는 여부는 `docker run` 명령어를 사용하여 확인할 수 있습니다.

 ```bash
 sudo docker info
 [sudo] password for linuxias: 
 Client:
 Debug Mode: false

 Server:
 Containers: 1
 Running: 1
 Paused: 0
 Stopped: 0
 Images: 5
 Server Version: 19.03.5
 Storage Driver: overlay2
 Backing Filesystem: extfs
 Supports d_type: true
 Native Overlay Diff: true
 Logging Driver: json-file
 Cgroup Driver: cgroupfs
 Plugins:
 Volume: local
 Network: bridge host ipvlan macvlan null overlay
 Log: awslogs fluentd gcplogs gelf journald json-file local logentries splunk syslog
 Swarm: inactive
 Runtimes: runc
 Default Runtime: runc
 Init Binary: docker-init
 containerd version: b34a5c8af56e510852c35414db4c1f4fa6172339
 runc version: 3e425f80a8c931f88e6d94a8c831b9d5aa481657
 init version: fec3683
 Security Options:
 apparmor
 seccomp
 Profile: default
 Kernel Version: 4.15.0-76-generic
 Operating System: Ubuntu 16.04.6 LTS
 OSType: linux
 Architecture: x86_64
 CPUs: 8
 Total Memory: 31.36GiB
 Name: desktop
 ID: EKUS:7MJY:7SHK:ORWK:BHEB:3LDT:DLYP:BC66:T6B5:Z7PN:H22E:LNBL
 Docker Root Dir: /var/lib/docker
 Debug Mode: false
 Registry: https://index.docker.io/v1/
 Labels:
 Experimental: false
 Insecure Registries:
 127.0.0.0/8
 Live Restore Enabled: false

 WARNING: No swap limit support

 ```

 `docker run` 명령어로 확인 시 가장 아래 **WARNING: No swap limit support** 라는 경고가 발생한다면 아래와 같이 조치하시면 됩니다. 아래 내용은 Docker docs에서 참조한 내용입니다.
 ```vim
 WARNING: Your kernel does not support swap limit capabilities. Limitation discarded.
 This warning does not occur on RPM-based systems, which enable these capabilities by default.

 If you don’t need these capabilities, you can ignore the warning. You can enable these capabilities on Ubuntu or Debian by following these instructions. Memory and swap accounting incur an overhead of about 1% of the total available memory and a 10% overall performance degradation, even if Docker is not running.

 Log into the Ubuntu or Debian host as a user with sudo privileges.

 Edit the /etc/default/grub file. Add or edit the GRUB_CMDLINE_LINUX line to add the following two key-value pairs:

 GRUB_CMDLINE_LINUX="cgroup_enable=memory swapaccount=1"
 Save and close the file.

 Update GRUB.

 $ sudo update-grub
 If your GRUB configuration file has incorrect syntax, an error occurs. In this case, repeat steps 2 and 3.

 The changes take effect when the system is rebooted.
 ```

### Memory

#### Memory 부족의 위험성..?
실행 중인 컨테이너가 호스트 시스템의 메모리를 과다하게 사용하는 것은 매우 위험합니다. 컨테이너를 동작 시에 호스트 시스템의 메모리를 너무 많이 사용하지 않도록 주의해 주세요. 리눅스 호스트에서 커널이 시스템을 원활하게 동작시키기 위한 과정 중에 메모리가 부족하다는 것을 감지하면 OOME 또는 OOM 이 발생하고 프로세스를 강제 종료하게 됩니다. 즉 도커 컨테이너를 위함 여러 중요 프로세스들이 종료될 수 있습니다. 원치 않는 종료는 시스템에 큰 영향을 주고 시스템이 종료될 수 도 있으니 주의해주세요!

과다한 메모리 사용으로 인해 도커 데몬이 커널에 의해 종료되는 위험을 최소화 하기 위한 방안이 있습니다. OOM 우선순위를 변경하여 커널에 의해 종료되는 여러 프로세스 들 중 우선순위를 낮추는 것 입니다. 위 방법은 cgroup memory 자원에 대한 제어 방법에서도 말씀 드린 적이 있습니다. cgroup으로 memory 자원 추상화된 프로세스에 대해 OOM을 disable 함으로서 종료됨을 막고 메모리가 필요하다면 사용할 수 있는 메모리가 생길 때 까지 대기하고 있는 것입니다. 도커 데몬도 위와 같은 방법을 제한합니다. 도커 데몬이 아닌 컨테이너는 OOM 우선 순위가 조정되지 않습니다. 따라서 Docker 데몬이나 다른 시스템 프로세스가 종료되는 것보다 개별 컨테이너가 종료 될 가능성이 높아집니다. 데몬이나 컨테이너에서 수동으로 `--oom-score-adj`를 최소값으로 설정하거나 컨테이너에서 `--oom-kill-disable`을 설정하여 이러한 안전 장치를 피하려고 시도해서는 안됩니다. 이 점 꼭 유의해주세요.

nginx 이미지를 이용하여 메모리가 1GB로 제한되어 있는 컨테이너를 생성해봅시다. 제한된 메모리 설정은 `docker inspect` 명령어를 사용하여 확인하였습니다.

```bash
$sudo docker run -d --memory="1g" nginx
WARNING: Your kernel does not support swap limit capabilities or the cgroup is not mounted. Memory limited without swap.
5019001493a593b4c7082362a1af05ee754d7c142705ecbb9a5e1306b82abb2d

$sudo docker ps
CONTAINER ID        IMAGE               COMMAND                  CREATED             STATUS              PORTS               NAMES
5019001493a5        nginx               "nginx -g 'daemon of…"   23 seconds ago      Up 22 seconds       80/tcp              beautiful_chandrasekhar


$sudo docker inspect beautiful_chandrasekhar | grep Memory
"Memory": 1073741824,
"KernelMemory": 0,
"KernelMemoryTCP": 0,
"MemoryReservation": 0,
"MemorySwap": -1,
"MemorySwappiness": null,
```

만약 nginx 컨테이너 내부에서 할당된 1GB의 메모리 이상의 메모리를 사용하게 되면 컨테이너는 자동으로 종료됩니다. 그렇기에 각 컨테이너에 메모리를 할당할 시 적절량을 잘 생각하셔서 할당해 주셔야 합니다. `--memory` 옵션 외의 메모리와 관련된 옵션을 정리하면 아래와 같습니다.

|option|description|
|---|---|
|`--memory-swap`|디스크로 swap 할 수 있는 메모리 설정|
|`--memory-swappiness`|기본적으로 호스트 커널은 컨테이너가 사용하는 익명 페이지의 비율을 바꿀 수 있습니다. --memory-swappiness를 0에서 100 사이의 값으로 설정하여이 백분율을 조정할 수 있습니다. |
|`--memory-reservation`|호스트 시스템에서 경합 또는 메모리 부족을 감지하면 활성화되는 --memory보다 작은 소프트 제한을 지정할 수 있습니다. --memory-reservation을 사용하는 경우 우선 순위를 지정하려면 --memory보다 낮게 설정해야합니다. 소프트 한계이므로 컨테이너가 한계를 초과하지는 않습니다.|
|`--kernel-memory`|컨테이너가 사용할 수 있는 커널 메모리의 최대 값입니다. 허용되는 최소값은 4m입니다. 커널 메모리는 스왑아웃 할 수 없기 때문에 커널 메모리가 부족한 컨테이너는 호스트 시스템 리소스를 차단될 수 있으며 이로 인해 호스트 시스템과 다른 컨테이너에 부작용이 발생할 수 있습니다.|
|--oom-kill-disable|기본적으로 메모리 부족 (OOM) 오류가 발생하면 커널은 컨테이너의 프로세스를 종료합니다. 이 동작을 변경하려면 --oom-kill-disable 옵션을 사용하십시오. -m /-memory 옵션도 설정 한 컨테이너에서는 OOM 킬러 만 비활성화하십시오. -m 플래그를 설정하지 않으면 호스트에 메모리가 부족할 수 있으며 커널은 메모리를 비우기 위해 호스트 시스템의 프로세스를 종료해야합니다.|



### CPU
기본적으로 호스트 시스템에서 동작하는 컨테이너들은 시스템의 CPU 사용량에 제한이 없습니다. 하지만 CPU 사용량을 제한할 수 있는 방법이 있습니다. 대부분 사용자들은 CFS 스케쥴러를 기본으로 사용하고 있지만, Docker 1.13 버전 이상에서는 real-time 스케쥴러로 변경이 가능합니다. 먼저 CFS 스케쥴러에 대한 설정부터 정리해보겠습니다.

#### CPU - CFS 스케쥴러

CFS 스케쥴러는 리눅스 커널 CPU 스케쥴러입니다. 스케쥴러에 대한 자세한 설명은 생략하겠습니다.

#### 1. --cpus
컨테이너가 사용할 수 있는 CPU 리소스 양을 지정합니다. 만약 cpu가 8개 있는 호스트 시스템에서 `--cpus=0.5`로 지정하면 컨테이너는 최대 4개의 CPU 사용을 보장 받을 수 있습니다. 

#### 2. --cpu-shares

`cpu-shares` 옵션은 컨테이너에 가중치를 설정해 해당 컨테이너가 CPU를 상대적으로 얼마나 사용할 수 있는지를 설정할 수 있습니다. 기본적으로 1024가 기본 값으로 설정되어 있습니다. 컨테이너 간 사용하는 상대 비율로써 하나의 컨테이너가 1024, 다른 하나가 2048로 설정되어 있다면, 1:2 의 비율로 CPU 자원을 공유하게 됩니다.

```bash
$ sudo docker run -it --cpu-shares 1024 ubuntu:18.04
```

#### 3. --cpuset-cpu

여러 CPU를 자유롭게 컨테이너들이 사용할 수 있도록 할수도 있지만, 특정 CPU만 사용할 수도 있게 할 수 있습니다. 아래 이미지는 문영일님의 블로그인 문c블로그에서 참조하였습니다. 이미지를 보시면 쉽게 이해가 되실 겁니다. 
![문 c 블로그 cgroup 설명](http://jake.dothome.co.kr/wp-content/uploads/2015/12/cgroup2.png)

위 방식의 이점은 명확합니다. 코어 스위칭 비용을 없앰으로서 cache miss로 인한 성능저하 등의 문제를 최소화 할 수 있습니다.

```bash
sudo docker run -it --cpuset-cpus=2 --name cpu_test ubuntu:18.04
root@452468a96c63:/# apt update && apt install stress
root@452468a96c63:/# stress --cpu 1
....

다음 호스트 시스템에서 `htop`을 이용하여 확인해봅시다.
```bash
$htop
1  [|                                                                                     0.7%]   5  [                                                                                      0.0%]
2  [                                                                                      0.0%]   6  [                                                                                      0.0%]
3  [||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||||100.0%]   7  [                                                                                      0.0%]
4  [                                                                                      0.0%]   8  [||                                                                                    1.3%]
Mem[||||||||||||||||||||||||||                                                     4.33G/31.4G]   Tasks: 182, 656 thr; 2 running
Swp[|                                                                              2.00M/31.9G]   Load average: 1.04 0.88 0.47 
Uptime: 1 day, 19:25:52
```

CPU 3번(1번부터 0입니다.) 이 100%로 표기되어 있습니다. --cpuset-cpus=2 옵션으로 인해 ubuntu:18.04 이미지로 생성한 컨테이너에서 stress 테스트 진행하였으니, 3번이 100%임을 확인할 수 있습니다.

#### 4. --cpu-period, --cpu-quota

컨테이너의 CFS 주기는 기본적으로 100ms로 설정되어 있습니다. 위 명령어를 사용하여 변경이 가능합니다. 

```bash
$sudo docker run -it --cpu-period=100000 --cpu-quota=25000 --name cpu_test ubuntu:18.04
#apt update && apt install stress
#stress --cpu 1
```	

위와 같이 컨테이너를 생성하고 stress를 설치하여 실행해 봅시다.
--cpu-period 100000은 100ms를 의미합니다. --cpu-quota는 --cpu-period로 설정된 시간 중 CPU 스케쥴링에 얼마나 할당할 지 설정해 줍니다. 컨테이너가 스로틀링되기 전에 제한되는 마이크로 초 수입니다. 이 기능은 효과적인 한도 역할을합니다. Docker 1.13 이상을 사용하는 경우 앞서 설명한 --cpus를 대신 사용하는 것을 추천드립니다.

Docker docs에는 아래와 같이 추천하고 있습니다.

##### Docker 1.13 and higher:

```bash
docker run -it --cpus=".5" ubuntu /bin/bash
```

##### Docker 1.12 and lower:
```bash
$ docker run -it --cpu-period=100000 --cpu-quota=50000 ubuntu /bin/bash
```
