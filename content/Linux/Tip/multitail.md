+++
title = "[multitail] 여러 로그 동시에 확인하기"
+++

로그를 확인할 때 tail 명령어를 많이 사용합니다. tail 명령어를 이용 시 여러 개의 로그를 동시에 보고싶지만 하나의 터미널 창에서는 하나의 로그만 확인 가능합니다. 이러한 점을 보완한 툴이 multitail입니다.

아래 명령어로 먼저 설치해 줍니다.

```bash
$ sudo apt install multitail
```

사용방법은 간단합니다.

```bash
$ multitail {log file} {log file}
```

예를 들어,

```bash
$ multitail /var/log/apache2/access.log /var/log/apach2/error.log
```

위 명령어를 수행 시 아래와 같은 결과를 얻을 수 있습니다.

![multitail](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile4.uf.tistory.com%2Fimage%2F99D518375B5671481CF837)

감사합니다.
