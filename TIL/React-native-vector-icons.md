# React native vector icons

출처 : https://github.com/oblador/react-native-vector-icons#bundled-icon-sets

* 패키지 설치

```
npm i react-native-vector-icons 
// 프로젝트 폴더 내에서 설치
```

* ios 설정 : 위의 소스코드를 프로젝트 내의 iOS/프로젝트폴더이름/Info.plist안에 붙여넣는다. 붙여넣는 위치는 맨 밑에서 `</dict></plist>`로 끝나는 부분의 바로 앞에 넣는다.

```javascript
<key>UIAppFonts</key>
<array>
  <string>AntDesign.ttf</string>
  <string>Entypo.ttf</string>
  <string>EvilIcons.ttf</string>
  <string>Feather.ttf</string>
  <string>FontAwesome.ttf</string>
  <string>FontAwesome5_Brands.ttf</string>
  <string>FontAwesome5_Regular.ttf</string>
  <string>FontAwesome5_Solid.ttf</string>
  <string>Foundation.ttf</string>
  <string>Ionicons.ttf</string>
  <string>MaterialIcons.ttf</string>
  <string>MaterialCommunityIcons.ttf</string>
  <string>SimpleLineIcons.ttf</string>
  <string>Octicons.ttf</string>
  <string>Zocial.ttf</string>
  <string>Fontisto.ttf</string>
</array>
```



* 안드로이드 설정 : 아래의 소스코드를 프로잭트 내의 android/app/build.gradle 파일 안에 붙여 넣는다. 붙여 넣는 위치는 맨 아래 줄의 apply..로 시작하는 코드의 바로위에 넣으면 된다.

```
apply from: "../../node_modules/react-native-vector-icons/fonts.gradle"
```



위의 설정을 마치면 ios만을 위해서 pod install을 해준다.

```
npx pod-install ios

// 그 이후에 npm run ios를 다시 해준다.
```



* 사용법

  ```javascript
  import React from 'react';
  import {View, Text} from 'react-native';
  import Entype from 'react-native-vector-icons/Entypo';
  // import 해주기, 사용할 것은 공식문서에 다양하게 있으므로, 원하는 것을import 하면 된다.
  
  const App = () => {
    return (
      <View>
        <Text>dd</Text>
        <Text>dd</Text>
        <Text>dd</Text>
        <Text>dd</Text>
        <Text>dd</Text>
        <Text>
      // name과 size 지정
          <Entype name={'home'} size={24} />
        </Text>
      </View>
    );
  };
  
  export default App;
  ```

  
