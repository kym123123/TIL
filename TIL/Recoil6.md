# Data Fetching Advanced

비동기 selector를 두가지를 차례대로 쓰면, recoil은 알아서 하나의 selector가 끝난뒤에 의존하는 다음 selector를 실행시켜서 순서를 보장해준다.

```javascript
const userState = selectorFamily({
    key: 'user',
    get: (userId: number) => async () => {
        const userData = await fetch(`https://jsonplaceholder.typicode.com/users/${userId}`).then((res) => res.json());

        console.log('calculating');

        console.log(userData);
        return userData;
    },
});

const weatherState = selectorFamily({
    key: 'weather',
    get:
        (userId: number) =>
        async ({get}) => {
            // userState도 비동기, 이 selctor도 비동기지만, recoil이 알아서 차례를 보장해준다, 따라서 user는 항상 정의되어 있다. 리코일의 기능임.
            const user = get(userState(userId));
            const weather = await getWeather(user.address.city);

            return weather;
        },
});
```



그리고 아래처럼 suspense로 감싼 컴포넌트 아래에서 다른 api를 이용하여 서버로부터 나중에 data를 받아오게 코드를 작성한 경우에는, 해당 부분만 suspense상태에 빠지고 replacing UI를 적용할 수있다.

```javascript
onst UserData = ({userId}: {userId: number}) => {
    const user = useRecoilValue(userState(userId));

    return (
        <div>
            <Heading as="h2" size="md" mb={1}>
                User data:
            </Heading>
            <Text>
                <b>Name:</b> {user.name}
            </Text>
            <Text>
                <b>Phone:</b> {user.phone}
            </Text>
            <Suspense fallback={<div>weather loading</div>}>
                <UserWeather userId={userId} />
            </Suspense>
        </div>
    );
};
```

위의 코드는 `<UserWeather />` 컴포넌트만 suspense상태에 들어가며 loading ui가 나타나게 된다. 이렇게 편리하게 원하는 부분만 loading 처리를 해줄수 있다.

recoil은 캐싱처리를 알아서 해주기 때문에, 같은 id값에 대해서 실시간으로 변하는 정보를 서버로부터 가져오는데 불편함이 있을 수있다. 

ex: 서울의 실시간 날씨를 받아오는 앱에서 seoul의 id는 서버상에서 같다. 따라서 seoul의 날씨를 몇번 요청하게 되면 가장 처음에 받아온 값을 유지하여 캐싱하고 있기 때문에 다시 요청하지 않고 그 값을 계속 사용자에게 보여주게 된다.

이를 위해서 실시간으로 변하는 데이터를 재요청하는 함수를 작성해보자.

```javascript
const userState = selectorFamily({
    key: 'user',
    get: (userId: number) => async () => {
        const userData = await fetch(`https://jsonplaceholder.typicode.com/users/${userId}`).then((res) => res.json());

      return userData;
    },
});

const weatherRequestIdState = atomFamily({
    key: 'weatherRequestId',
    default: 0,
});

const useRefetchWeather = (userId: number) => {
    const setReqeustId = useSetRecoilState(weatherRequestIdState(userId));
    return () => {
        setReqeustId((id) => id + 1);
    };
};

const weatherState = selectorFamily({
    key: 'weather',
    get:
        (userId: number) =>
        async ({get}) => {
          // ###########
             get(weatherRequestIdState(userId));
            // userState도 비동기, 이 selctor도 비동기지만, recoil이 알아서 차례를 보장해준다, 따라서 user는 항상 정의되어 있다. 리코일의 기능임.
            const user = get(userState(userId));
            const weather = await getWeather(user.address.city);

            return weather;
        },
});

const UserWeather = ({userId}: {userId: number}) => {
    const weather = useRecoilValue(weatherState(userId));
    const user = useRecoilValue(userState(userId));
    const refetch = useRefetchWeather(userId); 

    return (
        <div>
            <Text>
                <b>weather for {user.address.city}</b> {weather}
            </Text>
            <Text onClick={refetch}>(refresh weather)</Text>
        </div>
    );
};
```

// ####으로 표시된 weatherState selectorFamily 안에서 의도적으로 weatherRequestIdState를 구독하면, 사용자가 클릭 할때마다 증가하는  id값의 변화에 따라서 다시 요청을 보내게 된다. selctor안에서 의존하는 atom의 상태가 변하면 selctor의 재연산을 수행하는 특성을 활용한 것이다.

나중에는 atomEffect를 이용하여 이러한 코드를 더 효율적으로 작성할 수 있다.