## Managed 언어

인터넷이나 유튜브에서 자바가 Managed 언어라는 말을 들었다. Managed? 뭐가 관리된다는거지? 그리고 Unmanaged 언어도 있다는데 정확한 구분이 뭔지 궁금해서 공부해 보았다. 

'Managed Language' 라는 말은 사실 외국에서는 그렇게 많이 쓰이지 않는 용어인거 같고 구글링이나 스택오버플로우를 찾아보면 'Managed Code'라고 더 많이 쓰이는 듯하다.

위키디피아에 따르면 Managed Code라는 말은 MS 진영에서 C#을 만들면서 처음 사용된 용어로 **코드가 하드웨어에서 바로 구동 되는 것이 아니라 특정 런타임 환경에 의해 관리되고 의존하는 코드**를 의미한다. 

특정 런타임 환경이란 자바의 JVM이나 C#의 CLR, 자바스크립트의 V8와 같이 특정 언어 코드를 구동해주는 가상머신, 인터프리터, 엔진등을 의미하며 

해당 머신들은 인터프리터 동작 이외에도 구동되는 코드들의 **메모리를 관리를 해주거나** 보안, 스레드 운용등의 보조를 받는 특징이 있다.

### 정 의

'Managed Language'란 런타임 환경에서 메모리를 관리해주는 공통점에 방점을 두어 **보통 GC(Garbage Collector)를 쓰는 언어들**을 말하는 듯 하다. 

어떤 분들은 위의 Managed Code를 그대로 계승하여 특정 런타임 환경에 의존하는 언어를 Managed Language로 정의해야한다고 하기도 하는데 광의 /협의의 개념으로 이해하고 넘어가면 될거 같다. 

### 장점
- 런타임 환경에서 다양한 도움, 특히 메모리 관리를 자동으로 해주기 때문에 메모리 누수의 문제에서 보다 자유롭다.
- 코드가 런타임환경에 의존하므로 하드웨어나 OS에 종속되지 않는다.

### 단점
- 중간 매개체를 두는 만큼 성능적인 부분에서 손실이 날 수 밖에 없다.
- GC에 의해 의도치 않은 오작동이 발생할 위험성이 존재한다.

## Unmanaged 언어
Managed 언어의 반대 개념으로 특정 **런타임 환경의 관리를 받지 않는 모든 언어**는 Unmanaged Language라고 정의할 수 있다.

C/C++과 같은 언어가 대표적인 Unmanaged Languaged 이다.

### 장단점
Unmanaged Language에 비해 상대적으로 빠르며, 메모리의 할당과 해제를 사용자의 의도에 따라 세밀하게 조정할수 있으므로, 프로그래밍 자유도가 높고 최적화가 용이하다.

하지만 프로그래머가 실수할 경우 Memory Leak이 발생할 수 있다.

## References
[https://en.wikipedia.org/wiki/Managed_code](https://en.wikipedia.org/wiki/Managed_code)
[https://algorfati.tistory.com/m/113](https://algorfati.tistory.com/m/113)
[https://blog.seulgi.kim/2019/04/managed-language-vs-unmanaged-langauge.html](https://blog.seulgi.kim/2019/04/managed-language-vs-unmanaged-langauge.html)