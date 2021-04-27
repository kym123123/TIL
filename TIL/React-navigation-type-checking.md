# React navigation

## Type checking with Typescript 

1. route name과 params 타입 체크하기 : route name과 각 route name에 mapping 되는 params의 타입을 가진 object 만들기

   ```typescript
   type RootStackParamList = {
     Profile: { userId: string };
   }
   // Profile이라는 이름은 가진 route의 매개변수는 userId 프로퍼티가 있는 객체이며, userId는 string 타입
   ```

   ```typescript
   // 여러가지 route가 있고, 여러 매개변수를 가질 경우
   type RootStackParamList = {
     Home: undefined;
     Profile: { userId: string };
     Feed: { sort: 'latest' | 'top' } | undefined;
   };
   
   // Home route에는 매개변수가 없음을 undefined로 표현
   // Feed route는 sort 프로퍼티 키가 있는 객체를 매개변수로 갖거나, 아무것도 갖지 않거나.. optional params
   ```

   위의 ParamList를 사용하는 Stack Navigation은 아래와 같다.

   ```react
   const RootStack = createStackNavigator();
   
   <RootStack.Navigator initialRouteName="Home">
     <RootStack.Screen name="Home" component={Home}/>
     <RootStack.Screen
       name="Profile"
       component={Profile}
       initialParams={{ userId: user.id }} />
     <RootStack.Screen name="Feed" component={Feed} />
   </RootStack.Navigator>
   ```

2. Screen 타입 체크하기 : screen에서 받은 `route prop`과 `navigation prop`을 타입 체킹 해준다. 타입 체크를 위해서 아래처럼 `StackNavigationProp`과 `RouteProp`을 각각 import 해서 사용해 준다.

   1. navigationProp 타입

   ```typescript
   import { StackNavigationProp } from '@react-navigatioin/stack';
   
   type ProfileScreenNavigationProp = StackNavigationProp<RootStackParamList, 'Profile'>;
   // RootStackParamList와 현재 screen의 이름을 제네릭으로 받는다.
   
   type Props = {
     navigation: ProfileScreenNavigationProp;
   }
   ```

   StackNavigation이 아닌 다른 navigation을 사용하는 경우도 각각 필요한 navigationprop을 import 해준다. 

   ```typescript
   import { DrawerNavigationProp } from @react-navigation/drawer;
   import { BottomTabNavigationProp } from @react-navigation/bottom-tabs;
   ```

   2. routeProp 타입

   ```typescript
   import { RouteProp } from '@react-navigation/native';
   
   type ProfileScreenRouteProp = RouteProp<RootStackParamList, 'Profile'>;
   // 제네릭 2가지를 받는다. 
   
   type Props = {
     route: ProfileScreenRouteProp;
   }
   ```

   3. 타입체킹 결과

   ```typescript
   import { RouteProp } from '@react-navigation/native';
   import { StackNavigationProp } from '@react-navigation/stack';
   
   type RootStackParamList = {
     Home: undefined;
     Profile: { userId: string };
     Feed: { sort: 'latest' | 'top' } | undefined;
   };
   
   type ProfileScreenRouteProp = RouteProp<RootStackParamList, 'Profile'>;
   
   type ProfileScreenNavigationProp = StackNavigationProp<RootStackParamList, 'Profile'>;
   
   type Props = {
     route: ProfileScreenRouteProp;
     navigation: ProfileScreenNavigationProp;
   }
   ```

   혹은 navigation과 route props를 아래처럼 처리할 수 있다.

   ```typescript
   import { StackScreenProps } from '@react-navigation/stack';
   
   type RootStackParamList = {
     Home: undefined;
     Profile: { userId: string };
     Feed: { sort: 'latest' | 'top' } | undefined;
   };
   
   type Props = StackScreenPops<RootStackParamList, 'Profile'>;
   ```

3. 컴포넌트에 props 적용하기

   ```typescript
   // 함수형 컴포넌트
   function ProfileScreen({route, navigation}: Props) {
     // ...
   }
   
   // 클래스형 컴포넌트
   class ProfileScreen extends React.Components<Props> {
     render() {
       // ...
     }
   }
   ```

> react-navigation 공식문서에는, 이러한 각 type들을 모든 screen components 마다 반복해서 import 후 사용하지 말고, types.tsx라는 분리된 파일을 만들고, 그 파일에서  import 해서 사용하는 것을 권장.