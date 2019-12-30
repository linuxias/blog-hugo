---
title: "[gcc] Warning 옵션"
date: 2019-12-30T23:24:46+09:00
---


gcc 컴파일 옵션으로 많이 사용하는 -Wall , -Wextra 이외에 다양한 옵션들은 정리하고자 합니다.

| 옵션 | 설명 | 특이사항 |
| :-- | :-- | :-: |
| \-fstack-usage | 컴파일러가 프로그램에 대한 스택 사용 정보를 함수 단위로 출력하도록 합니다. 함수의 이름, 바이트 수 등이 표기됩니다. | x |
| \-Wframe-larger-than={len} | 함수 프레임의 크기가 len을 넘어가면 Warning이 출력합니다. | x |
| \-Wstrict-overflow=n | \-fstrict-overflow가 활성화 되어있는 경우에만 활성화됩니다. 컴파일러가 signed 오버플로우가 일어나지 않을거라 가정하고 최적화를 진행하는 경우에 Warning이 발생합니다. | Wall에 포함 (-Wstrict-overflow=1) |
| \-Wlogical-op | 표현식에서 논리연산자의 사용에 대해 문제가 발생할 수 있는 경우 Warning을 발생합니다. | x |
| \-Wjump-misses-init | goto문을 통해 변수 초기화 이전으로 분기하거나, 변수가 초기화된 이후로 분기하는 것을 Warning이 발생합니다. | C, Objective-C only |
| \-Wmissing-include-dirs | 사용자 제공 include 디렉토리가 존재하지 않으면 Warning이 발생합니다. | C/C++, Obj C/C++ only |
| \-Wunused | 여러 unused 옵션(unused-but-set-parament, unused-but-set-variable, unused-function, unused-label, unused-local-typedefs, unused-parameter, -no-unused-result, unused-variable, unused-value)을 한 번에 포함하는 옵션입니다. 사용되지 않는 함수 매개 변수에 대한 경고를 얻으려면 -Wextra -Wunused (-Wall implies -Wunused)를 지정하거나 -Wunused-parameter를 별도로 지정해야합니다 | Wall에 -Wunused-function -Wunused-label -Wunused-value -Wunused-variable 옵션이 포함되어 있습니다. |
| \-Wpacked-bitfield-compat | 4.1, 4.2 및 4.3 계열의 GCC는 "char"유형의 비트 필드에서 "packed"속성을 무시합니다. GCC 4.4에서 그러한 필드의 오프셋이 변경되면 GCC에서 알려줍니다. | Default enable |
| \-Winvalid-pch | precompile된 헤더가 검색 경로에서 찾았으나 사용하지 못하는 경우 Warning이 발생합니다. | \- |
| \-Wstack-protector | Stack smashing으로부터 보호되지 않는 경우 Warning이 발생합니다. Stack smashing protector(SSP) 기능은 -fstack-protector 옵션을 사용해야 합니다. SSP 는 함수 진입 시 스택에 return address와 frame pointer 정보를 저장할 때 이 정보를 보호하기 위해 (canary라고 부르는) 특정한 값을 기록해두고 함수에서 반환할 때 기록된 값이 변경되지 않았는지 검사하여 정보의 일관성을 관리합니다. 만약 악의적인 사용자가 buffer overflow 등의 공격을 통해 스택 내의 정보를 덮어쓰려면 canary 값을 먼저 덮어써야 하기 때문에 canary 값 만 보면 공격이 일어났는지를 알 수 있습니다. | \-fstack-proctector가 활성화 된 경우 활성화 됩니다. |
| \-Wunused-variable | 지역변수 또는 상수가아닌 정적변수가 사용되지 않을 때 Warning이 발생합니다. | \-Wunused , -Wall에 포함 |
| \-Wunused-value | 명시적으로 사용되지 않은 결과를 계산하는 경우 Warning이 발생합니다. | \-Wunused , -Wall에 포함 |
| \-Wcast-qual | 포인터를 형변환 할 때 기존 type qualifier가 사라지는 경우 경고해줍니다(const char_을 char_로 형변환) | \- |
| \-Wconversion | 묵시적으로 타입을 변환하는 상황에서 값이 바뀔 가능성이 있는 경우 경고해줍니다(int temp = 0.3 등) | \- |
| \-Wsign-conversion | 부호있는 정수 표현식을 부호없는 정수 변수에 할당하는 것과 같이 정수 값의 부호를 변경할 수있는 변환에 대해 Warning이 발생합니다. | \-Wconversion 옵션에 의해 활성화됩니다. |
| \-Wbad-function-cast | 함수 콜이 매칭할 수 없는 타입에 캐스팅된 경우 Warning이 발생합니다. | \- |
| \-Wwrite-strings | constant 스트링을 non-const char\* 포인터에 복사하는 경우 Warning이 발생합니다. 컴파일 타임에 const string을 변경하려는 문제를 찾을 수 있습니다. | \- |
| \-Wconversion-null | NULL 과 non-pointer 타입간의 변환에 대해 Warning이 발생합니다. | Default enable |
| \-Wextra | \-Wall에 의해 활성화되지 않는 추가적인 Warning flags(-Wclobbered -Wempty-body -Wignored-qualifiers -Wmissing-field-initializers -Wmissing-parameter-type (C only) -Wold-style-declaration (C only) -Woverride-init -Wsign-compare -Wtype-limits -Wuninitialized -Wunused-parameter (only with -Wunused or -Wall) -Wunused-but-set-parameter (only with -Wunused or -Wall))를 활성화합니다. | \- |
| \-Wpacked | 구조체에 **packed** 속성이 주어졌으나, 해당 레이아웃이나 크기에 영향이 없는 경우 Warning이 발생합니다. 이런 구조체는 아주 작은 이득을 위해 잘못 정렬될 수도 있습니다. | \- |
| \-Wredundant-decls | 유효범위 내에 동일한 오브젝트(변수 등)이 여러 번 선언된 경우 Warning이 발생합니다. | \- |
| \-Waggregate-return | 구조체 또는 공용체를 반환하는 함수를 선언하거나 호출 시 Warning이 발생합니다. | 거의 사용하지 않는 Warning.. ANSI C 표준에 맞추기 위해 사용하나.. 흠, |
| \-Wpointer-arith | void의 크기나 함수의 크기를 갖고 연산(+/- 등)을 하는 경우 Warning이 발생합니다. | \- |
| \-Wswitch-default | switch 문에서 default case가 존재하지 않는 경우 Warning이 발생합니다. | \- |
| \-Wundef | 정의되지 않는 식별자가 #if, #endif 구문에서 사용된 경우 Warning이 발생합니다 | \- |
| \-Wstrict-prototype | 함수가 인자 형을 명시하지 않고 선언, 정의된 경우 Warning이 발생합니다. 즉, void test()와 같이 ()로 인자를 비워둔 경우 발생합니다. | \- |
| \-Wfloat-equal | 부동소수점 값이 ==, != 등의 등호로 비교된 경우 Warning이 발생합니다. | \- |
| \-Wformat-y2k | 2자리 연도를 출력하는 strftime()에 대해 Warning이 발생합니다. | \- |
| \-Wshift-count-overflow | Shift 연산이 타입의 크기와 같거나 큰 경우 Warning이 발생합니다. | Default enable |
| \-Wswitch-bool | switch문이 boolean 타입의 인덱스를 가지는 경우 Warning이 발생합니다. |  |
| \-Wno-conversion-null | NULL과 non-pointer 타입 간의 변환에 대해 Warning이 발생하지 않도록 합니다. | \-Wconversion-null이 Default입니다. |
| \-Wnested-externs | extern 선언이 함수 안에 존재하는 경우 Warning이 발생합니다. | \- |
| \-Wvarargs | va\_start와 같은 가변인자를 처리하는데 사용된 매크로의 의심스러운 사용에 대해 Warning이 발생합니다. | Default enable |
| \-Wunsuffixed-float-constants | 접미사가 없는 부동상수에 대해 Warning이 발생합니다. | \- |
| \-Wswitch-enum | switch문에서 index로 enum을 사용한 경우에 enum의 멤버와 case의 수가 맞지 않을 때 Warning이 발생합니다. | \- |
| \-Wshadow | 지역변수가 다른 지역변수, 매개변수 등(shadow) 덮는 경우 Warning이 발생합니다. |  |
| \-Wunreachable-code | 어떠한 경우에도 실행할 수 없는 코드 라인이 존재하는 경우 Warning이 발생합니다. | gcc 4.4 이상에서 제거됨 |
| \-Winline | inline으로 선언된 함수가 inline이 불가능 경우 Warning이 발생합니다. |  |
| \-funroll-loops | for()와 같은 루프문을 풀어서 최적화 해주는 옵션으로, 코드를 크게 만들어주며 수행속도가 빨라질수도 아닐수도 있습니다. |  |

감사합니다.
