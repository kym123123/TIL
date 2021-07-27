# REST API

<u>Re</u>presentational <u>S</u>tate <u>T</u>ransfer

* REST 아키텍쳐 스타일을 따르는 API
* REST : 분산 하이퍼 미디어 시스템(e.g. 웹)을 위한 아키텍쳐 스타일(아키텍쳐 스타일 = 제약조건의 집합) 
* REST를 따른다는 것은 이 제약 조건을 모두 지켜야 한다는 것이다.

## REST를 구성하는 스타일

* Client-Server – 클라이언트-서버 스타일은 사용자 인터페이스에 대한 관심(concern)을 데이터 저장에 대한 관심으로부터 분리함으로써 클라이언트의 이식성과 서버의 규모확장성을 개선한다.
* Stateless – 클라이언트와 서버의 통신에는 상태가 없어야한다. 모든 요청은 필요한 모든 정보를 담고 있어야한다. 요청 하나만 봐도 바로 뭔지 알 수 있으므로 가시성이 개선되고, task 실패시 복원이 쉬우므로 신뢰성이 개선되며, 상태를 저장할 필요가 없으므로 규모확장성이 개선된다.
* Cache – 캐시가 가능해야한다. 즉 모든 서버 응답은 캐시 가능한지 그렇지 아닌지 알 수 있어야한다. 호율, 규모확장성, 사용자 입장에서의 성능이 개선된다.
* Uniform Interface – 구성요소(클라이언트, 서버 등) 사이의 인터페이스는 균일(uniform)해야한다. 인터페이스를 일반화함으로써, 전체 시스템 아키텍처가 단순해지고, 상호작용의 가시성이 개선되며, 구현과 서비스가 분리되므로 독립적인 진화가 가능해진다. 이 스타일은 다음의 네 제약조건으로 이루어진다: identification of resources, manipulation of resources through representation, self-descriptive messages, hypermedia as the engine of application state
* Layered System – 계층(hierarchical layers)으로 구성이 가능해야하며, 각 레이어에 속한 구성요소는 인접하지 않은 레이어의 구성요소를 볼 수 없어야한다.
* Code-On-Demand (Optional) – Code-On-Demand가 가능해야한다. 서버가 네트워크를 통해 클라이언트에 프로그램을 전달하면 그 프로그램이 클라이언트에서 실행될 수 있어야한다. (Java applet이나 Javascript 같은 것을 말함) 다만 이 제약조건은 필수는 아니다.

## Uniform Interface의 제약조건

* **identification of resources** : 리소스가 uri를 통해서 식별이 가능해야 한다.

* **manipulation of resources through representations** : representation 전송을 통해서 리소스 조작을 해야한다. (리소스 생성, 업데이트,삭제 등을 할 경우 http 메서드에 그러한 표현을 담아서 전송해야 한다.) 

* **self-descriptive messages** : 메시지가 스스로를 설명 가능해야 한다.

* ```http
  GET / HTTP/1.1 
  ```

  get 메서드를 통해서 데이터를 얻어오는 요청. 이 http 요청 메시지는 self-descriptive 하지 못하다. 목적지를 추가하면 self-descriptive 하다.

  ```http
  GET / HTTP/1.1
  HOST: www.example.org
  ```

   

* ```http
  HTTP/1.1 200 OK
  [ { "op": "remove", "path": "/a/b/c" } ]
  ```

  Self-descriptive하지 못하다. 클라이언트가 응답을 해석할 경우, 어떠한 문법으로 작성된 건지를 해석불가능. Content-Type 헤더가 필요하다. -> json형식의 문법을 해석이 가능해진다. -> 그러나 아직도 self-descriptive 하지 못하다. "op", "path"등이 무엇을 의미하는지 모른다.

* ```http
  HTTP/1.1 200 OK
  Content-Type: application/json
  
  [ { "op": "remove", "path": "/a/b/c" } ]
  ```
  
  이 메시지만 보고서 path, op등이 무엇을 의미하는지 알도록 하는 명세에대한 정보가 필요하다. json-patch+json이라고 적어주면, 이 명세를 찾아 갈수 있다. (json-patch에 대한 명세가 존재하므로 찾아서 확인하면 메시지 해석이 가능해진다.) -> self-descriptive 만족. 


* ```http
  HTTP/1.1 200 OK
  Content-Type: application/json-patch+json
  
  [ { "op": "remove", "path": "/a/b/c" } ]
  ```

* 오늘날의 restAPI는 대부분 이러한 점을 만족하지 않고 그냥 application/json이라고 적혀있기만 할 뿐이다.

* **Hypermedia as the engine of app state (HATEOAS)** : 애플리케이션의 상태는 hyperlink를 이용해서 전이되어야 한다. 하이퍼 링크를 통한 전이.

* ```http
  HTTP/1.1 200 OK
  Contenty-Type: text/html
  
  <html>
  <head></head>
  <body><a href="/test">test</a></body>
  </html>
  ```

  html 로 hateoas를 만족시킨경우. a태그를 통해서 하이퍼링크가 제공되고, 하이퍼링크를 통해서 다음 상태로 전이가 가능하므로 만족.

  ```http
  HTTP/1.1 200 OK
  Content-Type: application/json
  Link: </articles/1>; rel="previous",
  			</articles/3>; rel="next";
  
  {
  	"title": "the second article",
  	"contents": "blah blah..."
  }
  ```

  json을 통해서 hateoas를 만족시키기. Link헤더를 통해서 본 메시지와 연결된 하이퍼링크(다른 리소스를 가리키는)를 제공해주는 헤더이다. 한 게시물을 json으로 표현한 것인데, 이전게시물과 다음게시물의 uri정보가 표현되고, 이 정보는 Link헤더에 대한 표준 문서가 있으므로, 메시지를 보는사람이 해당 문서를 통해서 이해가 가능하고, 하이퍼 링크를 통해서 다른 상태로 전이가 가능하므로 hateoas를 만족한다.

### Uniform interface를 만족해야하는 이유? 

독립적 진화 :  서버와 클라이언트는 각각 독립적으로 진화한다. 서버의 기능이 변경되어도 클라이언트를 업데이트 할 필요가 없다.

REST를 만들게 된 계기 = How do i improve http without breaking the web.

### json 데이터를 이용한 api

json은 hyperlink가 정의되어 있지 않다. (html은 html 명세에 a태그가 정의되어 있다.) self-descriptive 측면에서 봤을때, html은 html명세에 모든 태그들과 그 의미가 적혀 있으므로 만족하지만, json은 키 밸류 값들의 의미가 적힌 명세가 없고 만들기도 불가능하다. 따라서 한 api에 대한 별도의 api문서가 필요하다.

### Representation

어떤 리소스의 특정 시점의 상태를 반영하고 있는 정보이다. Https://diveInto.com/classes라는 uri로 get요청을 해서 1,2,3라는 리소스를 응답으로 받았다고 자주 표현하지만, 이건 사실 틀린표현이다. 1,2,3은 리소스가 아니라 representation data이다. 

Get 메서드의 정의: target resource에 대한 현재 선택된 representation하나를 반환.

Target resource란 https://diveInto.com/classes이다(uri). 리소스란 무엇인가? http의 요청 대상이다. http는 리소스의 개념을 제한하지 않고 무엇이든 그 대상이 될 수 있다.(text, json,image, file...) 1,2,3이라는 text는 리소스가 아니라 classes에 대한 의미를 담은 문서가 리소스이다.

* 하나의 representation은 representation data와 representation meta data로 구성된다.
* representation data는 본문, representation metadata는 content-type 헤더와 같은 http 헤더들이 대부분 해당되며 그렇지 않은것들이 있다.

### rest에서의 representation

rest는 representational state transfer이다. state는 웹 앱의 상탱며, transfer는 이 상태의 전송이다.

웹페이지 a를 보던 사용자가 웹페이지 b로 가는 링크를 클릭하면, 브라우저는 웹페이지 b를 렌더링 해준다.

웹 앱이란 웹 브라우저와 웹 서버가 연결되어 사용자에게 서비스를 제공하는 앱이다. 따라서 브라우저와 서버가 연결되어 서비스를 제공해주어야지 웹 앱이 되는것이다. 두명의 사용자가 각각 자신의 웹 브라우저로 같은 웹 서버에 접속하면, 두개의 웹 앱이 실행되는 것이다.

링크를 클릭해서 a페이지가 b페이지로 바뀌었다. 웹 앱의 상태가 변경된 것이다. 이 상태의 변경은 representation의 전송(transfer)을 통해 이루어졌다. 따라서 representational state transfer이다.

1. transfer는 상태의 전이(transit)를 의미하지 않는다. 사용자가 링크를 클릭해서 웹 앱의 상태가 전이 되었지만, transfer가 의미하는 것은 그 전이가 아니라, network component사이의 전송이다. 즉 웹 서버에서 클라이언트로 웹 페이지 전송을 의미한다.
2. 리소스의 상태와 앱의 상태는 둘다 state라고 표현되지만, 본질적으로는 완전히 다르다. representation이란 어떤 리소스의 특정 시점의 상태를 반영한 정보이다. 이것은 리소스의 상태이지 앱의 상태가 아니다. 앱의 상태란 웹 앱의 페이지a를 렌더링하다가 b를 렌더링 하는 것으로 바뀐 그 상태이다.
3. http의 메시지의 payload로 전달되는 모든것은 하나의 representation이거나 적어도 그 일부다. put메서드를 이용해 welcome이란 텍스트를 전송해서 greeting 리소스의 representation을 업데이트 하면, 클라이언트가 서버로 전송한 'welcome'은 representation이다. 업데이트가 성공하여 서버가 업데이트 성공 이라는 메시지를 응답의 payload로 돌려보내면, 이 메시지 또한 representation이다. 권한없음 이라는 에러 메시지로 응답하면, 이 또한 representation이다.(모든 payload는 representation)
4. 성공, 에러시의 메시지도 representation이다. 그런데 representation은 어떤 리소스에 대한 상태를 담은 정보인데, 성공, 에러 메시지는 어떤 리소스에 대한 상태인가? greeting리소스는 welcome으로 업데이트 되었으므로 이 리소스의 상태가 아니다. 그렇다면 무엇인가? 이론적인 답은 'Content-location'헤더에 있는 uri가 가리키는 리소스이다. 보통 이 헤더는 비어있고, 현실적인 답은 uri를 모르는 어떤 리소스가 된다. 어떤 리소스에 대한 것이지만, 그 리소스가 가리키는 uri는 뭔지 모른다.



Api application programming interface: 한 소프트웨어가 다른 소프트웨어로부터 지정된 형식으로 요청 명령을 받을수 있는 수단.



==== 

1. Api: api는 어떠한 소프트웨어가 다른 소프트웨어로부터 정해진 형식으로 요청과 명령을 주고 받을 수 있는 수단이다.(application programming interface)

2. rest api란 ?? rest의 의미는 => rest는 representational state transfer의 약자이다. representation이란 어떤 리소스의 특정시점(요청 시점)의 상태를 반영하고 있는 정보 -> 리소스란? http의 요청 대상으로써 uri로 표현된다. uniform resource identifier로써, 자원을 식별할 수 있는 식별자이다. http에서는 자원에 대한 제한을 두지 않고 있기 때문에 어떠한 것이나 자원이 될수있다. Json, text, image....등등 .
   어쨌든 우리가 /user라는 uri로 요청을 보냈다고 한다면 응답으로 무언가가 온다. 예를들어 응답이 아래와 같이 왔다고 해보자

   ```http
   HTTP/1.1 200 OK
   Content-Type: 'application/json'
   
   {
   	"name": "kim",
   	"age": 29
   }
   ```

   우리는 요청에 대한 리소스로  {name,age}가 담긴 json을 응답받았다고 자주말한다. 이는 잘못된 표현이다.  저 http응답 문서 전체가 representation이다. representation은 1)representation data와 2)representation metadata로 구분된다. 1)은 json이고(http 본문), 2)는 content-type헤더와 같은 http 헤더가 대부분 포함된다.
