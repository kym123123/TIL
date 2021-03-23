Transitioned 이벤트

css의 transition 속성에 의해서 transition이 완료되면 발생하는 이벤트

transition이 완료되기전에 제거되거나(transition-property가 제거되거나 display:none이 되는경우..) 이벤트는 발생되지 않음.

버블링 발생 . addEventListener('transitionend')

또는 element.ontransitionend

transitionend 이벤트는 양방향으로 모두발생한다.

1. 트랜지션된 상태로 트랜지션이 종료될때.
2. 트랜지션이 되지않은 상태로 완전히 돌아갈떄

만약, 트랜지션 딜레이나 duration이 없으면(0s이거나 선언되지 않았거나) 이벤트 발생하지 않는다.





transitionstart: css transition이 실제적으로 시작할때 발생(transition-delay가 끝난후)

transitioncancel: css transition이 취소될때 발생하는 이벤트

transition이 취소되는 경우 -> 1. transition-property 가 바뀔때

2. display:none이 될때 3. transition이 완료되기전에 멈출때 (hover가 진행중인데 마우스 커서를 요소 밖으로 움직일때)

transitionend:  transition이 완료되면 발생하는 이벤트

transitionrun : transition 이 처음 발생할때 동작하는 event (transition-delay 발생전에)







* css 변수
* https://developer.mozilla.org/ko/docs/Web/CSS/Using_CSS_custom_properties

* animationend : css의 animation이 active상태의 끝에 도달하면 발생한다. active상태= animation-duration * animation-iteration-count + animation-delay

* animationstart : css animation이 시작되면 발생, animation-delay 프로퍼티가 설정되어 있으면, 이 프로퍼티가 종료된 후에 이벤트가 발생한다. 