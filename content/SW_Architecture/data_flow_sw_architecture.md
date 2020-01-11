+++
title = "Data Flow Software Architecture"
+++

Data Flow Software Architecture에 대해 정리해보고, 해당 아키텍처에 속하는 이키텍처들을 정리해 보려합니다. 주제에서 알 수 있듯이 데이터의 흐름에 대한 소프트웨어 아키텍처입니다. Data Flow Software Architecture는 전체 소프트웨어 시스템을 연속적인 데이터 집합에 대한 일련의 변환으로 봅니다.



소프트웨어 시스템은 데이터가 데이터 연산 처리 순서를 지시하고 제어하는 데이터 처리 요소로 분리 될 수 있습니다. 각 컴포넌트는 입력으로 데이터를 받고, 출력으로 연산된 데이터를 출력합니다. 이렇게 출력된 연산 데이터는 다음 컴포넌트의 입력이 됩니다. 이 부분이 Data Flow Software Architecture 들의 가장 큰 특징입니다. 

데이터를 처리하는 각 서브시스템 컴포넌트들 사이의 연결은 I/O 스트림, I/O 파일, 버퍼나 파이프등 다양한 방법이 있습니다. 이 아키텍처에서 트리 구조에서는 사이클이 없는 선형적인 구조를 가지고 있으며, 그래프 토폴로지와 같은 데이터 흐름에서는 사이클이 발생할 수 있습니다.



Data Flow Software Architecture에 속하는 대표적인 구조 3가지는 아래와 같습니다.

- Batch Sequential Architecture

- Pipe and Filter Architecture

- Process Control Architecture



Data Flow Software Architecture의 장점은 변경용이성과 재사용성입니다. 서브시스템은 서로간 독립적으로 구성되어 있습니다. 각 서브시스템은 서로간의 어떠한 영향없이 새로운 서브시스템으로 교체가 가능하며 중간에 새로운 서브시스템의 추가도 쉽기 때문에 아키텍처의 변경이 용이하고 각 서브시스템은 다른 아키텍처에서 재사용이 쉽습니다. 한 가지 유의점은 서브시스템 추가 시 출력되는 데이터 형태가 다음 서브시스템의 입력과 일치해야 합니다. A에서 B 모듈로 파일을 이용해 입출력을 하는 아키텍처에서 중간에 S 서브시스템을 추가한다고 합시다. 이때 S 서브시스템의 출력은 파일이 아니라 Buffer 형태를 사용한다면 추가가 불가능 합니다. 이런 점은 유의해야 합니다.



위와 같은 특징을 생각했을 때 전통적인 절차지향적 구조라는 생각이 듭니다. Data Flow Software Architecture는 컴파일러나 Batch 데이터 처리 등에서 많이 사용합니다.



다음 글에서 Batch Sequential, Pipe and Filter, Process Control을 순서대로 정리해보겠습니다.

감사합니다.
