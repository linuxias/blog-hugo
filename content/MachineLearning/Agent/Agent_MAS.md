+++
title = "[Agent] MAS (Multi-agent System)"
+++

### MAS (Multi-agent System)

현재 세계의 시스템에선 하나의 agent로는 모든 처리를 할 수 없습니다. 그래서 각각의 기능을 담당하는 여러 agent를 생성하여 협업 또는 조율하게 만드는 방법을 사용하기도 합니다. 이번 글에서는 MAS, Multi-agent System에 대해 정리해보겠습니다.

Multi-agent System (지금부턴 MAS라 칭하겠습니다.)은 시스템 내에 여러 agent를 가지고 있는 시스템입니다. 각 agent 간 커뮤니케이션을 통해 상호작용을 하게 되고, 서로 다른 작업을 하기에 "spheres of influence" 라고 불리는 자신만의 환경 영역을 가지고 있습니다. 즉 변화하는 환경에 대해 영향을 받거나 주는 환경이 다를 수 있습니다. 분산 AI 측면에서 MAS를 살펴보면 하나의 Agent로는 해결하기 어려운 문제들을 여러 Agent가 서로 보유한 다른 정보들과 능력을 이용하여 해결하게 됩니다. 문제의 해를 찾기위해 함께 동작하게 되며 서로간은 루즈하게 결합되어 있습니다. 각각의 agent는 문제 해결을 위해 불완전한 기능들을 가지고 있습니다. 하나의 agent만으로는 문제 해결이 불가능하단 얘기이죠. 또한 MAS는 중계자, 전체 시스템을 컨트롤하는 녀석이 별도로 존재하지 않고 각 데이터도 agent들에게 분산되어 있기에 탈 중앙화 시스템입니다. 이러한 탈 중앙화로 보안성과 안정성이 확보되었습니다. 개별적으로 agent가 동작할 수 있기에 비동기적으로 동작되는 시스템입니다.

MAS에서 agent가 모두 협렵하는 방식으로 구성되는 건 아닙니다. agent들이 서로 경쟁하도록 만들수도 있죠. 문제해결을 위한 방법 중 하나인데요. 하나의 문제를 협력하여 해결하는게 아니라 서로 경쟁시킴으로써 가장 좋은 해를 구할 수 있게 됩니다. 뭐가 더 좋은지는 상황에 따라 다르겠지요. 좀 더 자세히 Cooperative MAS와 Competitive of Self-interested MAS에 대해 정리해 보죠 

  
#### Cooperative MAS
   
1.  대표적인 MAS domain입니다.
2.  분산 문제 해결로 각 agent는 자율성이 낮습니다.
3.  협력과 팀웍을 위한 모델로써 각자 새우는 계획이 다릅니다. 다른 동작을 하기 때문이죠.

#### Competitie of Self-interested MAS
1.  분산된 합리성입니다. 투표나 경쟁하는 시스템입니다.
2.  협상 등에 자주 사용되는 모델입니다.

MAS의 전통적인 모델은 서버-클라이언트 모델이였습니다. 서버-클라이언트 간 커뮤니케이션 또한 매우 Low-level의 메시지들을 사용하였으며 동기적으로 동작하였습니다. 클라이언트가 요청하면 서버가 응답하는 구조였죠. 하지만 지금은 여러 Agents들이 Peer-to-Peer로 연결되어 있으며 서버라는 개념이 없습니다. 캡슐화된 메시징 기법으로 보안성도 강화화였으며 High-level의 메시지 프로토콜들을 사용하게 되었습니다. MAS를 연구하는 사람들은 Agent간 커뮤니케이션을 매우 중요하게 생각하였습니다. 그래서 멀티 시스템에 맞는 Communication Language, Interaction Protocol 들을 개발하게 되었지요. 개발에 영감을 준건 곤충의 군집생활입니다. 이런 MAS와 같은 시스템에 좋은 본보기가 될 수 있죠. 공통의 목적을 위해 어떻게 군집활동을 하고 어떻게 커뮤니케이션하며 어떠한 룰을 정하는가와 같은 점을 토대로 만들었습니다.

  

그럼 유명한 MAS 몇개 모델만 간단히 정리해보겠습니다. 

  

##### Object Manager Group (OMG)  
일반적인 패턴들과 정책들을 사용하여 협업하는 에이전트들과 에이전시들로 구성되어 있습니다. 에이전트는 능력, 상호협력의 타입으로 특정지어지며 에이전스들은 에이전트들의 동시 실행, 보안 등을 지원하는 녀석입니다. 
    
##### FIPA(Foundation for Intelligent Physical Agents)'s Model  
Agents, Agent Platform, Directory Facilitator, Agent Management System, Agent Communication Channel, Agent Communication Language 로 구성되어 있는 모델입니다.
    
##### KAoS'S Model  
에이전트를 위한 개방형 분산 아키텍쳐입니다. 다양한 에이전트 구현들을 정의하고 있으며 에이전트 간 커뮤니케이션을 위해 대화 정책을 사용합니다.
    
##### OAA Model  
User Interface, NL to ICL, Application, Meta에이전트들과 이 에이전트들이 커뮤니케이션을 하는 Facilitator 에이전트로 구성되어 있습니다. 어플리케이션은 API를 통해 Application Agent를 사용하게 되고 User Interface 에이전트는 사용자의 요청등을 처리하게 됩니다. 각 에이전트간의 커뮤니케이션은 모두 Facilitator 에이전트가 중재하게 되는 구조입니다.
    
##### General Magic's Model  
전자상거래를 위한 금융 에이전트로 시작되었습니다. 
    
##### Zeus (MAS development toolkil)  
MAS 개발을 위한 개발도구 입니다.
    
MAS의 각 Agent들은 서로 조직을 이루기 위해 다른 기능을 가지고 있는데요, 그 기능들 사이의 종속성들을 관리하는 과정을 Coordination이라고 합니다. Coordination을 통해 순서나 병렬적 수행들을 관리할 수 있죠. 이런 Coordination은 Activity, Conversation, Implementation 3가지 측면에서 살펴보겠습니다. 

1. Activity  
 실행해야 할 행위가 무엇인지 언제 실행되어야 하는지에 대한 것입니다. 분산 태스크에 대해 Statechart나 Flowchart, process algebra 등으로 표현할 수 있습니다.
    
2. Conversation (State)  
 협력하는 개체들간의 대화 구조가 무엇인지에 대한 것입니다. FSM, Petri-Nets, State Transition Diagram 등이 있습니다.
    
3. Implementation  
 어떻게 분산 시스템을 구현할지 협력하는 행위들의 요소들이 어디에 위치해야 할지에 대한 것입니다.
    

  

또한 MAS는 기본 협업 메커니즘(Coordination mechanism)에 기초한 지식을 교환하여 단일 목표에 도달하기 위해 에이전트 간 협업을 합니다. 대표적인 예시가 CNP(Contract Net protocol)입니다. CNP는 4개의 Phase로 구성되어 있습니다.

 - Phase 1 - Task Announcement  
    계약 에이전트가 태스크를 수행할 능력이 있는 에이전트들이 나타나도록 브로드캐스팅합니다. 그 태스크를 수행하기 위한 잠재적 후보자들을 평가하기 위함입니다.
    
 - Phase 2 - Submission of Bids / Proposal  
    요구를 만족하고 태스크를 수행할 능력이 있는 에이전트들은 계약 에이전트에게 입찰을 하게 됩니다. 본인이 해당 태스크를 처리할 수 있도록 말이죠.
    
 - Phase 3 - Selection  
    계약 에이전트는 받은 입찰들을 기반으로 최종 후보자를 선택하는 과정을 진행합니다.
    
 - Phase 4 - Contract awarding  
    계약 에이전트, 최종 선택된 에이전트간 계약이 성립되고 상호 커뮤니케이션을 위한 채널이 생성됩니다.
    

  

위와 같은 단계를 거쳐 Collaboration이 일어나게 됩니다. 일반 사회에서 일어나는 경매 시스템과 비슷한 것을 느끼시나요? 그에 맞춰서 이해하셔도 좋을 것 같습니다.
 
이 글의 앞부분에서 MAS는 Cooperative와 Competitive 모델이 있다고 했습니다. Competitive보단 Cooperative가 많이 사용되는데 Competitive 모델을 디자인하는데 몇 가지 이슈를 확인해보겠습니다. 

첫 번째 이슈는 Distributed Rationality입니다. Distributed Rationality는 Self-interested Agent 들이 샌드 박스에서 공정하게 경쟁하도록 유도하거나 강제하는 기술입니다. 모든 에이전트들의 의견을 취합하는 투표방식이나 모든 에이전트들이 얼마나 공정하게 기회를 부여받을지에 대한 경쟁등이 있습니다. 공정성, 안정성, 거짓말, 전역적으로 사용성을 높이기 위한 방안 등에 대한 이슈입니다. 

두 번째 이슈는 Pareto optimality(파레토 효율성)입니다. Pareto Optimality는 \[1\]어떤 경제주체가 새로운 거래를 통해 예전보다 유리해지기 위해서는 반드시 다른 경제주체가 예전보다 불리해져야만 하는 자원배분상태를 의미합니다. 최적화를 위해서 단편적으로 보아서는 안되며 여러 측면에서 고려가 필요한 문제입니다. 

마지막으로 Stability(안정성)입니다. 만약 어느 에이전트가 특정 전략을 가지고 다른 에이전트들의 행동과 관련없이 사용성을 항상 최대화 할 수 있다면 어떻까요? 다른 에이전트의 전략을 고려할 때 각 에이전트의 전략이 지역 최적인 경우 에이전트 전략 집합은 \[2\]내쉬 균형([게임이론](https://terms.naver.com/entry.nhn?docId=778576&ref=y)의 개념으로서 각 참여자(Player)가 상대방의 전략을 주어진 것으로 보고 자신에게 최적인 전략을 선택할 때 그 결과가 균형을 이루는 최적 전략의 집합을 말한다)에 있습니다. 그렇게 되면 전략을 변경하려는 에이전트도 없을 것입니다. 

  

MAS를 어떤 플로우로 개발하는지 살펴보겠습니다.

1. 특정 문제를 해결하기 위한 MAS 구성을 정의한다.
    
2. Coordination mechanism을 결정한다.
    
3. MAS를 구현하기 위한 프레임워크(Zeus 같은)를 선택한다.
    
4. MAS를 지원할 Collaborative mechanism을 구현한다.
    
5. Shared ontology를 구현한다.
    
6. 각 태스크 에이전트를 구현한다.
    
7. 중간자 에이전트를 커스터마이징 한다. (Facilitators, Mediators, Brokers, ...)
    

큰 그림부터 필요한 요소들을 하나하나 선택, 결정하고 구현하는 단계를 거칩니다.


이러한 MAS는 다양한 곳에서 사용됩니다. 인터넷에서 정보검색을 하거나 보안 강화나 전자상거래의 쇼핑 에이전트들, 분산 신호처리 시스템 등에서 다양하게 사용됩니다.
  

뭔가 주절주절 나열한 것 같습니다. 이해가 안되시는 부분이나 잘못된 부분은 언제든지 댓글로 남겨주세요. 

감사합니다.
  

  

\[1\] https://terms.naver.com/entry.nhn?docId=3437709&cid=58393&categoryId=58393

\[2\] https://terms.naver.com/entry.nhn?docId=778808&cid=42085&categoryId=42085