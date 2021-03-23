# Sass 

# 3) Nesting, import, extend, 조건과 반복, Mixin, Function

## 1. Nesting

: Nesting은 Sass의 유용한 확장 기능으로, 선언을 중첩 하는 것이다.

CSS는 후손 셀렉터의 경우, 부모요소를 기술해야 한다.

```css
#navbar {
  width: 80%;
  height: 23px;
}

#navbar ul {
  list-style-type : none;
}
// ul은 부모요소 #navbar를 기술한다.
```

Sass의 Nesting은 후손 셀렉터를 간단히 기술이 가능하다. 또한 HTML의 구조를 반영한 css를 기술할 수 있다. 그렇지만 너무 깊은 Nesting은 가독성을 나쁘게 하고 셀렉터를 복잡하게 한다.

```scss
#navbar {
  width: 80%;
  height: 23px;
  
  ul { list-style-type: none; }
  
  li {
    float: left;
    a { font-weight: bold; }
  }
}
```

부모요소의 참조가 필요한 경우 `&`를 사용한다. :hover또는 ::before등의 가상 클래스 선택자를 지정하는 경우 부모요소의 참조가 필요하다.

```scss
.myAnchor {
  color: blue;
  
  // .myAnchor: hover
  &:hover {
    text-decoration: underline;
  }
  
  // .myAnchor: visited
  &:visited {
    color: purple;
  }
}
```

Nesting은 프로퍼티에도 사용할 수 있다.

```scss
.funky {
  font: {
    family: fantasy;
    size: 30em;
    weight: bold;
  }
}
```



## 2. @-Rules and Directives

: 기능에 따라 css 파일을 분리하면 재사용 및 유지보수 측면에서 유리하다. 따라서 룰을 정하여 파일을 분리하여 개발하는 것은 효과적인 방법이다.

Sass는 @import directive를 사용하여 분리된 stylesheet 파일을 import 할 수 있다. 기존의 css @import 보다 편리한 기능을 제공한다.

```scss
@import "foo.scss";
@import "foo" // 확장자는 생략 가능하다
@import "rounded-corners", "text-shadow"; // import multiple files
$family: unquote("Droid+sans");
@import url("http://fonts.googleapis.com/css?family=#{$family}");
```

여러개의 파일로 분할하는 것 또는 분할된 파일 => `partial`

partial된 Sass파일명의 선두에는 underscore(_)를 붙인다. _reset.scss, _module.scss, _print.scss

예를들어 "_foo.scss"라는 partial된 Sass파일이 있고, 이 파일을 import 하는 경우, 아래와 같이 기술한다. 파일명의 선두의 _와 확장자는 생략가능

```scss
@import "foo";
```

partial된 Sass 파일명 선두에 붙인 _의 의미는 import 는 수행하되, CSS로의 컴파일은 수행하지 말라는 의미를 갖는다. partial은 import시에는 css 파일로 컴파일 되지 않기 때문에 최종적으로 css로 컴파일을 수행할 sass파일에서 import 한다. -> Sass 파일명 선두에 _를 붙이면 import 시에는 css파일로 컴파일 되지 않고, import가 완료된 이후 css로 컴파일을 수행한다.

* @import는 top-level에서 사용하는 것이 일반적이지만, css rule 또는 @media rule 내에 포함시키는 것도 가능하다.

* ```scss
  // _example.scss
  .example {
    color: red;
  }
  ```

* ```scss
  #main {
    @import "example";
  }
  // main안에서 import 수행 => nested된다.
  ```

* 위 코드의 컴파일 결과

  ```scss
  #main .example {
    color: red;
  }
  ```

### 2.2 @extend

: 기존 스타일을 상속하고자 하는경우 사용한다. 상속되는 rule set을 그대로 상속받아 다른 부분만 별도 선언하면 된다.

```scss
.error {
  border: 1px #f00;
  background-color: blue;
}
.seriousError {
  @extend .error;
  
  border-width: 3px;
  border-color: darkblue;
}
```

위 코드의 컴파일 결과 (.error와 .seriousError가 공통으로 사용하는 프로퍼티를 묶어 합리적 룰셋 생성)

```css
.error, .seriousError {
  border: 1px #f00;
  background-color: blue;
}
.seriousError {
  border-width: 3px;
  border-color: darkblue;
}
```

* @extend를 사용하면 컴파일 후 자신의 셀렉터가 어디에 첨부될 것인지 예상하기 어렵고, 예상치 못한 부작용이 발생할 수 있다. -> @extend의 사용은 가급적 자제하고 Mixin을 사용하자. (@extend의 부작용)

### 2.3 Placeholder Selectors

: Sass 3.2부터 제공되는 기능. 재이용 가능한 rule set을 % 키워드로 지정하는 @extend 전용 selector이다.

상속만을 위한 rule set이므로 자신은 컴파일 되지 않는다.

```scss
%input-style {
  font-size: 14px;
}

.input-black {
  @extend %input-style;
  
  color: black;
}

.input-red {
  @extend %input-style;
  
  color: red;
}
```

컴파일 결과는 아래와 같다.

```css
.input-black, .input-red {
  font-size: 14px
}
.input-black {
  color: black;
}
.input-red {
  color: red;
}
```

## 3. 조건과 반복

: sass는 js 같은 프로그래밍 언어와 같이 제어문 기능을 제공한다.

### 3.1 if()

: built-in if()함수는 주어진 조건을 판단하여 결과를 리턴한다. Javascript의 삼항연산자와 유사하게 동작

```scss
if(condition, if_true, if_false)
// condition이 true 이면, if_true를
// condition이 false 이면, if_false를 반환한다.
```

```scss
$type: ocean;

p {
  color: if($type == ocean, blue, black); // color: blue;
}
```

### 3.2 @if

: @if를 사용하면 조건 분기가 가능하다.

```scss
$type: monster;

p {
  @if $type == ocean {
    color: blue;
  } @else if $type == matador {
    color: red;
  } @else if $type == monster {
    color: green;
  } @else {
    color: black;
  }
}
```

컴파일 결과

```css
p {
  color: green;
}
```

### 3.3 @for

: @for로 반복문을 사용할 수 있다.

```scss
@for $i from 1 through 3 {
  .item-#{$i} { width: 2em * $i; }
}// .item-1, .item-2, .item-3...생성
```

```css
/* 컴파일 결과 */
.item-1 {
  width: 2em;
}
.item-2 {
  width: 4em;
}
.item-3 {
  width: 6em;
}
```

### 3.4 @each

: @each는 list또는 map의 요소에 대해 반복을 실시한다.

```scss
@each $animal in puma, sea-slug, egret, salamander { // List
  
  .#{$animal}-icon {
    background-image: url('/images/#{$animal}.png');
	}
}

// Map
// $header: h1, $size: 2em
// $header: h2, $size: 1.5em
// $header: h3, $size: 1.2em
@each $header, $size in (h1: 2em, h2: 1.5em, h3: 1.2em) {
  #{$header} {
    font-size: $size;
  }
}
```

```css
.puma-icon {
  background-image: url("/images/puma.png");
}

.sea-slug-icon {
  background-image: url("/images/sea-slug.png");
}

.egret-icon {
  background-image: url("/images/egret.png");
}

.salamander-icon {
  background-image: url("/images/salamander.png");
}

h1 {
  font-size: 2em;
}

h2 {
  font-size: 1.5em;
}

h3 {
  font-size: 1.2em;
}
```

### 3.5 @while

: 반복문을 사용할 수 있다.

```scss
$i: 6;
@while $i > 0 {
  .item-#{$i} { width: 2em * $i; }
  $i: $i - 2;
}
```

컴파일  결과

```css
.item-6 {
  width: 12em;
}
.item-4 {
  width: 8em;
}
.item-2 {
  width: 4em;
}
```



## 4. Mixin

: Mixin 은 Sass의 매우 유용한 기능으로 중복 기술을 방지하기 위해 사용 빈도가 높은 마크업을 사전에 정의하여 필요할 때에 불러 사용하는 방법이다.

@exten와 유사하지만, 프로그래밍 언어의 함수와 같이 argument를 받을 수 있다.

`@mixin`을 선언하고 `@include`로 불러들인다.

```scss
// 지름이 50px인 원
@mixin circle {
  width: 50px;
  height: 50px;
  border-radius: 50%;
}

// 지름이 50px인 원을 위한 mixin을 include한 후, 배경을 추가 지정
.box {
  @include circle;
  
  background: #foo;
}
```

```css
/* 컴파일 결과 */
.box {
  width: 50px;
  height: 50px;
  border-radius: 50%;
  background: #f00;
}
```

* @extend와 달리 Mixin은 함수처럼 argument를 사용할 수 있다.

  ```scss
  @mixin circle($size) {
    width: $size;
    height: $size;
    border-radius: 50%;
  }
  
  .box {
    @include circle(100px);
    
    background: #f00;
  }
  // 컴파일 결과
  // .box {
  // 		width: 100px;
  //		height: 100px;
  //		border-radius: 50%;
  //		background: #f00;
  // }
  ```

  ```scss
  // argument의 초기값을 설정할 수도 있다.
  @mixin circle($size: 10px) {
    width: $size;
    height: $size;
    border-radius: 50%;
  }
  
  .box {
    //인자가 없으면 초기값을 사용한다.
    @include circle();
    background: #f00;
  }
  ```

  ```css
  /* 컴파일 결과 */
  .box {
    width: 10px;
    height: 10px;
    border-radius: 50%;
    background: #f00;
  }
  ```

  ```scss
  /* Mixin을 사용한 유용한 예제 */
  @mixin vendorPrefix($property, $value) {
    @each $prefix in -webkit-, -moz-, -ms-, -o-, '' {
      #{$prefix}#{$property}: $value;
    }
  }
  .border_radius {
    @include vendorPrefix(transition, 0.5s);
  }
  ```

  ```css
  .border_radius {
    -webkit-transition: 0.5s;
    -moz-transition: 0.5s;
    -ms-transition: 0.5s;
    -o-transition: 0.5s;
    transition: 0.5s;
  }
  ```

  ```scss
  @mixin position($position, $top: null, $right: null, $bottom: null, $left: null) {
    position: $position;
    top: $top;
    right: $right;
    bottom: $bottom;
    left: $left;
  }
  .box {
    @include position (absolute, $top: 10px, $left: 50%);
  }
  ```

  ```css
  .box {
    position: absolute;
    top: 10px;
    left: 50%;
  }
  ```

  ## 5. Function

  : Function은 mixin과 유사하나 리턴값에 차이가 있다.

  * Mixing: style markup을 리턴
  * Function: @return directive를 통해 값을 리턴

  ```scss
  $grid-width: 40px;
  $gutter-width: 10px;
  
  @function grid-width($n) {
    @return $n * $grid-width + ($n - 1) * $gutter-width;
  }
  #sidebar { width: grid-width(5); } // width: 240px;
  ```

  ## 6. comment(주석)

  : ` css는 멀티라인 주석 /* */만 지원하지만, sass는 /* */와 // 모두 사용가능 하다.`

  한줄 주석 //은 컴파일 후  css에서 사라지고, 멀티라인 주석은  css에 나타난다.





















