# react-native debugger 설치

https://github.com/jhen0409/react-native-debugger <- 공식문서

1. react-native-debugger 설치 (brew 이용)

```
$ brew install --cask react-native-debugger
```

2. application 폴더에 생성된 react-native-debugger 실행 -> 크롬 react devtools와 같은 ui의 프로그램
3. 진행중인 프로젝트 내에 redux devtools 설치

```
npm install --save-dev redux-devtools-extension 
```

4. App.tsx내의 store 설정 변경

```typescript
import { composeWithDevTools } from 'redux-devtools-extension';

const store = createStore(
  reducers,
  composeWithDevTools(applyMiddleware(/* thunk, saga 혹은 다른 middleware */)),
);
```



5. 설치 완료