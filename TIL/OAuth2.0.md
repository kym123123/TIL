# OAuth 2.0

google에서 주는 accessToken은 실제 user의 google id, pw가 아니다. 또한 accessToken은 구글의 어떠한 서비스에 대한 허가가 있는지 를 알려준다.

accessToken을 이용해서 구글의 서비스에 접근해서 허용된 작업이 가능하다. oAuth가 이를 가능하게 해준다.

1. Resource server(제어하고자 하는 자원을 갖고있는 서버) 구글 - authorization server(인증과 관련된 것을 전담하는 서버)
2. resource owner(자원의 소유자) 사용자 
3. client(구글에 대신해서 요청을 해주는 앱)



oAuth 등록 절차

클라이언트가 리소스 서버를 이요하기 위해서는 사전에 리소스 서버에 승인을 받아야 한다(등록) 서비스마다 등록 방법이 다르다.

등록 방법은 다르지만 모두 공통적으로 client ID, client Secret, Authorized redirect URI

* 클라이언트 아이디: 애플리케이션 식별자 - 외부에 노출 가능

* 클라이언트 시크릿: 애플리케이션 식별자에 대한 비밀번호 - 외부에 노출되면 안된다(보안사고)

* 리다이렉트 uri는 권한 부여과정에서 authorized code를 전송해주는데 그때 전송해주는 주소. 이 주소 외에 다른곳에서 요청하면 무시당한다.

등록을 마치면 리소스 서버와 클라이언트가 위의 3가지 정보를 알게 된다. 클라이언트는 redirect url의 페이지에 해당하는 컴포넌트를 미리 준비해 두어야 한다.

소셜 로그인 버튼을클릭하면 아래와 같은 정보가 담긴 url을 연결해 주면 된다.

https://resource.server/?client_id=1&scope=b,c&redirect_uri=https://client/callback

clientid, scope(사용하고자 하는 기능 리소스 서버에서 정해놓은 형식에 따라서 들어간다, b,c 이렇게 들어가지 않음.), redirect 주소를 제공한다.



리소스 오너가 서버로 접속을 위의 주소로 하게되면, 리소스 오너가 현재 리소스 서버에 접속되어있는지 여부를 확인하고 안되어있으면 로그인 화면을 보여준다. 아무튼 로그인에 성공을하면, 리소스 서버가 client_id값과 같은 값이 있는지, 그리고 링크에있는 redirect_uri값과 서버에 등록된 값을 비교해서 일치하지 않으면 중지시킨다. 일치하면 스코프에 해당하는 권한을 클라이언트(앱)에 줄것인지 묻는 메시지를 리소스 오너에게 물어보고 유저가 허용하면 , 허용되었다는 내용이 리소스 서버로 전송된다. 

그러면 리소스 서버는 userId와 허용한 scope에 대한 정보를 서버에 저장시켜둔다.

--- 여기까지 리소스 오너에 대한 승인 완료, 이제 리소스 서버에 대한 승인을 받아야 한다.

리소스 서버로부터 바로 accessToken을 받지 않고 그전에 한가지 스텝이 있다. 

authorization code(임시비밀번호)를 리소스 서버가 리소스 오너에게 전송한다. 응답할때 헤더location: https://client/callback?code=3 이런식으로 헤더 설정을 해서 준다. location은 리다이렉션을 시킨다. (브라우저에게 해당 주소로 이동시키는 명령) 리소스 서버가 리소스 오너에게 명령한 것.

code=3은 authorization code:3 을 의미한다. 임시비밀번호를 전달해줌, uri를 통해서...

저 code=3을 통해서 client는 authorization code를 갖게 된다.

획득한 코드를 통해서클라이언트는 리소스 서버로 바로 접근한다. Https:// resource.server/token?grant_type=authorization_code&code=3&redirect_uri=https://client/callback&client_id=1&client_secret=2

code, redirect_uri, client id, client secret을 리소스 서버어게 전송한다.

리소스 서버는 auth code3을 보고 자신의서버에잇는 auth3코드와 일치하는지 확인한다. Client id, client secret, redirect url, userid, scode, auth code 전부 다 확인이 가능하다. 모두 일치하면 그때 다음 단계로 진행한다.

이제 accessToken을 발급한다 (oAuth의 목적) auth code는 이제 필요가 없으므로 리소스 서버와 클라이언트에서 지워버린다(재인증 방지)

리소스 서버는 accessToken을 발급한다. 그리고 client에게 응답으로 전해준다. 클라이언트는 accessToken을 저장한다. 이 access token은 리소스서버가 액세스 토큰을 보고 스코프도 확인이 가능하고 유저 정보도 확인이 가능하다.