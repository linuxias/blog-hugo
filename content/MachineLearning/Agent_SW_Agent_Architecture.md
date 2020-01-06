+++
title = "[Agent] SW Agent Architecture"
+++

소프트웨어 에이전트 아키텍쳐에 대해 정리해보겠습니다.

에이전트 구조는 3개로 크게 나뉠 수 있습니다.

-   Deliberative : 의도적인, 신중한

-   Reactive : 반응하는

-   Hybrid : Deliberative + Reactive


용어 의미만 보아서 파악할 수 있는건 Hybrid밖에 없는 것 같네요. 하나씩 정리해보도록 하겠습니다.



#### Deliberative Agents

Deliberative Agents는 명확하게 표현되어 질 수 있는 실세계의 상징적 모델이며 Symbolic reasoning을 통해 결정을 만들어 나가는 에이전트입니다. Sense-plan-act 를 통한 문제 해결방식으로 Deliberative 구조로 BDI, GRATE\*, HOMER, Shoham 등이 유명합니다. Deliberative는 Deductive reasoning agents와 Practical reasoning agents로 나뉘어져 있습니다. Deductive reasoning은 연역적 추론을 기반으로 한 Agent이며 Practical reasoning은 실용적 추론을 기반으로 한 Agent 입니다.



Deliberative Agent는 아래와 같은 플로우를 가집니다.



Input -> **\[ Sensors -> World Model -> Planner -> Plan Executor -> Effector \]** -> Output



입력에 대해 Sensing하고 Model에 대해 목표 달성을 위한 Plan을 세우게 됩니다. 이 때 Plan은 하나일 수도 있고 여러개 일 수도 있습니다. 그 중 목표달성을 위한 적절한 Plan을 선택하여 실행하게 되는 순서입니다.  



Deliberative Agents의 문제는 Performance와 Representation 문제가 있습니다. Performance는 환경이 급속하게 변화된다면 상징적 표현을 필요한 모든 정보로 변환하는데 시간 소모가 많습니다. Representation은 어떻게 세계 모델이 상직적으로 표현될 수 있는가와 그리고 결과를 유용하게 사용할 수 있도록 제 시간에 정보를 근거로 에이전트를 추론하는 방법에 대한 문제가 있습니다. 이렇게 느린 결과는 쓸모가 없을 뿐더러 실제 세계 모델은 매우 복잡하기에 심볼로만 처리하기엔 어려움이 많습니다.



Deliberative Agents에는 Deductive Reasoning과 Practical Reasoning이 있다고 말씀드렸는데요, 이 둘에 대해 자세히 정리해 보겠습니다. 



먼저 Deliberative Agent입니다. 연역적 추론을 기반으로하는 에이전트 인데요, 연역적 추론이란 전제들이 참이면 결론은 항상 참이라는 것이 보장되는 추론입니다. 에이전트가 어떻게 정리 이론을 이용해서 무엇을 하는지 결정해야하는가? 에 대한 질문을 던질 수 있는데요, 기본적인 아이디어는 주어진 상황에서 수행가능한 최고의 액션을 서술하는 이론을 인코딩하는 사용하는 것입니다. 뭔가 어렵네요. Deductive Reasoning은 매우 단순하고 논리적인 의미를 담고있습니다. 하지만 어떻게 어떻게 실세계의 정보를 심볼정보로 변경할 지, 추론에 대한 시간 복잡도는 어떨지 고민해봐야 할 것 같습니다.



다음 Practical Reasoning입니다. Practical Reasoning은 행동에 대한 Reasoning입니다. Practical reasoning은 두 개의 activity로 구성되어 있는데요, Deliberation과 Means-ends reasoning입니다.

-   Deliberation (숙고, 신중함) : 달성하고자 하는 일의 상태가 무엇인지 결정하는 것

-   Means-ends reasoning (방법 목적 추론) : 일의 상태들을 달성하기 위해 어떻게 해야하는지 결정하는 것


어렵네요. Deliberation은 달성하고자 하는 일 자체의 상태이고, Means-ends reasoning은 일의 상태를 달성하기 위해 어떠한 과정을 결정해야 하는 것인가(?) 로 생각해보면 좋을 것 같습니다. Deliberation의 출력이 곧 Intentions입니다. Intentions이란 용어는 에이전트가 선택하고 실행해야할 일의 상태를 말합니다. Practical reasoning에서 중요한 역할을 하는 녀석입니다. Intentions은 means-ends reasoning을 드라이브하고 지속됩니다. 미래의 Deliberation을 제한하기도 하며 미래에 대한 Belief들과 연관된 녀석입니다. 좀 어렵나요? 천천히 정리해보시기 바랍니다. 다음 Means-end reasoning은 에이전트에게 달성하기 위한 목표, 수행할 수 있는 액션들, 환경의 표현을 주기 위함입니다. 목표를 달성하기 위한 계획을 생성하는 것입니다.



#### Reactive Agents

Reactive agent는 내부적으로 세계의 표현을 매우 단순화하였으며 Perception과 Action이 매우 강하게 결속되어 있는 에이전트입니다. 행위 기반 에이전트의 전형적인 예로서 에이전트 내부에서 각 상태에 따른 Agent가 나뉘어져 있습니다.

\[ State 1 -> Action 1 \]

\[ State 2 -> Action 2 \]

Sensors ->\[ State 3 -> Action 3 \] ->Effectors

 \[ State 4 -> Action 4 \]

 \[ State 5 -> Action 5 \]



위 처럼 Sensor에서 받아들인 입력을 각 State로 전달하고 액션을 결정하여 Effector로 전달합니다. 각 행위는 지각의 입력에서 출력이 연속적으로 매핑되어 있는 구조입니다. Reactive Agents는 매우 단순하고 경제적이며 구현하기 매우 쉽습니다. 시스템 문제에 대한 강건성 또한 이 에이전트의 이점입니다. 하지만 문제도 다양합니다. 환경 모델 없는 에이전트들은 지역적 환경으로부터 가능한 충분한 정보를 가져야 합니다. 만약 지역 환경을 기반으로한 결정들은 비지역 환경들에 대해선 어떻게 고려할 것이냐도 문제가 됩니다. 또한 Reactive agents는 학습시키 매우 어렵습니다. 마지막으로 상호작용하는 요소들과 환경에 따라 동작이 달라지기 때문에 특정 에이전트를 엔지니어링 하기 어렵습니다.



#### Hybrid Agents

Hybrid Agent는 Deliberative와 Reactive 행위의 결합입니다. 계획을 세우거나 상징적 추론을 사용해 결정을 만드는 시스템과 복잡한 추론 없이 이벤트에 대해 빠르게 반응하는 시스템이 Hybrid Agent의 서브시스템으로 구성되어 있습니다. Sensor와 Effector 사이에 Deliberative 요소와 Reactive 요소가 함께 포함되어 있습니다. Deliberative와 Reactive를 자세히 알아보았으니 Hybrid는 두개의 결합이라고 이해하면 가장 좋을 것 같습니다 :D



잘못된 내용이나 궁금한 내용은 댓글 부탁드립니다. 긴 글 읽어주셔서 감사드립니다.
