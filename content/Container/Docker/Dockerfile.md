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

#### FROM

FROM 명령어는 Dockerfile 내부에 가장 먼저 등장해야 하는 명령어이며 최소 하나 이상 존재해야 하는 명령어 입니다. 생성할 이미지의 기반이 될 도커 이미지를 명시해주시면 됩니다.
