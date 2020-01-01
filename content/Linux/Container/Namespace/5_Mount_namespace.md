+++
title = "5. Mount namespace"
weight = 5
+++

mount namespace는 프로세스와 그 자식 프로세스에게 다른 파일시스템 마운트 포인트를 제공합니다. 각 namespace instance의 프로세스들에게 보여지는 마운트 포인트를 격리시키는 기능을 제공합니다. 이렇게 격리된 마운트 포인트는 각 프로세스에게 단일 디렉토리 구조로 보여지게됩니다.

처음 설치된 시스템에서는 기본적으로 모든 프로세스가 하나의 mount namespaece(기본 namespace)에 속하기 때문에 파일시스템를 마운트하거나 해제하는 등의 모든 사항을 확인 및 인지할 수 있습니다.

**clone(2)** 또는 **unshare()** 시스템 콜과 **CLONE_NEWNS** 플래그를 사용하여 새로운 프로세스를 생성하면 생성하는 프로세스의 mount namespace를 그대로 복사하여 생성하게 됩니다.

## mount의 종류
mount 할 때 private, shared, slave 옵션을 이용하여 mount를 할 수 있습니다. 각 방식의 차이는 아래와 같습니다.

|mount|설명|
|---|---|
|private mount| 각 마운트 포인트가 다른 마운트 포인트에 반영되지 않는 방법|
|shared mount| 각 마운트 포인트가 다른 마운트 포인트에 반영되어 보여지는 방법(양방향)|
|slave mount| A 파일시스템 하위에서 새로운 마운트는 B 파일시스템에 반영되나, 반대는 반영되지 않는 방법(단방향)|

manual page를 검색하면 아래와 같이 나뉘어져 있습니다.


                                    source(A)
                            shared  private    slave         unbind
       ──────────────────────────────────────────────────────────────────
       dest(B)  shared    | shared  shared     slave+shared  invalid
                nonshared | shared  private    slave         unbindable

## Restriction on mount namespace

- mount namespace는 owner user namespace를 가지고 있습니다. 부모 mount namespace의 owner user namespace와 다른 mount namespace는 그 권한이 낮다고 판단할 수 있습니다.

- 권한이 낮은 mount namespace를 생성할 때, shared mount namespace는 slave mounts로 권한이 낮아집니다. 


## 실습


1. 임시 디렉토리를 생성한다. 
```bash
linuxias$ mkdir /tmp/mount_ns
```

2. **unshare(1)** 를 이용하여 새로운 bash 프로세스를 생성할 때 새로운 마운트 네임스페이스를 생성한다.
```bash
linuxias$ sudo unshare -m /bin/bash
```

3. bash 프로세스가 별도의 네임스페이스에 속함을 확인한다. readlink 명령어를 이용해 네임스페이스의 inode 번호를 확인한다.
```bash
root# readlink /proc/$$/ns/mount
mnt:[4026532199]
```

4. 임시 마운트 지점을 생성하여 1번에서 생성한 디렉토리에 마운트 시킨다.
```bash
root# mount -n -t tmpfs tmpfs /tmp/mount_ns
```

5. 새로 생성한 마운트 지점을 확인한다.
```bash
root# mount | grep mount_ns
or
root# cat /proc/mounts | grep mount_ns
```

6. 새로운 터미널을 열고, 네임스페이스 inode를 확인한다.
```bash
linuxias$ readlink /proc/$$/ns/mount
mnt:[4026531840]
```

7. 새로운 터미널에서 마운트 지점을 확인한다.
```bash
linuxias$ mount | grep mount_ns
or
linuxias$ cat /proc/mounts | grep mount_ns
```

위 실습을 통해 알 수 있는 것은 새로 생성한 mount namespace 내에서 새로운 마운트 지점을 생성해도 기본 namespace에서는 알 수 없다는 것이다. 즉 독립화 되어 동작하게 됩니다.






## 참고자료 및 문헌
- Konstantin Ivanov | Containerization with LXC
- linux manual page - mount_namespace
