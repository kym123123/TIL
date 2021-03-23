# HTTP cookie

: http cookie(웹 쿠키, 브라우저 쿠키) 서버가 사용자의 웹 브라우저에 전송하는 데이터 조각. 

브라우저는 같은 서버에 재 요청을 할 시에 이 데이터를 같이 담아 전송한다. http 프로토콜은 상태가 없기 때문에 cookie를 이용하여 상태정보를 기억시켜준다. (사용자의 로그인 상태를 유지 하도록 해준다.)



## cookie의 목적

1. 세션 관리(session management) : 브라우저의 저장소는 누구나 접근하고 변조 할 수 있기 때문에, 그렇게 하면 안되는 중요한 정보들을 서버에서 직접 관리하도록 할 경우 이용하는 것. 서버에 저장해야 할 로그인, 장바구니 목록, 게임에서 개인 전적 등의 정보 관리
2. 개인화(Personalization) : 사용자의 선호에 관련된 정보를 저장. Ex: 다크모드, 테마 세팅
3. 트래킹(Tracking) : 사용자의 행동을 기록하고 분석한다.



## local stroage, session storage, cookies

| -                     | cookies                                     | local storage                               | session storage                                |
| --------------------- | ------------------------------------------- | ------------------------------------------- | ---------------------------------------------- |
| 용량                  | 4KB                                         | 10MB                                        | 5MB                                            |
| 브라우저              | HTML4 / HTML5                               | HTML5                                       | HTML5                                          |
| 접근 가능 경로        | 동일 브라우저의 모든 window로 부터 접근가증 | 동일 브라우저의 모든 window로 부터 접근가능 | 같은 tab(브라우저 context)으로 부터만 접근가능 |
| 만료                  | 수동으로 만료기간 설정                      | 만료 x (명시적으로 지워야 함)               | 탭을 닫을시 자동으로 삭제 됨                   |
| 저장 위치             | 브라우저, 서버                              | only 브라우저                               | only 브라우저                                  |
| 요청과 함께 전송 여부 | yes (request header에 담겨 전송됨)          | no                                          | No                                             |

* 공통점: 유저가 사용하는 브라우저에 저장된다. edge에 저장된 정보를 같은 컴퓨터의 chrome이나 firefox에서는 확인할 수 없다. 

쿠키나 스토리지 간의 정보를 서로 공유 할 수 없다.

브라우저 사용자가 자유롭게 접근 할 수 있고 수정/삭제 할 수 있기 때문에 중요한 정보를 저장하는 것은 지양한다.



* 차이점: 쿠키는 세션스토리지/로컬스토리지와는 꽤 다르고 더 오래되었다.

쿠키는 훨씬 더 적은 양의 정보를 저장할 수 있다. 쿠키는 더 오래된 브라우저에서도 호환이 된다.(HTML4)

만료에 있어서는 스토리지와 쿠키가 차이점이 크다. 특히 로컬스토리지는 유저가 지우지 않으면 영원히 지속된다. 세션스토리지는 탭을 닫을 때 삭제된다.  쿠키는 만료기간을 설정해주어야 한다. (1년 뒤 만료되도록 혹은, 더 오랜시간뒤에 만료하도록 할 수 있고 짧은 시간 프레임마다 만료되도록 할 수도 있다.) 쿠키는 브라우저에 저장되어 있는 동안은 유저가 서버로 리퀘스트를 보낼 때 마다 (요청 파일의 종류에 관계없이... html, css, image 모두 해당 됨) 요청과 함께 전송된다. 따라서 쿠키는 용량이 더 작은것이다. 용량이 스토리지 만큼 크다면 요청 속도와 서버로 부터 오는 응답을 다시 받기까지의 시간을 길게 만들 것 이다. 따라서 쿠키는 작게 만들수록 좋다. 

쿠키는 authentication 과 관련된 일을 수행하기도 한다. 로컬/세션 스토리지와는 다르게 브라우저에서 서버로 전송 되는 특성이 있기 때문이다.

위의 특성을 제외하고는 브라우저에 무언가를 저장하도록 하고싶다면 만료 특징을 고려하여 로컬이나 세션스토리지를 사용하면 된다.

무언가를 서버에 전송하고 싶을때만 쿠키를 사용하자.



## actual usage of cookies and storages

```javascript
// sessionStorage와 localStorage는 메서드의 사용법과 이름이 같다.
localStorage.setItem('name', 'min'); // localStorage에 name프로퍼티키와 min 프로퍼티 value설정 저장
localStorage.getItem('name'); // name프로퍼티 키를 가진 값을 가져온다. min 반환
localStorage.clear(); // localStorage에 저장된 모든 값을 삭제시켜준다.
```

주의할 점은, localStorage.setItem으로 저장한 모든 값들은 getItem으로 반환받을 시 string으로 반환된다. 불리언 값이나 숫자값을 저장했다가 다시 가져 올 경우 타입에 주의하자.

```javascript
// cookies는 메서드가 좀 덜 다듬어진 형태이다.
document.cookie = 'name=min'; // 이렇게 설정하면 name(프로퍼티key): min(프로퍼티 value)형태로 저장된다.
```

브라우저의 application 탭의 cookie를 찾아보면 name: min으로 저장된 값이 확인이 된다.  Path, expires, domain등의 값도 확인이 된다.

* path 기본값은 '/', path혹은 그 하위 path에서만 쿠키 정보를 볼 수 있다.

* 쿠키를 생성할 때 Expires값을 설정해주지 않으면 브라우저가 닫힐때 쿠키가 만료된다.

* domain은 해당 쿠키가 유효한 domain값이 저장 됨. 

* secure 옵션이 지정되지 않으면, https://site.com에서 생성한 쿠키를 http://site.com에서도 확인할 수 있다. 쿠키에 민감한 정보가 있다면 secure 옵션을 지정하자.

  ```javascript
  // 만료일 설정방법. 만료일에는 날짜를 UTC string형태로 전달해야 한다. 따라서 javascript의 Date객체를 이용하여 전달한다. 
  // 2021년 12월 31일 만료되도록 설정한 코드
  document.cookie = 'name=min; expires=' + new Date(2021, 11, 31).toUTCString();
  
  // 만료되지 않도록 하고 싶으면 아래와 같이 아주 먼 미래를 입력해주면 된다. 9999년 12월 31일 만료...
  document.cookie = 'name=min; expires=' + new Date(9999, 11, 31).toUTCString();
  
  // https 프로토콜에서만 쿠키를 확인할 수 있도록 secure 옵션을 추가
  document.cookie = 'info=important; secure'
  ```

cookies에 저장된 값을 확인하는 유일한 방법은 아래와 같다.

```javascript
// 아래와 같이 설정하면 쿠키들이 한번에 출력된다.
console.log(document.cookie); //name=min; lastname=kim
```

쿠키에 저장된 값들을 parse하는 쉬운 방법은 기본 메서드로는 불가능하므로, 라이브러리를 사용하거나 브라우저 저장용이면 세션스토리지나 로컬스토리지를 사용하자.



* 서버에서 cookie를 담아 클라이언트에 전달할때 set-cookie 헤더를 이용 httpOnly 설정을 해 줄 수 있다. 이렇게 설정되어 전달된 쿠키는 javascript의 document.cookie로 확인이 불가능하다. 좀더 안전하게 cookie를 보호할 수 있는 방법이다.