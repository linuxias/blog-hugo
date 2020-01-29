+++
title = "Hierarchical Software Architecture"
+++

Hierarchical Software Architecture, 한국어로 계층적 소프트웨어 아키텍처라 불리는 아키텍처에 대해 정리하겠습니다.

`Hierarchical Architecture`는 전체 시스템을 계층 구조적으로 나뉘어져 있으며 계층적으로 서로 다른 레벨의 서브시스템으로 구성되어 있습니다. `Hierarchical Architecture`는 매우 다양한 곳에서 사용되고 있습니다. 운영체제, 네트워크 프로토콜 계층들, 인터프리터, 그 외 다양한 곳에서 사용되고 있는데요, 이 아키텍처의 가장 대표적인 구조로서 여러분들이 가장 많이 접해본 아키텍처의 한 예가 안드로이드 일 것 같습니다. 

![Hierarchical Software Architecture](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Ft1.daumcdn.net%2Fcfile%2Ftistory%2F9985F7365C9A34E51B)

위 안드로이드 아키텍처를 보시면 Applications, Application Framework, Libraries, Linux Kernel 까지 여러 개의 서브시스템이 계층적으로 구성되어 하나의 시스템을 이루고 있습니다. 각 서브시스템은 상위 시스템이 하위 시스템을 호출하는 구조, 즉 Call-and-Return 연결 구조를 가집니다. 서로 다른 계층 레벨들은 Method Invocation에 의해 연결되어 있으며 하위 레벨의 서브시스템이 상위 레벨 서브시스템에게 필요한 서비스를 제공하는 방식으로 구성됩니다. 

이런 Hierarchical Software Architecture 스타일을 가지는 여러 아키텍처들이 존재합니다. 각 아키텍처에 대해서는 다른 글로 다룰 예정이니 참고 바랍니다.

 - Master - Slave Architecture

 - Layered Architecture

 - Virtual Machine Architecture

 - Plug-in Architecture

 - Micro-kernel Architecture

Hierarchical Software Architecture를 적용하기 위해서 몇 가지 주의해야 할 부분들이 있습니다. 먼저, 계층을 나누는 기준이 명확해야 합니다. 각 계층은 하위 계층만을 의존해야 하며, 각 계층을 명확하고 특정적인 태스크를 처리하도록 분리해야 합니다. 다음으로 계층을 몇 개로 나눌지도 고민해야 합니다. 무조건 많거나 무조건 적다고 좋은 것이 아닌 본인이 설계하는 시스템에 가장 접합한 계층의 수를 정의해야합니다. 세 번째로 각 계층에 대한 인터페이스를 정의해야 합니다. 인터페이스를 잘 정의해야 계층의 수정사항이 발생하여도 다른 계층에 영향을 주지 않도록 정의되어야 합니다. 마지막으로 각 계층에서 발생한 에러를 어떻게 처리할 지 고민해야 합니다. 해당 에러를 그 계층에서 처리할 지 아니면, 상위 계층에게 전달할지에 대한 부분도 잘 정의해야합니다.

Hierarchical Software Architecture에 대해 간단히 정리해 보았습니다. 감사합니다.
