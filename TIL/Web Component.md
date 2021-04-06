# Web Component

컴포넌트의 기능을 나머지 코드로부터 캡슐화 하여, 재사용 가능한 커스텀 엘리먼트를 만들고 웹 앱에서 활용할 수 있도록 해주는 기술의 모음



## 1. 커스텀 엘리먼트

* 커스터마이징한 HTML 요소를 제작할 수 있다.

```javascript
customElements.define('word-count', WordCount, {extends: 'p'}); // 이 메서드의 반환값은 없다. void
// 이 메서드를 사용하여 커스텀 엘리먼트를 등록한다. 
// 커스텀 엘리먼트의 이름은 '-'가 포함되어야 한다. word-count
// 엘리먼트의 행위가 정의된 class를 두 번째 인자로 전달한다.
// 세 번째 인자로, options객체를 전달. element가 어떻게 정의되는지를 정한다. extends의 키값에 대한 value로 내장 html 요소의 이름을 문자열 형태로 전달한다.

class WordCount extends HTMLParagraphElement {
  constructor() {
    super();
    // super를 생성자에서 처음으로 호출한다. -> 이를 통해서 올바른 프로토타입 체인이 형성된다.
    
    // 엘리먼트의 기능을 작성한다.
  }
}
```

* 커스텀 엘리먼트의 두 가지 종류

  1. Autonomous custom elements: 스탠다드 HTML elements로부터 상속받지 않고 독립적이다. 이 커스텀 엘리먼트는 HTML엘리먼트처럼 페이지에서 사용한다. -> <popup-info> 또는 document.createElement('popup-info''); 의 방법으로 사용한다.

  2. customized built-in elements: 기본 HTML요소들로부터 상속받는다. 이 커스텀 엘리먼트를 만들려면, 어떠한 기본 HTML요소를 확장해서 사용하는 것인지 구체적으로 알려줘야 한다. 사용법은 기본 HTML 요소들을 사용하는것처럼 사용하되 조금더 구체화 하여 사용한다. (아래처럼 attribute를 통해서 구체화 한다.) 

     ```javascript
     <p is="word-cont"> // HTML에서 is 어트리뷰트 설정
     
     document.createElement('p', {is: 'word-count'}) // javascript에서 createElement의 두번째 인자로 is 어트리뷰트 설정
     ```

* built-in elements의 커스터마이징

새롭게 class를 만들고, built-in 요소들을 extends해서 자신만의 커스텀 요소를 만들 수 있다.

```javascript
class ExpandingList extends HTMLUListElement { // HTML의 <ul> 요소를 확장받아서 커스텀
  constructor() {
    super();
    
    // 수정할 내용 입력.
  }
}

// ExpandingList를 태그처럼 사용하기 위해 등록한다. define 메서드를 사용.

window.customElements.define('expanding-list', ExpandingList, {extends: 'ul'});
```



## 2. Shadow DOM

: Shadow DOM은 숨겨진 DOM tree를 실제 DOM tree에 추가할 수 있도록 해준다. 이러한 Shadow DOM tree의 시작 노드는 shadow root이며, 그 아래에는 개발자가 원하는 요소들을 일반 DOM tree를 다루듯이 추가시킬 수 있다.

Shadow DOM은 자립적인 컴포넌트로, 스타일과 마크업을 캡슐화 해주며, element.attachShadow({mode: 'open'}) 메서드로 생성한다. shadowRoot를 통해서 참조하고 interaction 할 수 있다.

1. Shadow host: 일반 DOM이고, 이 node에 shadow DOM 이 붙는다.
2. Shadow tree: Shadow DOM 내부의 DOM tree
3. Shadow boundary: Shadow DOM이 끝나고, 일반 DOM이 시작되는 장소
4. Shadow root: shadow tree의 root node (Shadow host(일반 DOM)와 Shadow root(Shadow DOM)가 붙는다.)

Shadow DOM은 우리가 일반 DOM을 다루듯이 다룰수 있다. (자식노드를 붙이거나, attribute를 설정하거나, element.style.xx를 사용하여 styling을 하거나 또는 shadow DOM 안에 <style>요소를 추가해서.. 등등)  차이점은, Shadow DOM 내부의 코드는 shadow DOM 바깥에 어떠한 영향도 줄 수 없다. 

Shadow DOM은 새로운 것이 아니고, 브라우저가 예전부터 사용하던 방법이다. <video>태그 하나로만 표현되는 비디오(컨트롤러 포함)를 보면, 여러가지 기능(정지/ 재생 버튼, 볼륨조절 버튼 등)이 포함되어 있지만, 우리가 볼 수 있는 태그는 video 하나이다. 



* 기본 사용법

shadow root를 Element.attachShadow()메서드를 사용하여 추가할 수 있다. 파라미터로 options 객체를 받는데, 한가지 option을 받는다. (mode: open 혹은 mode: closed)

```javascript
let shadow = elementRef.attachShadow({mode: 'open'});
let shadow = elementRef.attachShadow({mode: 'closed'});
```

mode: open을 전달한 경우, 자바스크립트를 통해서 해당 shadow DOM에 접근이 가능하다. 

```javascript
const $mainDiv = document.getElementById('main-div');
let shadow = $mainDiv.attachShadow({ mode: 'open' });
let myShadowDom = $mainDiv.shadowRoot; // #shadow-root (open)

let shadow = $mainDiv.attachShadow({ mode: 'closed' });
let myShadowDom = $mainDiv.shadowRoot; // null -> 접근 불가
```



* 가장 유용하게 shadow DOM을 사용하는 방법 :  생성자의 일부로써 custom element내부에 사용하는 것.

```javascript
// custom element의 constructor 내부에서
let shadow = this.attachShadow({mode: 'open'});
// this는 새로 생성되는 custom element의 인스턴스를 바인딩하게 된다.
// 이렇게 생성한 shadow DOM을 어떠한 요소에 붙이고나면, 일반 DOM API를 통해서 조작이 가능하다

const newPara = document.createElement('p');

shadow.appendChild(newPara);
```

```javascript
class PopUpInfo extends HTMLElement {
        constructor() {
          super();

          const $shadow = this.attachShadow({ mode: 'open' }); // shadow DOM 생성
          const $wrapper = document.createElement('span');
          const $icon = document.createElement('span');
          const $info = document.createElement('span');
          const text = this.getAttribute('text');

          $wrapper.setAttribute('class', 'wrapper');
          $icon.setAttribute('class', 'icon');
          $icon.setAttribute('tabindex', 0);
          $info.setAttribute('class', 'info');
          $info.textContent = text;

          let imgUrl;

          if (this.hasAttribute('img')) {
            imgUrl = this.getAttribute('img');
          } else {
            imgUrl = 'img/default.png';
          }

          const $img = document.createElement('img');
          $img.src = imgUrl;
          $icon.appendChild($img);

          //styling the shadow DOM
          // css 생성
          let $style = document.createElement('style');

          $style.textContent = `
            .wrapper {
              position: relative;
            }

            .info {
              font-size: 0.8rem;
              width: 200px;
              display: inline-block;
              border: 1px solid black;
              padding: 10px;
              background: white;
              border-radius: 10px;
              opacity: 0;
              transition: 0.6s all;
              position: absolute;
              bottom: 20px;
              left: 10px;
              z-index: 3;
            }

            img {
              width: 1.2rem;
            }

            .icon:hover + .info, .icon:focus + .info {
              opacity: 1;
            }
          `;

          $shadow.appendChild($style);
          $shadow.appendChild($wrapper);
          $wrapper.appendChild($icon);
          $wrapper.appendChild($info);
        }
      }
```

위의 코드는 스타일을 지정해주는 문자열 코드가 너무 길어서 보기 좋지 않을 수 있다. 이를 대신해서 <link>태그를 사용하여 shadow DOM 의 스타일링을 할 수 있다.

```javascript
// constructor 안에서 사용
const $linkTag = document.createElement('link');
$linkTag.setAttribute('rel', 'stylesheet');
$linkTag.setAttribute('href', 'style.css');

$shadow.appendChild($linkTag); //shadow DOM에 생성한 link태그 추가
```



## 3. custom element의 라이프사이클 callbacks

요소의 라이프사이클 내의 다른 지점에서 호출되는 callbacks를 이용할 수 있다.

1. constructor():  커스텀 엘리먼트가 처음 생성될때 그리고 업그레이드 될 때, 가장 처음으로 실행되는 생성자함수. 이 안에서 shadowDOM을 요소에 추가하고, 다른 요소들을 shadowDOM에 추가하는 작업등을 수행.
2. connectedCallback(): 커스텀 엘리먼트가 처음으로 DOM에 연결될 때 호출되는 콜백
3. disconnectedCallback(): 커스텀 엘리먼트가 DOM에서 제거될 때 호출되는 콜백
4. adoptedCallback():  커스텀 엘리먼트가 다른 새로운 document로 이동될 때 호출되는 콜백
5. attribuiteChangedCallback(attribuiteName, oldValue, newValue): 커스텀 엘리먼트의 attribute가 추가/제거 혹은 바뀔때 호출되는 콜백



## 4. HTML <template>

: 캡슐화된 웹 컴포넌트 마크업을 정의하고, 페이지의 마크업을 저장할 수 있다. HTML과 css모두를 포함할 수 있다. custom text를 추가하려면 slots을 사용한다.