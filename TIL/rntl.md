# React-Native Testing Library

```
$ npm i --save-dev @testing-library/react-native
```



### Additional Jest Matchers

```
$ npm i --save-dev @testing-library/jest-native
```



### example

```javascript
import { render, fireEvent } from '@testing-library/react-native';
import { QuestionsBoard } from '../QuestionsBoard';

test('form submits two answers', () => {
  const allQuestions = ['q1', 'q2'];
  const mockFn = jest.fn();

  const { getAllByA11yLabel, getByText } = render(
    <QuestionsBoard questions={allQuestions} onSubmit={mockFn} />
  );

  const answerInputs = getAllByA11yLabel('answer input');

  fireEvent.changeText(answerInputs[0], 'a1');
  fireEvent.changeText(answerInputs[1], 'a2');
  fireEvent.press(getByText('Submit'));

  expect(mockFn).toBeCalledWith({
    '1': { q: 'q1', a: 'a1' },
    '2': { q: 'q2', a: 'a2' },
  });
});
```



### API / Usage

- [`render`](https://callstack.github.io/react-native-testing-library/docs/api#render) – deeply renders given React element and returns helpers to query the output components.
- [`fireEvent`](https://callstack.github.io/react-native-testing-library/docs/api#fireevent) - invokes named event handler on the element.
- [`waitFor`](https://callstack.github.io/react-native-testing-library/docs/api#waitfor) - waits for non-deterministic periods of time until queried element is added or times out.
- [`waitForElementToBeRemoved`](https://callstack.github.io/react-native-testing-library/docs/api#waitforelementtoberemoved) - waits for non-deterministic periods of time until queried element is removed or times out.
- [`within`](https://callstack.github.io/react-native-testing-library/docs/api#within) - creates a queries object scoped for given element.