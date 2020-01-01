+++
title = "2. PID Namespace"
weight = 5
+++

PID namespace는 프로세스 ID 공간을 격리 시킵니다. 이 말인 즉, 다른 PID namespace의 프로세스들은 같은 PID를 가질 수도 있음을 의미합니다. PID namespace들은 프로세스 집합의 종료, 재시작과 같은 기능을 제공하기 위한 컨테이너를 허용합니다. 또한 컨테이너를 새로운 호스트로 마이그레이션하는 등의 기능을 컨테이너가 제공할 수 있도록 해줍니다.

PID namepsace의 특이한 점은 새로운 PID namespace의 PID는 1 부터 시작한다는 것입니다. standalone 시스템과 동일하게 각 namespace의 시작 프로세스는 pid를 1번을 가지게됩니다. PID namespace를 사용하기 위해선 **CONFIG_PID_NS** 커널 옵션을 설정해야 합니다.


### The namespace init process

CLONE_NEWPID flag를 파라미터로 한 unshare(2) 시스템 콜을 호출한 이후, 또는 clone(2) 시스템 콜의 flag로 CLONE_NEWPID를 전달하여 생성한 프로세스는 새로운 Namespace의 첫 번째 프로세스가 됩니다. 이 말인 즉, 이 프로세스의 PID가 1번이라는 것입니다.

조금 혼란스러울 수 있습니다. 리눅스에서 PID는 고유하며, 프로세스의 식별자로 사용이 되는데, 새로운 Namespace의 첫 번째 프로세스의 PID가 1번이라면, 중복될테니까요. 그 이후 이 프로세스에 자식 프로세스들도 2,3,4... 와 같은 PID를 가질 수 있다는 말이됩니다. 식별자로써의 가치가 사라지게 되는 것일까요? 조금씩 정리해보도록 하겠습니다.

새로운 PID namespace의 첫 번째 프로세스의 PID가 1번이라고 말씀드렸습니다. 그 의미는 해당 namespace를 위한 init process가 된다는 의미입니다. 아래 그림처럼 새로운 namespace는 PID 1번부터 시작하게 됩니다. 뭔가 속임수 같나요?

![Parent PID namespace](https://uploads.toptal.io/blog/image/674/toptal-blog-image-1416487554032.png)

그림 출처 : https://www.toptal.com/linux/separation-anxiety-isolating-your-system-with-linux-namespaces

만약 8,1 두 개의 PID를 가진 프로세스에서 **getpid(2)** 시스템 콜을 호출하게 되면, 어떤 결과가 리턴 될까요? 결과는 1입니다. PID를 이용해 동작하는 시스템 콜들은 항상 호출자의 PID namespace 내에 표시되는 PID를 사용하게 됩니다. 그렇기 때문에 child PID namespace에서 표시되는 1이 반환됩니다.

namespace 동작 중에 init process가 종료되면 어떻게 될까요? 만약 PID namespace 내의 init process가 종료된다면, 커널은 **SIGKILL** 시그널을 통해 해당 namespace 내에 모든 프로세스를 종료시키게 됩니다. 이 의미는 PID namespace가 정상적으로 동작하기 위해선 PID 1의 init process가 필수적이란 의미입니다.

init process에 시그널을 보낼 수 있는 경우는 시그널 핸들러에 등록한 시그널들만 PID namespace의 다른 프로세스들에 의해 전달될 수 있습니다. 이러한 제한은 권한이 있는 프로세스들에게도 해당되며 실수로 init process가 PID namespace 내의 다른 멤버 프로세스에 의해 종료되는 것을 막아주게됩니다. 마찬가지로 상위 PID namespace의 프로세스는 자식 PID namespace의 init process가 등록한 시그널 **kill(2)**을 호출하여 전달할 수 있습니다. 여기서 **SIGKILL**과 **SIGSTOP**은 예외적으로 처리되는데요, 상위 PID namespace에서 시그널을 전달하면 init process에서는 처리할 수 없기에, 해당 시그널이 처리되어 프로세스 종료 및 중지가 발생하게 됩니다.


### Nesting PID namespace
PID Namespace는 중첩해서 사용이 가능합니다. 그 말은, 각 PID namespace는 상위(부모) namespace를 가지고 있습니다.(root PID namespace는 제외입니다 :D) PID namepsace의 부모 namespace는 **clone(2)** 또는 **unshare(2)**를 사용하여 namespace를 생성한 프로세스의 PID namepsace가 됩니다. 이러한 구조는 PID namespace가 트리 자료구조 형태로 이루어져 있습니다. 모든 namepsace는 자신의 상위 namespace들(root namespace 포함)을 언제든 찾을 수 있습니다.

특정 Namespace에 속한 프로세스는 해당 namespace에 속한 프로세스들과, 상위 모든(root namespace로 가는 경로의) namespace 프로세스들에게 보여집니다. 보여진다는 의미는 해당 프로세스를 타겟으로 작업을 진행할 수 있다는 의미입니다. 하지만 반대로 자식 PID namespace에서는 부모나 제거된 상위 namespace의 프로세스들을 볼 수 없습니다. 정리하면, 프로세스는 오직 자신의 PID namsepace의 프로세스들이나 자식 namespace들의 프로세스들만 볼 수 있습니다.

특정 PID namespace내의 프로세스들은 가끔 namespace 외부에 부모 프로세스를 가지는 경우가 있습니다. 첫 번째는 위에서 살펴보았듯이, Namespace가 생성된 후 첫 프로세스는 자신을 생성한 프로세스가 부모프로세스가 됩니다. 이 경우엔 부모와 자식 프로세스가 각각 다른 PID namespace에 존재하게 됩니다. 두 번째로 **setns(2)** 시스템콜을 이용하여 특정 PID namespace로 조인하게 되는 경우입니다. 조인할 수 있는 PID namespace는 자식 PID namespace으로만 가능합니다. 완전 다른 방향의 namespace로는 불가능합니다. 잘 생각하셔야 할게 지금 설명드리는 부분은 namespace간 부모, 자식 관계가 아닌 프로세스의 부모, 자식 관계입니다.


### /proc 파일시스템과 PID namespace

/proc 파일시스템은 /proc 파일시스템이 다른 Namespace에서 보여지더라도 마운트를 수행한 프로세스의 PID namespace에 보이는 프로세스만 보여줍니다. 새 PID namespace를 만든 후에는 ps (1)와 같은 툴이 정상적으로 작동하도록 /proc 파일시스템에 새로운 procfs 인스턴스를 마운트하고 루트 디렉토리를 변경하는 것이 좋습니다. clone (2) 또는 unshare(2)의 flags에 CLONE_NEWNS를 포함하여 새로운 마운트 네임 스페이스를 동시에 생성하면 루트 디렉토리를 변경할 필요가 없습니다. 새로운 procfs 인스턴스를 /proc에 직접 마운트 할 수 있습니다.

```shell
mount -t proc proc /proc
```

PID namespace는 container에서 유용하게 사용되는 기술 중 하나입니다. 추가적인 내용들은 정리되는대로 갱신하겠습니다. 글 읽어주셔서 감사합니다.

감사합니다.

참조 :
https://www.toptal.com/linux/separation-anxiety-isolating-your-system-with-linux-namespaces
