## Data Fetching using Recoil

data fetching을 하기 위해서 먼저 fetching에 필요한 atom과 selector를 정의해준다.

```javascript
// selector에서 사용할 상태 userId
const userIdState = atom<number | undefined>({
    key: 'userId',
    default: undefined,
});

// 비동기 통신을 통하여 서버에서 받아온 유저정보를 반환하는 getter를 가진 selector
const userState = selector({
    key: 'user',
    get: async ({get}) => {
        const userId = get(userIdState);
        if (userId === undefined) return;

        const userData = await fetch(`https://jsonplaceholder.typicode.com/users/${userId}`).then((res) => res.json());

        console.log(userData);
        return userData;
    },
});
```

```javascript
// userState selector의 getter를 사용하는 컴포넌트 UserData를 만든다. 
// 비동기 selector를 사용하는 컴포넌트는 반드시 react의 <Suspense>컴포넌트로 감싸 주어야 에러가 발생하지 않는다.
// Suspense의 fallback props에 로딩 아이콘 같은 컴포넌트를 넣어서, 비동기 selector를 사용하는 컴포넌트만 대체 UI에 replace되도록 해준다. 매우 편하다
const UserData = () => {
    const user = useRecoilValue(userState); // 비동기 selector에서 getter만 사용
    if (!user) return null;
    console.log({user});

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
        </div>
    );
};
```

아래처럼 React의 suspense를 사용해준다

```react
export const Async = () => {
    const [userId, setUserId] = useRecoilState(userIdState);

    return (
        <Container py={10}>
            <Heading as="h1" mb={4}>
                View Profile
            </Heading>
            <Heading as="h2" size="md" mb={1}>
                Choose a user:
            </Heading>
            <Select
                placeholder="Choose a user"
                mb={4}
                value={userId}
                onChange={(event) => {
                    const value = event.target.value;
                    setUserId(value ? parseInt(value) : undefined);
                }}
            >
                <option value="1">User 1</option>
                <option value="2">User 2</option>
                <option value="3">User 3</option>
            </Select>
        // suspense 사용 
            <Suspense fallback={<div>...loading</div>}>
                <UserData />
            </Suspense>
        </Container>
    );
};
```

아주 좋은점으로는 userIdState의 default값인 userId 를 통해서 fetch를 하게 되는데, 이미 이전에 사용한 userIdState의 default값을 캐싱해놔서, 같은 userId에 대한 서버 요청을 다시 하지 않고, 이전에 저장해 둔 값을 이용하여 성능상 좋다.

위의 예시처럼 말고, useState의 상태값이나, redux에 저장된 상태값(userId)값을 이용하여 fetching을 하고 싶은경우는? 아래의 코드처럼 한다.

```javascript
const userState = selectorFamily({
    key: 'user',
    get: (userId: number) => async () => {
        const userData = await fetch(`https://jsonplaceholder.typicode.com/users/${userId}`).then((res) => res.json());

        console.log(userData);
        return userData;
    },
});
```

더이상 atom을 이용하지 않을것이므로, atom을 제거하고  selector대신 selectorFamily로 수정한다. getter의 매개변수로 userId를 지정하여 상태값을 넘겨받을 준비를 한다.

기존의 atom의 상태값을 이용하는 코드를 useState를 이용하는 코드로 변경하고, Suspense 컴포넌트 아래에 UserData컴포넌트로 userId를 props로 전달해준다.

```react
const Async = () => {
  // useState에 userId값 저장
    const [userId, setUserId] = useState<undefined | number>(undefined);

    return (
        <Container py={10}>
            <Heading as="h1" mb={4}>
                View Profile
            </Heading>
            <Heading as="h2" size="md" mb={1}>
                Choose a user:
            </Heading>
            <Select
                placeholder="Choose a user"
                mb={4}
                value={userId}
                onChange={(event) => {
                    const value = event.target.value;
                    setUserId(value ? parseInt(value) : undefined);
                }}
            >
                <option value="1">User 1</option>
                <option value="2">User 2</option>
                <option value="3">User 3</option>
            </Select>
            <Suspense fallback={<div>...loading</div>}>{userId && <UserData userId={userId} />}</Suspense>
        </Container>
    );
};
```



userData는  userId props를 받아서 selectorFamily함수를 호출하면서 넘겨주고 그  userId에 해당하는 정보를 서버로부터 넘겨받아서 컴포넌트 안에서 렌더링해준다.

```react
const UserData = ({userId}: {userId: number}) => {
    const user = useRecoilValue(userState(userId));
    console.log({user});

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
        </div>
    );
};
```

