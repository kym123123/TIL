# SSR & CSR

## SSR

1. 서버는 렌더될 준비가 된 HTML 파일을 응답으로 브라우저에 전송한다.
2. 브라우저는 응답받은 HTML 파일을 브라우저에 바로 렌더링한다. 사용자는 HTML파일을 볼수 있게 된다.
3. 브라우저는 JS 파일을 다운받는다.
4. 브라우저는 다운받은 js를 실행하여 react 코드를 동작시킨다.
5. react 코드가 동작되면서 사용자는 인터랙티브 요소를 사용할 수 있게 된다.



## CSR

1. 브라우저의 응답에 따라서, 서버는 응답을 전송한다. 이 응답은 비어있는 HTML파일이다.
2. html 파일에 연결된 js를 다운받는다.
3. 브라우저가 js파일을 실행하여 react 코드를 동작시킨다.
4. react가 동작하여 비어있는 html 파일을 렌더링하고, 인터랙티브 요소가 사용 가능해 진다.



SSR은 서버가 보내준 html파일이 이미 렌더링될 준비가 완료된 파일이라는 것이다. 반면에 csr은 브라우저가 비어있는 document(js 파일 경로가 포함되어 있음)을 응답으로 받게된다. 

SSR방식의 의미는 브라우저가 응답받은 html파일을 보여주기위해 기다릴 필요가 없고(이미 렌더링될 준비가 완료된 상태이기 때문이다.)  이로 인하여 사용자가 뷰를 보기위해 js가 실행되고 동작하는 것을 기다릴 필요가 없는 것이다. 

두 경우 모두 리액트가 다운받아지고 실행되어야 하는것은 맞지만, ssr에서는 리액트가 가상돔을 만들고, 이벤트 핸들러를 추가하는 과정에서도 사용자가 view는 볼 수 있다는 것이고, csr은 리액트가 가상돔을 만들고, document에 추가하고, 핸들러를 추가하는 과정의 끝에 view가 렌더링 되기 때문에 그동안 비어있는 페이지를 봐야 한다. (물론 ssr이 view를 즉시 보여주지만, 리액트가 실행되는 과정중에 재빨리 버튼을 클릭한다든지, 인터렉티브 요소를 이용하려 하면 반응이 없다. 매우 짧은 시간동안 이지만...)

추가로 csr에서는 화면 깜빡임 현상이 발생한다. 최근에는 loading icon등을 활용하여 데이터를 로드하고 리액트가 동작하는 동안에는 대체화면을 보여주지만, 원래는 사용자가 비어있는 화면을 지켜보며 기다려아 하는 것이다. ssr은 그러한 화면 깜빡인 현상이 없이 즉시 렌더링이 된다. 

> 그 외의 특이사항
>
> * 페이지가 렌더링 되는 동안, 사용자는 view를 보고 interactive 요소와 이벤트를 이용하려 하지만 반응이 없다. 리액트가 전부 실행 완료 되고나서 동작이 되기 때문이다. 단순히 먼저 보여주기만 할 뿐이다.
> * ssr은 csr에 비하여 TTFB(Time To First Byte)이 느리다. 왜냐하면 서버가 csr에서는 단순히 response만 전송하지만(비어있는 html...) ssr에서는 렌더링을 해서 보내기 때문에 시간도 더 걸리고 보내야 하는 html의 크기도 더 크다.



### 렌더링

* SSR : server-side rendering : 클라이언트 측 앱을 서버의 HTML로 렌더링
* CSR: client-side rendering: 브라우저에서 애플리케이션을 렌더링한다. 일반적으로 DOM을 사용
* Rehydration: 클라이언트가 서버에서 렌더링 한 HTML의 DOM트리와 데이터를 재사용하도록 js 뷰를 부팅한다.
* Prerendering: 빌드 타임에 클라이언트측 앱을 실행하기 위해 초기 상태를 정적 HTML로 캡처.

### 성능

* TTFB: Time To FIrst Byte : 링크를 클릭후, 처음으로 들어오는 콘텐츠 byte 사이의 시간
* FB: First Paint: 픽셀이 처음 사용자에게 표시되는 시점
* FCP: First Contentful Paint: 요청 콘텐츠(기사, 본문)이 표시되는 시점
* TTI: Time To Interactive: 페이지가 상호작용 가능하게 될 때까지 시간 (이벤트 발생 등..)



* 정적 렌더링: 정적렌더링은 빌드 타임에 발생, ssr과 달리 페이지의 html을 즉석해서 생성할 필요가 없음, 따라서 일관성 있게 빠른 TTFB가 가능하다. 정적 렌더링은 미리 각 URL에 대하여 별도의 HTML파일을 생성하고 전송시켜주는 것을 의미한다. 

  정적 렌더링의 단점으로는 가능한 모든 url에 대하여 개별 html 파일을 생성해야 한다는 것이다. 미리 url을 예측 할 수 없거나, 독창적인 페이지가 많을 경우는 실행이 불가능 할 수도 있다.

  정적렌더링과 사전렌더링의 차이점의 이해가 중요. => 정적 렌더링 페이지는 클라이언트 사이드의 많은 js를 실행하지 않고 상호작용이 가능하지만, 사전 렌더링은 spa의 first paint나 ifrst contentful paint를 향상시켜도 페이지의 인터렉션이 가능하려면, 클라이언트에서 부팅 과정이 필요하다.

  js를 비활성화 하고 생성된 웹 페이지 불러오기 했을 경우 인터렉티브가 동작한다면 -> 정적렌더링,

  간단한 링크같은 기능은 동작하지만 대부분의 인터렉티브 동작x -> 사전렌더링

* 서버 렌더링은 동적으로 계산하는 특성이 있어서 계산 오버헤드가 발생할 수 있다. TTFB를 지연시키거나 전송되는 데이터가 늘어날 수 있다. react에서 renderToString()은 동기로 작용하는 단일 스레드이므로, 이 작업이 완료되기 까지 다른 작업들이 블록되는 현상이 발생하여 느려질 수 있다. 서버 렌더링을 올바르게 하기 위해서는 컴포넌트 캐싱, 메모리 관리, 메모기법등 여러 솔루션이 필요할 수 있다. 

  서버 렌더링은 각 URL마다 적합한 html을 생성하지만, 정적 렌더링된 콘텐츠 제공보다는 느릴 수 있다. 서버 렌더링의 장점은 정적렌더링에서 가능한 것보다 더 많은 실시간 데이터를 가져와서 응답이 가능하다는 것이다. 

* 클라이언트 렌더링은 js를 사용하여 브라우저에서 직접 페이지를 렌더링 하는 것이다. 모든 로직, 데이터, 템플릿, 라우팅은 클라이언트에서 처리된다. 단점으로는 app이 커짐에 따라서 js의 양이 증가하고, js라이브러리, 폴리필 등 서드파티 라이브러리를 추가하면 페이지를 렌더링 하기 전에 이것들을 먼저 처리해야 하는 경우가 있다는 것이다. 따라서 대규모 app을 bundle에 의존하는 csr 구축은 적극적인 코드 스플리팅이 권장되며, js중에 필요한 것만 필요한 때에 제공해야 한다. 

* rehydration을 통한 서버렌더링과 csr의 결합

  유니버설 렌더링 또는 ssr이라고 하는 이 접근 방식은 클라이언트 측 렌더링과 서버 렌더링의 절충안이다. 전체 page 로드, 리로드 같은 네비게이션 요청은 app을 html로 렌더링하는 서버에서 처리 -> 렌더링에 사용된 js와 데이터가 문서에 포함된다. (이 js와 데이터는 이후에 클라이언트에서 로드하여 실행시켜서 완전한 인터렉티브 앱이 동작하도록 된다.) 이 방식은 빠른 first paint를 제공하지만, time to interactive는 클라이언트에서 js를 로드하고 실행하는 과정이 완료되어야 인터렉티브 동작이 수행될 수 있으므로 부정적인 영향을 줄 수 있다. 이벤트 핸들러 등이 응답이 불가능한 시간은 모바일에서 몇분까지 걸릴수도 있다. -> 클릭했는데 아무것도 안돼!

* rehydration의 또다른 문제: 하나의 app, 두 배의 cost

  서버에서 html을 렌더링 하는데 사용했던 데이터를 클라이언트에서 다시 요청하지 않고 js가 사용할 수 있도록 하기 위해서 ssr은 ui 응답을 직렬화 한다. (데이터를 스크립트 태그로 문서에 종속시킨다.) 따라서 html 문서에는 복제가 포함된다.

  ```html
  // 복제된 데이터가 script 태그로 삽입된다.
  <script>
  var DATA = {'todos': [
    {'text': 'wash dishes', 'checked': false, "id": 'kjkjd232ldjs'}, 
    // .... data들....
  ]}
  </script>
  
  <script src="./bundle.js"></script>
  ```



### SEO에 대한 영향

크롤러가 쉽게 해석가능 하도록 서버렌더링이 선택된다.
