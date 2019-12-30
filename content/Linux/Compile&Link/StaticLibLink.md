---
title: "정적 라이브러리 링크"
date: 2019-12-30T23:21:11+09:00
---

라이브러리 중 정적 라이브러리(static library)를 사용하는 경우가 종종 있다. 그리고 해당 라이브러리가 또 다른 (static library)를 Link 하는 경우가 있다. 이럴 때 문제가 발생하게 된다. 아래와 같이 어플리케이션이 필요로 하는 정적 라이브러리를 링크하게 되는데, 해당 라이브러리가 다른 정적 라이브러리를 링크하게 되는 경우이다.

**Application --> Static library 1 --> Static library 2**

Application은 Static library 1의 존재만 알 뿐 2의 존재는 알지도, 알 필요도 없다.

## pkg-config를 이용한 경우 pc 파일에 정의

아래 libpalosalodp 의 pc.in 파일이다.

```sh
# Package Information for pkg-config
prefix=@prefix@
exec_prefix=${prefix}
libdir=${exec_prefix}/lib
includedir=${prefix}/include/
Name: libtest
Description: test library
Version: 0.0.1
Requires.private: libtest_internal >= 0.0.1
Libs: -L${libdir} -ltest
Cflags: -I${includedir} \
        -I${includedir}/Include
```

Requires.private 에서 libodp\_internal\_${sdktype} 에 대한 필요 정보를 명시하고, 다른 Application의 Makefile에서 pkg-config --static 옵션을 붙여서 추가 시 해당 lib을 추가해준다.

Requires.private 은 이 패키지에 필요한 패키지 목록을 나타내는 것으로 동적 링크된 실행 파일(static이 지정되지 않은 경우)에 대해 플래그 목록을 계산할 때 고려되지 않는다.

## Requires 사용하기

```sh

# Package Information for pkg-config
prefix=@prefix@
exec_prefix=${prefix}
libdir=${exec_prefix}/lib
includedir=${prefix}/include/
Name: libtest
Description: test library
Version: 0.0.1
Requires: libtest_internal >= 0.0.1
Libs: -L${libdir} -ltest
Cflags: -I${includedir} \
        -I${includedir}/Include
```

Requires 옵션을 이용하여 포함시킨다.

Requires와 Requires.private의 차이는 아래와 같다.

-   Requires: 패키지가 의존하여 사용하는 다른 패키지의 이름의 목록으로 공백으로 구분한다. 비교 연산자(=, <, >, <=, >=)를 사용하여 버전을 지정할 수도 있다.
    
-   Requires.private: 패키지가 의존하여 사용하는 다른 패키지의 이름의 목록으로 공백으로 구분한다. 단 Requires와 다르게 패키지 안에서만 사용하며 이 패키지를 가져다가 사용하는 애플리케이션에는 사용할 필요 없을 패키지를 나열한다. 버전을 지정하는 형식은 Requires와 동일하다.
    

하지만 정적 라이브러리를 링크하고 있는 상황이기 때문에, Requires를 사용하던지 Application은 pkg-config 에서 --static 을 사용해야만 Requires.private에 정의된 패키지를 사용할 수 있다.
