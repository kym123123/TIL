




# 클로저

: 어떠한 함수(내부함수)가 선언된 한 함수스코프 영역(내부함수가 정의된 영역; 외부함수)보다, 내부함수의 생명주기가 더 오래 유지되는경우 외부함수의 life cycle이 종료된 이후에 내부함수를 호출하면 외부함수의 지역변수에 접근할 수 가 있다. 이 함수를 closure라고 한다.

외부 함수가 평가되어 함수 객체가 생성될 때, 현재 실행 중인 실행 컨텍스트의 렉시컬환경(전역 객체의 전역 렉시컬 환경)을 내부슬롯 [[Environment]]에 상위 스코프로 저장한다.

외부함수가 호출되면 외부함수의 렉시컬 환경이 생성되고, 이때 outer lexical environment에 자신의 내부슬롯 [[Environment]]에 저장된 참조값인 전역 렉시컬 환경을 저장한다. 이렇게 outer lexical environment에 연결된 상위 렉시컬환경이 상위 스코프이며, lexical environment로 연결된 이것을 스코프 체인이라고 한다.

외부함수가 호출되었을 때, 내부 함수가 평가된다. 이때 내부함수객체가 생성되고 역시 내부함수의 [[Environment]] 내부슬롯에 현재 실행컨텍스트에서 실행중인 외부함수의 렉시컬환경이 참조된다. (내부함수의 상위스코프가 자신이 정의된 스코프인 외부함수의 렉시컬환경이 되는 것이다.)

내부함수가 실행되면, 외부함수의 실행컨텍스트가 현재 실행컨텍스트 스택에 존재하지 않아도(즉, 외부 함수의 life cycle이 종료되었어도) 내부함수는 외부함수의 렉시컬환경에 대한 참조가 가능하다. 내부함수의 [[Environment]]에 저장되어 있는 외부 함수의 렉시컬 환경 참조값을 호출시 생성되는 자신의 렉시컬 환경에 있는 outer lexical environment refernce에 저장하기 때문이다. 따라서 외부함수의 실행 컨텍스트 스택이 pop된 이후에도 스코프 체인을 따라(외부 렉시컬 환경에 대한 참조를 따라) 상위스코프들의 변수를 참조하기도 하고 변경하기도 할 수 있게 되는것이다.

## 상태 변수를 공유하는 클로저를 만드는 방법

한 반에있는 5명의 학생들이 수업에 질문을 한 횟수를 카운트 하고, 질문을 많이한 학생을 반환하는 함수를 만들어보자

```javascript
const studentsInfo = (() => {
        const numberOfQuestion = {
          Goofy: 0,
          Judy: 0,
          Sophie: 0,
          Aiden: 0,
          Alex: 0,
        };

        return {
          count: student => {
            numberOfQuestion[student] = numberOfQuestion[student] + 1;

            return numberOfQuestion;
          },
          getTheBestStudent: () => {
            let resultStudent = 'Goofy';
            let resultNum = numberOfQuestion.Goofy;

            for (const student in numberOfQuestion) {
              if (numberOfQuestion[student] > resultNum) {
                resultStudent = student;
                resultNum = numberOfQuestion.student;
              }
            }

            return resultStudent;
          },
        };
      })();

      console.log(studentsInfo.count('Goofy')); // {Goofy: 1, Judy: 0, Sophie: 0, Aiden: 0, Alex: 0}
      console.log(studentsInfo.count('Goofy')); // {Goofy: 2, Judy: 0, Sophie: 0, Aiden: 0, Alex: 0}
      console.log(studentsInfo.count('Judy')); // {Goofy: 2, Judy: 1, Sophie: 0, Aiden: 0, Alex: 0}
      console.log(studentsInfo.count('Judy')); // {Goofy: 2, Judy: 2, Sophie: 0, Aiden: 0, Alex: 0}
      console.log(studentsInfo.count('Judy')); // {Goofy: 2, Judy: 3, Sophie: 0, Aiden: 0, Alex: 0}
      console.log(studentsInfo.count('Aiden')); // {Goofy: 2, Judy: 3, Sophie: 0, Aiden: 1, Alex: 0}
      console.log(studentsInfo.count('Aiden')); // {Goofy: 2, Judy: 3, Sophie: 0, Aiden: 2, Alex: 0}
      console.log(studentsInfo.count('Goofy')); // {Goofy: 3, Judy: 3, Sophie: 0, Aiden: 2, Alex: 0}
      console.log(studentsInfo.getTheBestStudent()); // Goofy
```

studentsInfo 식별자는 즉시실행함수가 반환한 객체를 할당받게 되고, 객체내의 메서드 count와 getTheBestStudent를 포함한다. 2개의 메서드는 자신이 정의된 스코프인 즉시실행함수 내의 스코프(객체는 스코프가 아니기 때문에 즉시실행함수가 정의된 스코프이다.)를 자신의 상위 스코프로 스코프 체인을 통해 연결된다. 따라서 학생들의 정보를 가지고 있는 numberOfQuestion 객체는 오직 count와 getTheBestStudent를 통해서만 조회가 가능하게 된다.

자신의 상위스코프에 존재하는 식별자를 공통적으로 참조하기 위해서는 위처럼 함수의 호출 횟수가 한번이어야 한다. 즉시실행함수는 한번만 호출되기 때문에 렉시컬 환경을 한번만 만들게 되고, 이렇게 선언된 단 하나의 렉시컬환경을 두개의 메서드가 공유하여사용하므로 공통적인 numberOfQuestion 객체를 참조하고 수정할 수 있는 것이다. 

아래의 예는 즉시실행함수로 한번의 렉시컬환경을 만들지 않고, class1, class2에 각각 함수를 호출하여 객체를 반환하도록 하였다. 따라서 아래의 코드는 class1, class2라는 서로 다른 두개의 렉시컬환경을 상위스코프로 하는 클로저들을 가진 객체를 갖게 되어 아래처럼 다른 결과를 나타낸다.

```javascript

      const studentsInfo = () => {
        const numberOfQuestion = {
          Goofy: 0,
          Judy: 0,
          Sophie: 0,
          Aiden: 0,
          Alex: 0,
        };

        return {
          count: student => {
            numberOfQuestion[student] = numberOfQuestion[student] + 1;

            return numberOfQuestion;
          },
          getTheBestStudent: () => {
            let resultStudent = 'Goofy';
            let resultNum = numberOfQuestion.Goofy;

            for (const student in numberOfQuestion) {
              if (numberOfQuestion[student] > resultNum) {
                resultStudent = student;
                resultNum = numberOfQuestion.student;
              }
            }

            return resultStudent;
          },
        };
      };

      const class1 = studentsInfo();
      const class2 = studentsInfo();

      console.log(class1.count('Goofy')); // {Goofy: 1, Judy: 0, Sophie: 0, Aiden: 0, Alex: 0}
      console.log(class1.count('Goofy')); // {Goofy: 2, Judy: 0, Sophie: 0, Aiden: 0, Alex: 0}
      console.log(class1.getTheBestStudent()); // Goofy

      console.log(class2.count('Judy')); // {Goofy: 0, Judy: 1, Sophie: 0, Aiden: 0, Alex: 0}
      console.log(class2.count('Alex')); // {Goofy: 0, Judy: 1, Sophie: 0, Aiden: 0, Alex: 1}
      console.log(class2.getTheBestStudent()); // Judy
```



따라서 공통된 값을 이용하여 무언가를 하기 위해서는 반드시 값을 이용하는 함수들(클로저들)이 단 하나의 렉시컬 환경을 공유하도록 해야하므로, 즉시실행 함수로 감싸서 첫번째 예시처럼 사용하도록 하자.