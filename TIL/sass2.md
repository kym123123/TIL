# SASS 

# 2)SassScript

* SassScript는 CSS에서는 불가능한 연산, 변수, 함수 등의 확장 기능을 의미한다. 

## 1. data type

: 프로퍼티 값으로 사용할 수 있는 값에는 각각의 data type이 존재한다. SassScript는 7가지 데이터 타입을 제공한다.

1. 숫자형 (1, 2, 13, 10px)
2. 문자열 (Css는 2종류의 문자열을 사용할 수 있다. 따옴표 사용하는 경우와 사용하지 않는경우. Sass는 2종류의 문자열 모두를 인식할 수 있다.)
3. 컬러 (blue, #04a3f9, rgba(255, 0, 0, 0.5))
4. Boolean (true, false)
5. null(프로퍼티 값에 값이 null인 변수가 지정되면 해당 룰셋은 출력되자 않는다.)
6. List (margin과 padding 프로퍼티값 지정에 사용되는 0 auto와 font-family 프로퍼티값 지정에 사용되는 Helvetica, Arial, sans-serif 등은 공백 또는 콤마 구분된 값의 list이다.)
7. Map (JSON 과 유사한 방식으로 map-get 함수를 사용하여 원하는 값을 추출할 수 있다.)

```scss
$foundation-palette: (
	primary: #E44347,
  mars: #D7525C,
  saturn: #E4B884,
  neptune: #5147D7
);

.mars {
  color: map-get($foundation-palette, mars);
}
// => .mars {color: #D7525C;}
```

## 2. 변수

Sass 에서는 변수를 사용할 수 있다. 문자열, 숫자, 컬러 등을 변수에 저장하고 필요할때 불러 사용할 수 있다.

```scss
$site_max_width: 960px;
$font_color: #333;
$link_color: #00c;
$font_family: Arial, sans-serif;
$font_size: 16px;
$line_height: percentage(20px / $font_size);

body {
  color: $font_color;
  
  // property nesting
  font : {
    size: $font-size;
    family: $font_family;
  }
  
  line-height: $line_height;
}

#main {
  width: 100%;
  max-width: $site_max_width;
}
```

## 3. 변수의 SCOPE

* 변수에는 유효범위(scope)가 존재한다.

* 변수 $width는 top level에 기술되었으므로 전역변수가 된다. 전역변수는 전역은 물론 하위의 어떤 코드 블록 내에서도 유효하다.

* 코드 블록 내에서 선언된 변수는 지역변수가 된다. 지역변수의 유효범위는 자신이 속한 코드블록과 하위 코드 블록이다.

  ```scss
  $width: 960px; // global variable
  
  header {
    width: $width;
    margin: 0 auto;
  }
  
  #main {
    $color: #333; // local variable
    width: $width;
    margin: 20px auto;
    section {
      p {
        color: $color;
        
        a:link {
          color: $color;
        }
      }
    }
  }
  footer {
    width: $width;
    margin: 0 auto;
    color: $color; // main에서 선언한 변수 $color은 #main 코드블록 내에서만 유효
    // 따라서 코드를 컴파일하면 undefined variable: "$color" 에러가 발생한다.
  }
  ```

  * 코드 블록 내에서 선언한 지역변수를 전역변수화 하는 방법

    ```scss
    #main {
      $color: #333 !global; // global variable
      width: $width;
      	...
    }
    ```

## 4. 연산자(Operation)



### 4.1 숫자 연산자

* | operator | Descriptioin |
  | -------- | ------------ |
  | +        | 덧셈         |
  | -        | 뺄셈         |
  | *        | 곱셈         |
  | /        | 나눗셈       |
  | %        | 나머지       |
  | ==       | 동등         |
  | !=       | 부등         |

* ```scss
  $width: 100px;
  
  #foo {
    width: $width + 10; //110px
  }
  
  #bar {
    width: $width + 10in; // 1060px
  }
  ```

* 변수 $width의 값 100px 에 10 또는 10in 과 같이 다른 단위의 값을 연산하여도 에러없이 연산이 수행된다. 연산자의 왼쪽 값을 기준으로 단위가 설정된다.

  

* $width에 10em을 더하면?

  ```scss
  $width: 100px;
  
  #foo {
    width: $width + 10em; // NG: 100px + 10em
  }
  ```

  컴파일 결과 Incompatible units: 'em' and 'px'라는 에러를 출력한다.

  Sass 연산은 대상을 변환하여 연산할 수 없는경우 에러 출력.

  Sass 는 %, em, rem, vh, vw, vmin, vmax 같이 상대적인 값을 알지 못한다. 상대적인 값의 결과값은 브라우저만이 알 수 있다. **따라서 상대적인 값을 갖는 단위의 연산은 동일한 단위를 갖는 값과의 연산만이 유효하다.**

  ```scss
  #foo {
    width: 5% + 10% // 15% -> 유효
  }
  ```

  ```scss
  // css3의 calc함수를 사용하면 문제를 해결할 수 있다.
  #foo {
    width: calc(25% - 5px);
  }
  ```

  ```scss
  p {
    font: italic bold 12px/30px Georgia, serif;
    border-radius: 10px 20px/20px;
    
    $width: 1000px;
    
    width: $width / 2; // 변수에 대해 사용 -> width: 500px;
    height: (500px / 2); // 괄호 내에서 사용 -> height: 250px;
    margin-left: 5px + 8px / 2px; // 다른 연산의 일부로써 사용 -> margin-left: 9px;
  } // 변수를 css의 /와 함께 사용하려면 #{}(Interpolation)을 사용한다.
  
  p {
    $font-size: 12px;
    $line-height: 30px;
    font: #{$font-size} / #{$line-height}; // 12px / 30px
  }
  ```

### 4.2 컬러 연산자

* 모든 산술 연산자는 컬러 값에도 사용할 수 있다.

  ```scss
  p {
    color: #010203 + #040506;
    // R: 01 + 04 = 05
    // G: 02 + 05 = 07
    // B: 03 + 06 = 09
    // => #050709
  }
  p {
    color: #010203 * 2;
    // R: 01 * 2 = 02
    // G: 02 * 2 = 04
    // B: 03 * 2 = 06
    // => #020406
  }
  p {
    color: rgba(255, 0, 0, 0.75) + rgba(0, 255, 0, 0.75);
    // alpha 값은 연산되지 않는다.
    // color(255, 255, 0, 0.75);
  }
  ```

  * alpha 값은 연산되지 않는다. alpha 값의 연산을 위해서는 opacify 함수 또는 transparentize 함수를 사용한다.
  * opacify 함수: 첫 번째 argument의 alpha 값에 두번째 argument를 더해 불투명도를 증가시킨다.
  * Transparentize 함수: 첫 번째 argument의 alpha 값에 두번째 argument를 빼서 불투명도를 감소 시킨다.( 더 투명해진다.)

  ```scss
  $translucent-red: rgba(255, 0, 0, 0.5);
  
  p {
    color: opacify($translucent-red, 0.3); // alpha = 0.8
    background-color: transparentize($translucent-red, 0.25);
    // alpha = 0.25
  }
  ```

### 4.3 문자열 연산자

+ `+`연산자는 자바스크립트와 같이 문자열을 연결할 수 있다.

  ```scss
  p {
    cursor: e + -resize; // e-resize
  }
  ```

  ```scss
  p::before {
    content: "Foo" + Bar; // "Foo Bar"
    font-family: sans- + "serif"; // sans-serif
  }
  // 따옴표가 있는 문자열과 없는 문자열을 함께 사용하는 경우, 좌항의 문자열을 기준으로 따옴표를 처리한다.
  ```

### 4.4 불린 연산자

* | Operator | Description |
  | -------- | ----------- |
  | &&       | and         |
  | \|\|     | or          |
  | !        | not         |



### 4.5 리스트 연산자

리스트를 위한 별도의 연산자는 제공되지 않지만 리스트 함수를 사용하여 필요한 처리를 수행할 수 있다.

### 5. 함수

Sass Built-in Functions를 참조한다.

### 6. Interpolation: #{}

인터폴레이션은 변수의 값을 문자열 그대로 삽입한다. 인터폴레이션에 의해 삽입된 문자열은 연산의 대상으로 취급되지 않는다. 변수는 프로퍼티값으로만 사용할 수 있으나, #{}를 사용하면 프로퍼티 값은 물론 셀렉터와 프로퍼티명에도 사용할 수 있다.

```scss
$name: foo;
$attr: border;

p.#{$name} { // p.foo
  #{$attr}-color: blue; // border-color: blue;
}

.someclass {
  $font-size: 12px;
  $line-height: 30px;
  
  // 연산의 대상으로 취급되지 않도록
  font: #{$font-size} / #{$line-height}; // 12px / 30px
  }
```

### 7. Ampersand(&)

&는 부모요소를 참조하는 셀렉터

```scss
a {
  color: #ccc;
  &.home { //a.home
    color: #f0f;
  }
  &: hover { //a:hover
    text-decoration: none;
  }
  
  > span { // a > span
    color: blue;
  }
  
  span { // a span
    color: red;
  }
}
```

### 8. !default 

: !default flag는 할당되지 않은 변수의 초기값을 설정한다.

```scss
$content: null;
$content: "Non-null content" !default;

#main {
  content: $content; // "Non-null content"
}
```

```scss
$content: "First content";
$content: "Second content?" !default;
$new_content: "First time reference" !default;

#main {
  content: $content; // "First content" 이미 값이 할당되어 있는 변수에 !default flag	사용시 적용x
  new-content: $new_content; // "First time reference"
}
```

* 위의 특성은  partial에 유용하다.

* 2개의 파일 _font.scss와 main.scss를 생성. Main.scss은 내부에서 _font.scss를 import한다.

  ```scss
  // _font.scss
  $font-size: 16px !default;
  $line-height: 1.5 !default;
  $font-family: "Helvetica Neue", "Helvetica", "Arial", sans-serif !default;
  
  body {
    font: #{$font-size} / $line-height $font-family;
  }
  
  // main.scss
  $font-family : "Lucida Grande", "Lucida Sans Unicode", sans-serif;
  
  @import "font";
  
  
  // 컴파일결과 -> main.css
  body {
    font: 16px / 1.5 "Lucida Grande", "Lucida Sans Unicode", sans-serif;
  }
  // main.scss에서 변수에 값을 할당하였기 때문에 !default플래그 사용한 변수값이 무력화된 결과.
  ```

  * !default는 변수에 값이 할당되지 않았을 때 사용할 기본값을 지정할 때 사용한다.

