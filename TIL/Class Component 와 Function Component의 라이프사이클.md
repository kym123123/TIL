# Class Component 와 Function Component의 라이프사이클

## class component

* 클래스형 컴포넌트의 라이프사이클

1. constructor() : 컴포넌트가 마운트 되기 전에 호출된다. React.Component를 상속받아서 호출해야 하므로 다른 어떠한 코드보다 super(props)를 먼저 호출하고 작성해야 한다. constructor메서드는 필수 메서드가 아니고, state를 초기화하거나 클래스 컴포넌트 내부에 정의한 함수를 이벤트 핸들러로 메서드를 바인딩할때 사용한다. 그 과정이 없다면 생략이 가능한 메서드이다. this.state = value 형식으로 state를 할당가능한 유일한 메서드이다.
2. 