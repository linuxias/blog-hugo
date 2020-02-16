+++
title = "Dockerfile"
+++

Docker는 `Dockerfile`에 정의된 인스트럭션을 이용하여 자동적으로 이미지를 빌드 할 수 있습니다. `Dockerfile`은 일반적은 텍스트 파일로서 사용자가 이미지를 생성할 때 필요로 하는 모든 명령어들을 포함시킬 수 있습니다. `docker build` 명령어를 이용하여 사용자는 도커 이미지를 생성할 수 있습니다. 

ubuntu나 centOS 이미지를 DockerHub로 부터 다운받아서 본인의 어플리케이션을 동작시키려 한다면 생각은 쉽지만 쉽게 되지 않습니다. 어플리케이션이 동작하기 위한 여러 환경이 설정되어 있지 않기 때문입니다. 동일한 어플리케이션을 여러 컨테이너에서 동작 시키고자 한다면 각 컨테이너마다 새로운 환경을 구축해 줘야 할 것입니다. 도커에서는 환경을 설정하거나 명령어를 수행하는 등 다양한 과정을 손쉽게 작성할 수 있도록 `docker build` 명령어를 제공하고 있습니다. 


### Dockerfile 작성하기

먼저 설명드리기 전에 ubuntu 18.04에 net-tools이 설치된 도커 이미지를 생성해보도록 하겠습니다.

```bash
$mkdir docker-test && cd docker-test
$vi Dockerfile

# This contents in Dockerfile
FROM ubuntu:18.04
LABEL maintainer = "Seungha Son <linuxias@gamil.com>"
RUN apt-get update
RUN apt-get install net-tools -y
```

Dockerfile 내용은 단순합니다. 이 Dockerfile을 이용하여 빌드를 해보고 `docker images` 명령어로 생성된 이미지를 확인해봅시다.

```bash
$sudo docker build -t net-tools:0.0 .
...
---> d2b515967717
Successfully built d2b515967717
Successfully tagged net-tools:0.0
$sudo docker images
net-tools              0.0                 4081bd419bb1        4 minutes ago       93.4MB
```
잘 생성된 것을 알 수 있습니다. 그럼 하나하나 옵션을 정리해 보겠습니다.

##### FROM

FROM 명령어는 Dockerfile 내부에 가장 먼저 등장해야 하는 명령어이며 최소 하나 이상 존재해야 하는 명령어 입니다. 생성할 이미지의 기반이 될 도커 이미지를 명시해주시면 됩니다. 처음 시작하실 땐 하나만 작성을 할텐데요, 나중에 둘 이상 사용하는 예시에 대해 정리하겠습니다. 아래 예제는 생성할 이미지의 베이스가 될 이미지를 ubuntu:18.04로 명시합니다.

```
FROM ubuntu:18.04
```

##### LABEL
 
생성하려는 이미지에 메타데이터를 추가하는 것입니다. 키-밸류 형태로 이루어져있으며, 복수 개의 메타데이터를 생성할 수 있습니다.
```
LABEL maintainer "Seungha Son <linuxias@gmail.com>"
```

##### ADD

파일을 이미지에 추가할 때 사용합니다. Dockerfile이 위치한 경로에 파일을 포함합니다. 아래 예제는 Dockerfile이 존재하는 경로의 test.txt 파일을 생성하는 이미지의 /home/ 경로에 추가합니다. 두 번째 예제는 3개의 텍스트 파일을 /home/ 경로에 추가하는 내용입니다. 이 처럼 여러 파일을 한 번에 명시할 수도 있습니다.

```
ADD test.txt /home/
ADD [test.txt test1.txt test2.txt /home/]
```
ADD는 로컬에 있는 파일 뿐만아니라 URL을 명시하면 해당 URL의 파일을 복사해줍니다. 또한 tar 파일도 지원합니다. tar도 일반적인 파일인데, 뭐가 다를까라고 생각하실 수 있지만, ADD로 tar 파일을 추가 시 tar 파일의 압축을 풀어 내부 파일들을 해당 경로에 복사해줍니다. tar 파일 그 자체를 복사하는 것이 아니죠. 

```
ADD https://this/is/test/url.html /home/
ADD test_comp.tar /home/
```


##### COPY

ADD와 유사하게 파일을 이미지에 복사해 줍니다. COPY는 로컬에 존재하는 파일만 복사를 해주는 것이 ADD와 유사합니다. 하지만 COPY는 ADD에서 지원하는 URL, tar 파일에 대한 것은 지원하지 않습니다.

```
COPY test.txt /home/
COPY [test.txt test1.txt test2.txt /home/]
```

##### WORKDIR

`cd` 명령어와 같습니다. 생성한 이미지의 /tmp 디렉터리로 이동하는 예시 입니다.
```
WORKDIR /tmp
```

##### RUN

이미지를 생성하기 위해 컨테이너 내부에서 실행하는 명령어 입니다. 아래 예제는 업데이트 후 net-tools를 설치하는 명령어 예제입니다. **-y** 옵션은 RUN 동작 시 사용자와의 인터페이스가 없기에 설치 시 [y/N] 입력이 불가능 하기 때문입니다.
```
RUN apt-get update
RUN apt-get install net-tools -y
```

##### CMD

CMD 명령어는 생성된 이미지를 이용하여 컨테이너를 동작 시킬 때 컨테이너 시작 시 실행할 명령어 입니다. 단 한번만 사용할 수 있습니다. 명시하지 않는다면 기본적으로 /bin/bash 명령어가 실행됩니다.

##### ENV

Dockefile 내부에서 사용할 환경변수를 설정합니다. `ENV` 는 도커 이미지에 포함되므로 해당 이미지로 생성하는 컨테이너에서 이 환경변수를 사용할 수 있습니다. 사용 방법은 `${variable_name}` 또는 $variable_name 형태로 사용할 수 있습니다.

```bash
# This contents in Dockerfile
FROM ubuntu:18.04
LABEL maintainer = "Seungha Son <linuxias@gamil.com>"
ENV install_tool net-tools
RUN apt-get update
RUN apt-get install $install_tool -y
CMD ["/bin/bash"]
```

`$install_tool` 환경 변수는 컨테이너 에서도 사용이 가능하며 `docker run` 시 `-e` 옵션을 이용하여 환경변수를 덮어쓸 수 있습니다.

##### VOLUME

`VOLUME`은 컨테이너를 생성 했을 시 호스트 머신과 공유할 컨테이너 내부의 디렉터리를 설정합니다. 여러 개의 디렉터리 경로를 설정할 수 도 있습니다.

##### ARG

`docker build` 명령어 실행 시 추가로 입력을 받아 Dockefile 내부에서 사용될 변수의 값을 설정할 수 있습니다.

##### USER

기본적으로 컨테이너 실행 시 `root` 권한으로 실행 됩니다. 만약 다른 사용자 계정으로 실행하고자 할 때 `USER`를 사용합니다. root 가 아닌 `linuxias` 란 계정으로 시작하길 원하신다면 아래와 같이 작성해 줍니다.
```bash
USER linuxias
```
이때 유의할 점은현재 존재하는 사용자여야 합니다. `adduser` 명령어를 이용하여 미리 사용자를 만들어놓던지, `volume`을 이용하여 /etc/passwd 파일을 사용하도록 하고 호스트머신에 존재하는 사용자를 `USER`명령어로 사용합니다.


##### ENTRYPOINT

매우 매우 중요한 !!! 내용!. 앞서 컨테이너가 시작할 때 실행할 명령어를 설정해주는 방법으로 `CMD`를 설명하였습니다. `CMD` 와 유사한 기능을 하는 것이 ENTRYPOINT 입니다.

### 참고자료
- https://docs.docker.com/engine/reference/builder/
- 시작하세요! 도커/쿠버네티스. 용찬호 지음. 위키북스
