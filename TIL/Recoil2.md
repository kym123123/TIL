## Atoms

:app 어디에서나 사용 가능하고 공유될수 있는 상태인 recoil의 상태 단위 

```javascript
import {atom} from 'recoil';

const darkModeAtom = atom({
  key: 'darkMode',
  default: false
});

const darkModeAtom2 = atom({
  key: 'darkMode',
  default: false
});

onst DarkModeSwitch = () => {
    const [darkMode, setDarkMode] = useRecoilState(darkModeAtom2);

    console.log('darkMode', darkMode);

    return (
        <input
            type="checkbox"
            checked={darkMode}
            onChange={(e) => {
                setDarkMode(e.target.checked);
            }}
        />
    );
};

const Button = () => {
    const darkMode = useRecoilValue(darkModeAtom);

    return (
        <button style={{backgroundColor: darkMode ? 'black' : 'white', color: darkMode ? 'white' : 'black'}}>
            My Ui Button
        </button>
    );
};

export const Atoms = () => {
    return (
        <>
            <div>
                <DarkModeSwitch />
            </div>
            <div>
                <Button />
            </div>
        </>
    );
};
```

atom을 생성할때 key는 application전체에서 고유한 값을 넣어야 한다. 위의 예시처럼 darkModeAtom과 darkModeAtom2를 각각 생성하면서 같은 key값인 'darkMode'를 넣어주게 되면, `Duplicate atom key "darkMode". This is a FATAL ERROR in      production. But it is safe to ignore this warning if it occurred because of      hot module replacement.` 이러한 duplicate atom key 경고를 보게 된다. 또한 상태를 업데이트 할때, key값이 같기 때문에 두 값 모두 업데이트 되고 이는 에러를 야기하기 아주 좋다. 따라서  key값은 고유한 값을 사용하자.



## Selector

selector안에 연산 과정에서 의존하고있는 atom에 변화가 생긴다면 selector는 자동으로 내부 연산을 하게 된다. 의존성을 직접 설정해줄 필요가 없이, atom의 변화가 감지되면 자동으로 새로 연산이 되어서 원하는 data를 얻을 수 있게 된다. 만약 조건문으로 나뉘어진 연산에서, 조건에 따라서 의존하기도하고 하지 않기도 하는 atom이 있다면, 그 atom이 연산에 필요한 조건이 성립되어야 selector가 재호출 된다. 

예를들면, 아래의 연산에서 commissionEnabled atom이 true이면 if문의 연산이 실행되는데, 그 조건일때 commisionAtom의 값도 사용한다. 그러나 만약 commissionEnabled atom이 false인 상태에서 commissionAtom의 값이 변경되어도, commissionAtom의 값에 의존하지 않기 때문이 이 setter는 다시 연산되지 않게된다.

```react
set: ({set, get}, newEurValue) => {
        console.log('set value', newEurValue);
        // @ts-ignore
        let newUsdValue = newEurValue / exchangeRate;

        const commissionEnabled = get(commissionEnabledAtom); // 커미션 켜진상탠지 아닌지 boolean
        if (commissionEnabled) {
            const commission = get(commissionAtom); // 커미션 얼마나 있는지 number
            newUsdValue = addCommission(newUsdValue, commission); // 커미션 제외한 값
        }

        set(usdAtom, newUsdValue);
    },
```



```react
import {
    Box,
    FormControl,
    FormLabel,
    Heading,
    HStack,
    Icon,
    NumberInput,
    NumberInputField,
    Switch,
} from '@chakra-ui/react';
import {ArrowRight} from 'react-feather';
import {atom, selector, useRecoilState, useRecoilValue} from 'recoil';

const exchangeRate = 0.83;

const usdAtom = atom({
    key: 'usd',
    default: 1,
});

const eurSelector = selector<number>({
    key: 'eur',
    get: ({get}) => {
        let usd = get(usdAtom);

        const commissionEnabled = get(commissionEnabledAtom); // 커미션 켜진상탠지 아닌지 boolean
        if (commissionEnabled) {
            const commission = get(commissionAtom); // 커미션 얼마나 있는지 number
            usd = removeCommission(usd, commission); // 커미션 제외한 값
        }

        return usd * exchangeRate;
    },
    set: ({set, get}, newEurValue) => {
        console.log('set value', newEurValue);
        // @ts-ignore
        let newUsdValue = newEurValue / exchangeRate;

        const commissionEnabled = get(commissionEnabledAtom); // 커미션 켜진상탠지 아닌지 boolean
        if (commissionEnabled) {
            const commission = get(commissionAtom); // 커미션 얼마나 있는지 number
            newUsdValue = addCommission(newUsdValue, commission); // 커미션 추가한 값
        }

        set(usdAtom, newUsdValue);
    },
});

export const Selectors = () => {
    const [usd, setUSD] = useRecoilState(usdAtom);
    const [eur, setEUR] = useRecoilState(eurSelector);

    return (
        <div style={{padding: 20}}>
            <Heading size="lg" mb={2}>
                Currency converter
            </Heading>
            <InputStack>
                <CurrencyInput label="usd" amount={usd} onChange={(usd) => setUSD(usd)} />
                <CurrencyInput label="eur" amount={eur} onChange={(eur) => setEUR(eur)} />
            </InputStack>
            <Commission />
        </div>
    );
};

// You can ignore everything below this line.
// It's just a bunch of UI components that we're using in this example.

const InputStack: React.FC = ({children}) => {
    return (
        <HStack
            width="300px"
            mb={4}
            spacing={4}
            divider={
                <Box border="0 !important" height="40px" alignItems="center" display="flex">
                    <Icon as={ArrowRight} />
                </Box>
            }
            align="flex-end"
        >
            {children}
        </HStack>
    );
};

const CurrencyInput = ({
    amount,
    onChange,
    label,
}: {
    label: string;
    amount: number;
    onChange?: (amount: number) => void;
}) => {
    let symbol = label === 'usd' ? '$' : '€';

    return (
        <FormControl id={label.toUpperCase()}>
            <FormLabel>{label.toUpperCase()}</FormLabel>
            <NumberInput
                value={`${symbol} ${amount}`}
                onChange={(value) => {
                    const withoutSymbol = value.split(' ')[0];
                    onChange?.(parseFloat(withoutSymbol || '0'));
                }}
            >
                <NumberInputField />
            </NumberInput>
        </FormControl>
    );
};

const commissionEnabledAtom = atom({
    key: 'commissionEnabled',
    default: false,
});

const commissionAtom = atom({
    key: 'commission',
    default: 5,
});

const Commission = () => {
    const [enabled, setEnabled] = useRecoilState(commissionEnabledAtom);
    const [commission, setCommission] = useRecoilState(commissionAtom);

    return (
        <Box width="300px">
            <FormControl display="flex" alignItems="center" mb={2}>
                <FormLabel htmlFor="includeCommission" mb="0">
                    Include forex commission?
                </FormLabel>
                <Switch
                    id="includeCommission"
                    isChecked={enabled}
                    onChange={(event) => setEnabled(event.currentTarget.checked)}
                />
            </FormControl>
            <NumberInput
                isDisabled={!enabled}
                value={commission}
                onChange={(value) => setCommission(parseFloat(value || '0'))}
            >
                <NumberInputField />
            </NumberInput>
        </Box>
    );
};

const addCommission = (amount: number, commission: number) => {
    return amount / (1 - commission / 100);
};

const removeCommission = (amount: number, commission: number) => {
    return amount * (1 - commission / 100);
};

```

