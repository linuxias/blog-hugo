+++
title = "[Agent] BDI Architecture"
+++

### Belief - Desire - Intention Architect

BDI Architect는 Software Agent 분야에서 자주 사용되었던 구조입니다. 이 구조의 이름인 BDI 는 3가지 단어 입니다. Belief, Desire, Intention 이 3가지 단어의 앞자리를 따서 만들어졌습니다. 그럼 3가지 단어가 이 구조의 큰 요소일 텐데 구조를 정리해 가며 설명드리겠습니다.

BDI Architect는 목표를 이루기 위해 순간, 순간 행해야할 행위를 결정하는 의사결정 프로세스입니다. BDI 구조는 reactive behavior와 goal-directed behavior가 조화를 이루는 구조로써 Agent는 그 목표를 달성하기 위해 최선을 다하면서도 그 목표가 여전히 유효한지, 달성할 수 있는지에 대해 계속해서 확인하는 과정을 거치게 됩니다.

BDI agent는 두 개의 중요한 프로세스를 사용합니다. Deliberations와 Means-ends reasoning인데요, Deliberation은 우리가 달성하기 원하는 목표가 무엇인지 결정하는 프로세스이며 Means-ends reasoning은 어떻게 우리가 그것을 달성할지에 대해 결정하는 것입니다. 이름은 어려운데 결국 어떤 목표를 어떻게 달성해야 할지에 대한 것을 프로세스를 나눠놓은 것이라 생각하시면 편합니다. 어떤 목표를 세우고 어떻게 달성하지에 대해 BDI에서는 3가지 개념을 이용해 정의해 놓았습니다. 가장 처음 말씀드린 BDI 입니다. 하나의 예시를 개념 별로 들어보겠습니다.

 - B (Beilive) : 만약 내가 공부를 열심히 한다면 이번 시험을 패스할 수 있습니다.

 - D (Desire) : 이번 시험을 통과하길 바랍니다.

 - I (Intend) : 나는 공부를 열심히 할겁니다!!


사람이 무엇인가 목표를 세우고, 목표를 이루기 위해 어떻게 해야할지 결정하는 과정과 유사한 것 같습니다. BDI가 이 구조에서 어떤 역할을 하는지 정리해야 할 것 같습니다. Belief는 Agent가 가진 믿음, 정보입니다. 여기서 이 정보가 100% True일 필요는 없으며 Agent가 어떠한 정보를 가지고 있다라는 것이 중요한 점이라고 생각됩니다. 조금 의아하시겠지만, 아래서 더 자세히 설명드리겠습니다. Desire은 목표입니다. 이번 시험에서 통과하는게 목표가 되겠네요. 그리고 Intend는 목표를 이루기 위해 행동하는 방안입니다.

위에서 너무 나불나불 한 것 같아서 예시를 한번 들어보겠습니다. 예시는 호텔 매니저로 하겠습니다. 여러분들이 호텔 매니저라면 B, D, I 가 무엇이 되어야 할지 한번 생각해보시죠. 먼저 호텔 매니저는 룸 관리를 해야 할 것 입니다. 예약 상태나 현재 어느 객실에 고객이 숙박하고 있는지 등의 관리가 필요할 것입니다. 저는 아래와 같이 정리하였습니다.

 - B (Beilive) : 룸 상태와 스케쥴 정보(예약 상태, 현재 숙박 여부 등)를 알고 있어야합니다.

 - D (Desire) : 고객 문의에 대해 정확한 룸 상태와 예약 여부등을 알려주고 싶습니다.

 - I (Intend) : 룸 상태가 청결하며 현재 예약 여부 및 숙박 상태를 정리해 예약을 받아야 합니다.


뭔가 정리가 잘 안된 것 같지만.. 얼추 이해가 되시나요? 더 좋은 의견 있으시면 댓글로 부탁드려요 :D

계속해서 설명드리겠습니다. Intention의 역할에 관한 것입니다. Intention은 목표를 이루기 위한 행위라고 하였습니다. 어떻게 달성할지에 대한 행위라고 했죠. 만약 이 행위가 실패한다면 또 다른 방법으로 목표를 달성하기 위해 행동해야 할 것 입니다. 이 행위는 목표를 달성하기 위해 쉽게 포기해서는 안될 것 입니다. 그렇다고 무조건적으로 유지되는 것도 아니죠. 만약 더 좋은 정보로 인해 목표 달성에 좋아진다면 행위가 변경될 수 있습니다. 아까 공부에서 열심히 공부한다도 좋겠지만, 시험 감독이 없다 라는 정보가 있다면(잘못된 정보라 하더라도) 컨닝을 한다 라는 행위가 발생할 수 있다는 것이죠. 최종 목표는 시험을 통과하는 것이닌깐요. 이러한 Intention은 추후 practical reasoning을 기반으로 Belief에 영향을 주게됩니다.

그럼 얼마나 자주 현재 진행하고 있는 Intention이 재검토 될까요? 현재 결정된 Intention이 목표 달성에 불가능한 행위인데도 불구하고 계속 진행한다면 잘못된 것일테니 분명 재검토는 필요합니다. 공부를 계속해도 안된다면 컨닝으로 방향을 틀어서라도 목표를 달성해야죠!! (물론 잘못된 행동이지만요..) 재고의 빈도에 대해서는 2가지 Agent로 나뉠 수 있습니다. Bold agent와 Cautious agent인데요. 먼저 Bold agent는 대범한 에이전트입니다. 거의 재고하는 일이 없는 녀석이죠. 더이상 불가능하거나 달성할 이유가 없더라도 우직하게 수행해 나가는 에이전트입니다. 다른 하나는 Cautious agent입니다. 조심스러운 녀석으로 Intentions 수행위해 계속하여 시간을 투자해서 끊임없이 재고함으로써 결국 목표를 달성하지 못하는 에이전트입니다. Intention을 재고하는 가장 좋은 방법은 Bold도 Cautious도 아닌 둘의 적절한 균형이 필요할 것 같습니다. Agent는 항상 동일한 환경과 동일한 외부자극을 받는게 아니니 환경과 외부자극에 대해 현재 Intention을 검토할 필요가 있습니다. The rate of world change(r) 라는 값을 이용하게 됩니다. r이 낮다면 bold agents 쪽으로 더욱 강화하면 좋습니다. 외부 환경 변화가 적은편이니 현재 Intention을 유지해 나가면 좋죠, 반대로 r이 높다면 Cautious agent 쪽에 중점을 두면 좋습니다. 새로운 변화와 자극은 기회가 될 수 있고 어떠한 이점이 있는지 검토가 필요하닌깐요. r에 변화에 따라 어느 방향으로 나아갈 지 결정된다면 좋을 것 같네요.

그럼 이제 BDI Architecture의 큰 그림을 살펴보겠습니다.

![Image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile8.uf.tistory.com%2Fimage%2F99AFBB395BD54F381C4F02)

-   A belief revision function (brf)  
	\- 외부환경, 자극, 입력과 현재 Beliefs를 이용해 정보를 표현합니다.

-   A set of current beliefs  
	\- 현재 Beliefs의 집합

-   An option generation function  
	\- 환경 및 현재 Intention들 그리고 Belief 들을 기반으로 할 수 있는 Options들을 결정하게 됩니다.

-   A set of current desires (options)  
	\- 현재 Desire들의 집합입니다. 

-   A filter function  
	\- Intentions을 표현하기 위한 녀석으로 Deliberation Process를 나타냅니다. 이때 현재 Belief, Desire, Intention 모두를 사용합니다.

-   A set of current intentions  
	\- 현재 Intection들의 집합

-   An action selection function  
	\- 현재 Intention들을 기반으로 수행할 Action을 선택합니다.


BDI Architecture에 대해 간단하게 정리해보았습니다. Belief, Desire, Intention을 기반으로 입력, 외부환경, 자극에 대해 최종적으로 목표를 이루기 위한 Output이 결정되는 모델이라는 점에 주목하면 좋을 것 같네요. 

부족한 점이나 수정해야 될 부분이 있다면 언제든지 댓글 부탁드립니다. 감사합니다.
