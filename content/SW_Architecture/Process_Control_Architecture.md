+++
title = "Process Control Architecture"
+++

Process Control Architecture는 Data Flow Architecture 분류에 속하는 아키텍처입니다. 해당 분류에 속하는 아키텍처는 이전에 다뤘던 Batch Sequential, Pipe and Filter Architecture가 있습니다. 자세한 내용은 아래 링크 참고 부탁드립니다.

 - [Data Flow Software Architectures](https://sonseungha.tistory.com/507)

 - [Batch Sequential Architecture](https://sonseungha.tistory.com/508)

 - [Pipe and Filter Architecture](https://sonseungha.tistory.com/510)

Process Control Architecture에 대해 간략히 정리해보겠습니다.

Process Control의 가장 큰 특징은 데이터의 흐름이 프로세스의 실행을 제어하는 변수 집합이라는 것입니다. 한 번에 이해하기 어려운 말인 것 같습니다. 좀 더 살펴보겠습니다.

Process Control Architecture는 임베디드 시스템에서 많이 사용됩니다. 시스템이 프로세스를 제어할 수 있는 변수에 의해 조작되는 시스템에 알맞는 아키텍처입니다. 많은 임베디드 시스템은 연속적으로 동작해야 합니다. 안정된 상태에 대한 출력 데이터를 유지하는게 가장 중요한 시스템입니다. 예를 들어 크루즈나 화장실 변기를 많이 예시로 듭니다. 화장실 변기 물을 내리면 다시 물이 차오릅니다. 그 때 차오르는 물의 높이는 항상 일정합니다. 이러한 시스템은 물의 높이 즉 출력 데이터를 안정화 시키기 위해 프로세스가 데이터를 제어하게 되는 구조가 됩니다.

해당 시스템을 구성하는 몇 개의 서브시스템이 있는데 각 서브시스템은 아래와 같습니다.

-   Controlled Variable : 기본 시스템에 대한 값을 제공하며 센서에 의해 측정되어지는 값
    
-   Input Variable : 프로세스에 대한 입력 값
    
-   Manipulated Variable : 컨트롤러에 의해 조정되거나 변경되는 값
    
-   Process : 변수를 조작하기 위한 메커니즘
    
-   Sensor : 시스템 제어와 같련된 변수의 값을 구하며 조작된 변수를 재계산 하기 위한 피드백으로 사용
    
-   Set Point : 이 값은 제어된 변수에 대한 원하는 값
    
-   Control Algorithm : 프로세스 변수 조작 방법을 결정하는데 사용함
    

![Controll Architecture for Cruise Control](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile23.uf.tistory.com%2Fimage%2F9913CF335C924CC920F84C)

출처 : https://www.cs.cmu.edu/afs/cs/project/tinker-arch/www/html/Tutorial\_Slides/Soft\_Arch/base.097.html

위 그림은 Cruise Control 시스템의 예제입니다. 자동차의 크루즈 모드는 일정한 속도를 유지하기 위한 시스템입니다. 원하는 속도가 입력 값이 되고 컨트롤러에 의해 Throttle이 설정됩니다. 해당 Throttle은 엔진을 동작하게 만들고 엔진은 바퀴를 회전시킵니다. 바퀴의 회전은 센서에 의해 측정되고 Controller에게 전달됩니다. Controller는 Desired Speed에 도달할 때 까지 지속적으로 Throttle을 설정하게됩니다.

위 와 같은 시스템이 Process Control Architecture를 적용한 시스템이라고 보시면 됩니다. 바퀴의 회전을 센서로 다시 컨트롤러에게 전달되는데요, 이처럼 출력 데이터가 다시 컨트롤러의 입력으로 전달되어 Close-loop 형태를 뛰는 구조들이 있습니다. 만약 출력 데이터가 다시 피드백 되지 않는다면 Open-loop 형태라고 합니다. Close-loop Feedback은 Open-loop보다 출력 데이터를 제어하는데 훨씬 좋은 구조라는 것을 알 수 있을겁니다.

이상으로 Process Control Architecture에 대해 정리해보았습니다.

감사합니다.