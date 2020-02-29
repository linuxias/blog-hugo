+++
title = "Docker Compose"
+++

## Overview 

**Docker Compose** 는 멀티 컨테이너 도커 어플리케이션을 정의하거나 동작시키기 위한 툴이다. 이 툴로 인해 개발용이성이 매우 향상되었다. `docker` 명령어를 사용하나 단일 도커 이미지를 생성하거나 단일 컨테이너를 동작시켰다. 둘 이상의 상호 연관된 컨테이너가 필요한 경우 `docker run` 명령어를 여러번 실행하여 환경을 구성하였다. 한번 구성한 환경을 지속적으로 사용한다면 모르겠지만, 설정이 변경되거나 다른 호스트 머신에서 구축이 필요한 경우 또 다시 `docker run` 명령(`docker network` 등 포함)을 다시 실행해야 한다. 얼마나 불편한가?

```
개발자는 게을러야 한다.
```

위 글귀는 갑작스럽겠지만, 위 문구가 `docker compose`와 잘 어울리는 글귀라고 생각해서 뜬금없지만 적어보았다. 개발자는 게을러야 하기에 반복 작업을 부지런히 수행하면 된다. 효율적으로 게을러 질 수 있다면 게을러져야 한다. 

## Install

설치환경은 ** ubuntu 16.04 LTS ** 이며 설치하는 Compose의 버전은 **1.25.4**이다.

```bash
$sudo curl -L "https://github.com/docker/compose/releases/download/1.25.4/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose
$sudo chmod +x /usr/local/bin/docker-compose
$sudo ln -s /usr/local/bin/docker-compose /usr/bin/docker-compose
$docker-compose --version
docker-compose version 1.25.4, build 1110ad01
```

## Start

예제는 [Docker docs getting started](https://docs.docker.com/compose/gettingstarted/) 에 있는 내용을 사용한다.

### 1. setup

#### 디렉터리 생성하기

```bash
$ mkdir composetest
$ cd composetest
```

#### app.py 작성하기

```python
import time

import redis
from flask import Flask

app = Flask(__name__)
cache = redis.Redis(host='redis', port=6379)


def get_hit_count():
    retries = 5
    while True:
        try:
            return cache.incr('hits')
        except redis.exceptions.ConnectionError as exc:
            if retries == 0:
                raise exc
            retries -= 1
            time.sleep(0.5)


@app.route('/')
def hello():
    count = get_hit_count()
    return 'Hello World! I have been seen {} times.\n'.format(count
```

#### requirements.txt 파일 작성하기

requirements.txt 파일을 생성하여 아래 내용을 입력한다.
```bash
$cat requirements.txt
flask
redis
```

### 2. Dockerfile 작성하기

`python:3.7-alpine` 이미지를 기반으로 `flask`와 `redis`를 설치하고 실행시키는 Dockerfile을 작성한다. 자세한 내용은 Docker에 대해 설명한 글들을 참조하고 이 글에서는 자세한 내용은 생략한다.

```dockerfile
FROM python:3.7-alpine
WORKDIR /code
ENV FLASK_APP app.py
ENV FLASK_RUN_HOST 0.0.0.0
RUN apk add --no-cache gcc musl-dev linux-headers
COPY requirements.txt requirements.txt
RUN pip install -r requirements.txt
COPY . .
CMD ["flask", "run"]
```

### 3. Compose 파일 내에 이미지 정의하기.

compose는 `yml` 파일을 읽어 도커 엔진에게 컨테이너 생성을 요청한다. 아래 `docker-compose.yml` 파일을 참조하자. 

```yaml
version: '3'
services:
  web:
    build: .
    ports:
      - "5000:5000"
  redis:
    image: "redis:alpine"
```

두 개의 컨테이너를 생성한다. 각 컨테이너는 위에서 작성한 Dockerfile로 생성한 이미지와 redis:alpine 이미지를 기반으로 컨테이너를 실행한다.

yml 파일의 내용을 간략히 설명하면 아래와 같다.

* version : YAML 파일 포맷의 버전이다. 1, 2, 2.1, 3.0 등 다양한 버전이 있으며 여기서는 3.0 버전을 사용한다. 도커 컴포즈의 버전은 도커 엔진에 의존성이 존재하므로 본인이 사용하는 도커 엔진의 버전에 맞춰 알맞은 컴포즈 엔진을 사용하는 것을 추천한다.
* services : 생성될 컨테이너들의 묶음 단위이다. 위 파일에서는 web, redis 의 컨테이너가 하나의 묶음이다.
* web, redis : 생성될 서비스 이름이다. 각 서비스 아래 `docker run` 시 설정하던 각각의 옵션을 명시하여 사용한다.

이 외에도 다양한 옵션들이 존재한다. 다양한 옵션은 나중에 설명한다.

### 4. Compose 이용하여 도커 컨테이너 생성 및 동작시키기

`docker-compose.yml` 파일이 위치한 경로에서 아래 명령어를 실행한다.

```bash
$sudo docker-compose up
```

그 후 ** http://localhost:5000/ ** 로 접속하여 'hello world' 메시지가 뜨는지 확인한다.

`docker-compose up` 명령어로 생성된 컨테이너의 이름은 `[Project]_[Service]_[The Number]`이다.
현재 composetest 디렉터리에서 수행하므로 프로젝트 이름은 composetest가 된다. 즉 위의 docker-compose.yml 파일을 이용해 생성된 컨테이너의 이름은 아래와 같다.

- composetest_web_1
- composetest_redis_1


### 5. docker-compose.yml 을 수정하기.

volume을 추가하고, 환경변수를 추가하여 새로운 컨테이너를 생성한다.

```yaml
version: '3'
services:
  web:
    build: .
    ports:
      - "5000:5000"
    volumes:
      - .:/code
    environment:
      FLASK_ENV: development
  redis:
    image: "redis:alpine"
```

위 처럼 `docker-compose.yml` 파일을 수정하고 다시 아래 명령어로 생성한다.
```bash
$sudo docker-compose up
```

5000번 포트로 재접속 하면 갱신된 화면을 볼 수 있다.


## 무엇이 편해졌는가?

`docker-compose`를 사용하여 무엇이 편해진건지 이해하지 못하는 분들이 있다. 저 정도는 `docker run` 명령어의 옵션으로 금방 작성할 수 있다고 생각할 수 있지만 예제와 달리 매우 많은 컨테이너를 동시에 정의하고 동작시킨다고 생각하면, 저 파일 하나로 모두 다 제어할 수 있는게 좋을까? 아니면 필요한 시점마다 모든 컨테이너를 `docker run`으로 수행하는게 나을지는 본인의 판단에 맡긴다. 아니면 일단 위의 컨테이너를 `docker run` 명령어로 수행해 보자. 각각 10번씩 수행 시 얼마나 귀찮을지.. 그리고 많은 서버에 배포할 때 서버마다 명령어를 하나씩 작성하고 있을것인가? 부지런하다면.. 그 방법도 좋겠지만, 나는 게으른게 좋다.

### Reference 
[Docker docs](https://docs.docker.com/compose/)
도커/쿠버네티스, 용찬호 지음 | 위키북스
