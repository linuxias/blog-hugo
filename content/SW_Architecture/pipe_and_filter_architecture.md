+++
title = "Pipe and Filter Architecture"
+++

Data flow Architecture에는 Batch Sequential, Pipe and Filter, Process Control Architecture 로 3가지로 분류할 수 있습니다. 그 중 Pipe and Filter Architecture에 대해 정리해보려 합니다.

Pipe and Filter Architecture는 데이트 스트림을 처리하는 시스템을 위한 구조를 제공합니다. 데이터를 처리하는 각 프로세싱 단계는 Filter 컴포넌트 내부에 포함되어 있습니다. 데이터는 Filter 사이를 Pipe를 통해 전달되게 됩니다. 


![Image](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=http%3A%2F%2Fcfile2.uf.tistory.com%2Fimage%2F99545C3F5C89238C0A5135)


이러한 구조로 인해 Pipe and Filter Architecture는 Batch Sequential Architecture와 많이 비교됩니다. 

Pipe and Filter 구조의 구성요소는 크게 3가지 입니다. 데이터 스트림, 필터, 그리고 파이프입니다. 

데이터 스트림은 XML이나 Json 파이트 스트림 등 first-in / first-out 버퍼를 가지고 있습니다. 특정 시스템에서는 마샬링, 언마샬링도 사용합니다. 다음 구성요소인 필터는 Pipe and Filter Architecture에서 독립적으로 데이터 스트림을 처리하는 구성요소입니다. 입력 데이터 스트림으로부터 데이터를 읽고, 읽어들인 데이터를 처리한 후 다음 필터로 전달하도록 파이프로 데이터를 전달합니다. Pipe and Filter는 데이터가 연결된 파이프를 통해 전달되면 그 즉시 처리를 하고 다음 필터로 전달합니다. 필터를 독립적으로 동작하므로 시스템에서 자유롭게 교체 및 추가가 가능합니다. 여기서 필터는 2가지 타입으로 다시 분류할 수 있습니다. Active(능동형) 필터와 Passive(수동형) 필터입니다. 먼저 능동형 필터는 데이터를 가져오고 전달하는 것을 필터에서 처리하고 수동형 필터는 파이프가 필터로부터 데이터를 가져오고, 다음 필터로 전달합니다. 즉 능동형 필터는 수동형 파이프와 함께 동작하고 수동형 필터는 능동형 파이프와 함께 동작합니다. 마지막 구성요소인 파이프는 필터 사이에 데이터 스트림을 이동하는 경로입니다.

Pipe and Filter 구조는 리눅스 사용자라면 많이 사용하는 파이프를 생각하시면 편합니다. 

```sh
$cat example.txt | grep 'test'
```

위와 같은 예제는 쉽게 이해할 수 있습니다. cat을 이용해 example.txt 내부 문자열들을 파이프를 통해 grep에게 전달되고 처리하게 됩니다.

이러한 Pipe and Filter Architecture의 장점은 Concurrency(동시성), Reusability(재사용), Modifiability(변경용이성), Simplicity(단순성), Flexibility(유연함)입니다. Concurrency는 과도한 데이터 처리에 대해 각 필터가 독립적으로 동작하여 높은 처리량을 얻을 수 있습니다. Reusability는 각 필터가 독립적으로 동작되며 다른 필터와의 종속성이 없으므로 각 필터를 다른 시스템에 재사용이 가능합니다. Modifiability는 필터 간 종속성이 낮기에 새로운 필터를 추가하거나 수정, 제거했을때에도 시스템에 다른 수정을 최소화 할 수 있습니다. 두 필터 사이의 파이프가 존재한다는 매우 단순한 구조를 가지고 있으며 각 해당 시스템의 데이터를 Sequential하게 Parallel하게 수행이 가능함으로 유연한 구조를 만들 수 있습니다.

하지만, Pipe and Filter 구조에도 여러 단점이 있습니다. 데이터 스트림 형태가 고정된 형태의 구조이기에 동적으로 데이터 포맷을 변경하는 구조에는 알맞지 않습니다. 만약 A 필터로 이미지가 입력되었는데 출력으로는 XML 포맷으로 출력하고 B 필터는 XML을 입력받아 Character Stream으로 출력한다면, 이 구조의 장점인 변경용이성, 유연함을 잃어버리게 됩니다. 

이런 장점과 단점을 이해하고 정확하게 필요한 곳에 구조를 적용하는 연습이 필요할 것 같습니다. 마지막으로 많이 비교되는 Batch Sequential Architecture와의 차이점을 정리하고 글을 끝맺겠습니다.

#### Batch Sequential 과의 차이점

위에서 정리한 내용을 보면 Batch Sequential Architecture와 매우 유사해 보이지만 큰 차이점을 가지고 있습니다. Batch Sequential은 데이터가 처리되고 다음 데이터 처리 단계로 넘어가기 위해선 이전 데이터 처리가 모두 완료되어야 합니다. 즉 A에서 B 처리 단계로 데이터가 전달되기 위해선 모든 데이터가 A처리가 완료된 이후 B로 입력됩니다. 하지만 Pipe And Filter Architecture는 데이터 스트림 처리를 위한 구조로서 A 단계에서 모든 데이터가 처리되고 B의 입력이 되는 것이 아닌 A 단계에서 먼저 처리된 데이터는 바로 B의 입력으로 Pipe를 통해 전달됩니다. 100개의 데이터가 있을 때 Batch Sequential은 100개가 모두 처리된 이후 다음 스텝으로 입력되지만, Pipe And Filter는 100개 중 처리된 데이터는 B로 전달됩니다. 이 점이 가장 큰 차이입니다.

감사합니다.
