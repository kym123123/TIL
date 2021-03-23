# Express

## 1. 기본 라우팅

: 라우팅은 URI(또는 경로) 및 특정한 HTTP 요청 메소드(GET, POST 등)인 특정 엔드포인트에 대한 클라이언트 요청에 애플리케이션이 응답하는 방법을 결정하는 것.

**각 라우트는 하나 이상의 핸들러함수를 가질 수 있음. 핸들러 함수는 라우트가 일치할 때 실행된다.**

```javascript
app.METHOD(PATH, HANDLER)
```

app => express의 인스턴스.

METHOD => HTTP 요청 메소드

PATH => 서버에서의 경로

HANDLER => 라우트가 일치할 때 실행되는 함수.

```javascript
app.get('/', (req, res) => {
  res.send('Hello world!');
}); // 홈페이지에서 Hello world!로 응답하는 라우트
// '/'는 애플리케이션의 홈페이지인 루트 라우트
```

```javascript
// 애플리케이션의 홈 페이지인 루트 라우트(/)에서 POST 요청에 응답
app.post('/', (req, res) => {
  res.send('Got a POST request');
});
```

```javascript
// '/user'라우트에 대한 PUT 요청에 응답
app.put('/user', (req, res) => {
  res.send('Got a PUT request at /user');
});
```

```javascript
// '/user'라우트에 대한 DELETE 요청에 응답
app.delete('/user', (req, res) => {
  res.send('Got a DELETE request at /user');
});
```



- 라우트 핸들러

  : next('route')를 호출하여, 나머지 라우트 콜백을 우회할 수 있음, 이러한 메커니즘을 이용하면 라우트에 대한 사전 조건을 지정한 후, 현재의 라우트를 계속할 이유가 없는 경우에는 제어를 후속 라우트에 전달할 수 있음. 라우트 핸들러는 함수나 함수 배열의 형태 또는 둘을 조합한 형태일 수 있다. 하나의 콜백 함수는 하나의 라우트를 처리할 수 있다.

  

  2개 이상의 콜백 함수는 하나의 라우트를 처리할 수 있다.(next 오브젝트를 반드시 지정해야 한다.)

  ```javascript
  app.get('/example/b', function (req, res, next) {
    console.log('the response will be sent by the next function...');
    next();
  }, function (req, res) {
    res.send('Hello from B!');
  });
  ```

  