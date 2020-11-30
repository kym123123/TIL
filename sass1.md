# sass 

# 1) Basic

* scss 파일을 컴파일하여 css 파일을 생성할 때, 4가지 스타일 중 하나를 선택 할 수 있다.

  1. Nested : sass형식과 유사하게 nested된 css파일이 생성된다. 기본값으로 옵션을 추가하지 않아도 기본 적용된다.

     ```bash
     $ node-sass --output-style nested src/sass --output dist/css
     ```

     

  2. Expanded : 표준적인 스타일의 css 파일이 생성된다.

     ```bash
     $ node-sass --output-style expanded src/sass --output dist/css
     ```

  3. Compact : 여러 룰셋을 한줄로 나타내는 스타일의 css 파일이 생성된다.

     ```bash
     $ node-sass --output-style compact src/sass --output dist/css
     ```

  4. Compreessed : 가능한 빈공간이 없는 압축된 스타일의 css 파일이 생성된다.

     ```bash
     $ node-sass --output-style compressed src/sass --output dist/css
     ```

* Watch :  watch command는 scss 파일의 변경을 감지하여 변경될 때 마다, scss파일을 컴파일하여 css 파일을 자동 업데이트 한다. (디렉토리 단위, 파일 단위의 모니터링이 가능하다)

  ```bash
  $ node-sass --watch src/sass/foo.scss --output dist/css
  # 파일 단위의 watch
  ```

  ```bash
  $ node-sass --watch src/sass --output dist/css
  # 디렉토리 단위의 watch
  ```

  

* Sass는 SASS 표기법과 SCSS 표기법이 있다. SASS표기법이 기본 표기법 이었지만, Sass 3.0 부터 css친화적인 SCSS표기법이 기본 표기법이 되었다.

