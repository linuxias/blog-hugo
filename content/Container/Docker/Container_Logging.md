+++
title = "Container Logging"
draft = true
+++

`docker logs` 명령어는 이용하여 동작중인 컨테이너의 로그 정보를 볼 수 있습니다. `docker logs` 명령어는 터미널에서 대화식으로 명령을 실행했을 때와 같이 명령의 출력을 표시합니다. UNIX 및 Linux 명령은 일반적으로 STDIN, STDOUT 및 STDERR이라는 3 개의 I/O 스트림이 실행될 때 열립니다. STDIN은 명령의 입력 스트림으로 키보드 입력 또는 다른 명령 입력을 포함 할 수 있습니다. STDOUT은 일반적으로 명령의 일반 출력이며 STDERR은 일반적으로 오류 메시지를 출력하는 데 사용됩니다. 기본적으로 `docker logs`에는 명령의 STDOUT 및 STDERR이 표시됩니다.

하지만 특별한 경우에는 로그를 볼 수 없습니다. 그 경우는 아래와 같습니다.

 - logging driver 가 로그를 파일, 호스트 또느 DB 등에 저장하게 되면 `docker logs` 명령어로는 로그를 확인할 수 없습니다. 그 이유는 위에서 설명한 듯이 STDOUT, STDERR를 표시하기 때문입니다.
 -  Non-interactive 프로세스들 즉 STDIO 와 상호 인터렉션이 없는 프로세스들은 STDIO 대신 다른 방식으로 로그를 저장합니다.

위와 같은 경우 `docker logs` 명령어로 로그를 볼 수 있도록 별도의 작업이 필요합니다. 예시는 [View logs for a container or service](https://docs.docker.com/config/containers/logging/) 에서 제공하고 있는 nginx와 httpd입니다. Dockerfile 에서 저장되는 로그 파일을 STDOUT, STDERR로 리다이렉션 하거나, 설정을 변경함으로서 로그를 제공합니다.

nginx 이미지는 /var/log/nginx/access.log에서 /dev/stdout으로의 심볼릭 링크를 만들고 /var/log/nginx/error.log에서 /dev/stderr 로의 다른 심볼릭 링크를 만들어 로그를 덮어 씁니다. [nginx Dockerfile](https://github.com/nginxinc/docker-nginx/blob/8921999083def7ba43a06fabd5f80e4406651353/mainline/jessie/Dockerfile#L21-L23)  

httpd 이미지는 httpd 응용 프로그램의 구성을 변경하여 STDOUT을 /proc/self/fd/1 (STDOUT)에 직접 기록하고 오류를 /proc/self/fd/2 (STDERR)에 기록합니다. [httpd Dockerfile](https://github.com/docker-library/httpd/blob/b13054c7de5c74bbaa6d595dbe38969e6d4f860c/2.2/Dockerfile#L72-L75)



### logging driver 설정하기

도커는 다양한 로깅 메커니즘을 포함하고 있습니다. 각 메커니즘들은 우리는 **logging driver** 라고 부릅니다. 기본적으로 local file, json-file, syslog, fluentd와 구글 클라우드, 아마존 클라우드에서 제공하는 logging driver까지 여러 드라이버가 존재합니다. 해당 드라이버 중 몇가지에 대한 사용법은 아래에서 알아보겠습니다. 여기서는 logging driver에 대한 설정에 대해 정리하겠습니다.

#### Default logging driver 설정하기

Docker daemon에서 사용하는 기본 logging driver를 설정하기 위해서 `/etc/docker/` 경로에 존재하는 `daemon.json` 파일을 이용합니다. 아무런 설정을 하지 않았다면 도커에서 제공하는 logging driver는 json-file입니다. 이 설정을 `syslog` 로 변경하는 방법은 `daemon.json` 파일에 아래와 같이 추가하는 것입니다.

```json
{
	"log-driver": "syslog"
}
```

`logging-driver`에 추가적인 옵션을 주고자 한다면 아래와 같이 `log-opts`를 이용하여 설정해줍니다. 설정 내용은 읽어보시면 쉽게 파악하실 수 있습니다. 여기서 유의할 점은 모든 value는 문자열 기반입니다.
```json
{
  "log-driver": "json-file",
  "log-opts": {
    "max-size": "10m",
    "max-file": "3",
  }
}
```

#### logging driver 간략히 정리하기
[Configure logging drivers](https://docs.docker.com/config/containers/logging/configure/) 에 있는 표를 이용하여 컨테이너를 위한 `logging driver`들을 몇 가지만 정리하였습니다. 그 외 추가적인 내용은 링크에서 확인하시면 됩니다.

|Driver|Description|
|-|-|
|`none`|비활성화|
|`local`|최소한의 오버헤드를 위한 사용자가 정의한 포맷으로 로그가 저장됩니다|
|`json-file`|JSON 형태로 로그가 저장됩니다. 도커의 default logging driver 입니다.|
|`syslog`|`syslog`를 이용하여 로그를 작성합니다. `syslog` 데몬이 호스트에서 동작하고 있어야 합니다.|
|`journald`|`journald`를 이용하여 로그를 작성합니다. `journald` 데몬이 호스트에서 동작하고 있어야 합니다.|
|`fluentd`|`fluentd`에 로그를 작성합니다. `fluentd` 데몬이 호스트에서 동작하고 있어야 합니다.|

만약 `Docker Community Engine`을 사용하는 경우에는 `docker logs` 명령어를 사용하여 오직 `local`, `json-file`, `journald`에서만 사용할 수 있습니다.


#### 참고자료
[View logs for a container or service](https://docs.docker.com/config/containers/logging/)
[Configure logging drivers](https://docs.docker.com/config/containers/logging/configure/)
