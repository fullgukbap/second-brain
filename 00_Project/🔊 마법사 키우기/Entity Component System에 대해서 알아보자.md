	### 왜 Entity Component System을 알려고 하는가?
현재 게임 개발 중 클라이언트와 서버를 통신하여 개발해야 하는 상황인데 캐릭터의 구성요소 정보를 서버와 클라이언트가 정확히 알고 있지 않아 서로서로 햇갈리는 상황이다. 그래서 저번에 어쩌다보니 이 개념을 마추쳐서 이 개념을 가지고 Entity Component System을 가지고 사용하면 위 문제를 해결할 수 있을 것 같아서 

아무튼 중요한 점은 정확히 게임의 구성요소를 정의하는 것이다.

Entity Component Sytem을 읽는 목적은 우리 게임의 구성요소를 정확히 명시하여 서버와 클라이언트의 통신을 원활하기 위함

### Entity Component System 이라는 놈은 뭘까?
##### Entity Component System의 정의 
객체지향프로그래밍에서 오브젝트가 모두 가지고 있던 데이터와 기능(함수)를 Entity, Component, System으로 분리한 것을 의미한다.

##### 그러면 이 개념이 나온 이유는
상속구조의 복잡한 결합구조를 깨버리고, 구조자체를 단순하게 만들어 유지보수와 새로운 기능을 개발하는데 도움을 준다. 
-> 즉 엄청난 유지보수성을 위해서 나온 개념 

### Entity Component System의 구성요소들
Component: 단순한 데이터들을 저장한다. 행위를 절대로 가지고 있지 않는다. 마치 struct라고 보면 된다.
Entity: Entity를 식별하기 위한 키와, Component들로 구성된다. 
System: Entity들이 필요로 하는 기능 중 반복적으로 처리되는 로직을 정의한다. 

3개의 구성요소들은 다음과 같이 상호작용 한다.
![[Entity Component System 도식화 예제.png]]