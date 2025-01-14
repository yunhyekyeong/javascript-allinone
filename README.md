# 내 생에 마지막 javascript

패스트 캠퍼스 내 생에 마지막 javascript 강의기록

---

## 자바스크립트 필수 기초 개념

## 자바스크립트 필수 도장 깨기

### 1. 클로저(Closures)

열린(open)변수를 닫힌(close)변수로 변경

#### 1) 클로저의 정의

- 함수가 선언될 당시의 Lexical Environment(어휘적 환경)의 상호관계에 따른 현상
- 비공개 변수를 가질 수 있는 환경에 있는 함수(내부 스코프에서 함수가 호출되더라도 지역변수에 접근가능 비공개 변수를 통해 데이터를 은닉하고 상태를 유지)
- 외부 변수를 기억하고 이 외부 변수에 접근할 수 있는 함수 (함수가 선언된 시점에 Lexical Environment(어휘적 환경)에 대한 참조를 유지하기 때문에 어휘적 환경은 함수가 선언 될 때 주변에 있던 변수들을 포함하고 있음 따라서 어휘적 환경에 포함되어 있던 변수에 접근 할 수 있고 함수가 외부 스코프에 있는 변수를 기억하고 함수가 원래 스코프 밖에서 실행될 때도 변수에 접근 가능)

- 클로저 용어 뜻 : 영구적 문을닫는 상황
- 람다함수 : 람다표현식으로 익명함수를 정의하는 방법으로 이름 없는 함수로 간결하고 효율적인 함수 정의 가능 > 함수형 프로그래밍 중요 기초
- 닫힌림다식 : 내부에 자유변수(free variables)가 없는 람다 표현식
- 열린림다식 : 하나 이상의 자유변수를 포함하는 람다 표현식
- 자유변수 : 내부에 정의되지 않고 외부에서 참조되는 변수 외부에서 정의되고 내부에서는 참조만 됨 우리가 일반적으로 사용하는 변수
- 묶인변수(Bound Variables) : 클로저에 의해 묶여진 자유변수 종속변수(Dependent) 제한변수(Restricted)

```javascript
// 함수반환을 통한 클로져 구현

function createCounter() {
  let count = 0; // 지역변수 자유변수
  return function () {
    // 익명함수, 내부함수 설정 -> 클로저의 핵심
    count += 1;
    return count; // 익명함수 호출 될 때 마다 현재 count 값을 출력
  };
  // 클로저 덕분에 count는 변수는 상태를 유지하고 익명함수 내부에서 createCounter함수의 지역변수인 count에 접근 가능
}

const myCounter = createCounter(); // createCounter함수를 호출하면 함수의 익명함수 = 클로저를 반환하고 클로저는 myCounter 변수에 할당됨
console.log(myCounter()); // 1
console.log(myCounter()); // 2
console.log(myCounter()); // 3
```

클로저를 통해서 createCounter함수 내부의 count 변수가 myCounter 호출 상태를 유지하며 클로저는 외부함수(createCounter)에 선언된 변수들을 기억하고 외부함수가 종료된 후에도 변수에 접근 할 수 있다.
외부함수(createCounter)는 내부함수(return에 선언된 함수)를 반환 내부함수가 클로저이고 클로저는 외부함수에 선언된 변수에 접근해서 1씩 증가 시킨다.
그럼 count는 자유변수에서 닫힌변수로 변환되고 클로저에 의해서 열린람다식에서 닫힌람다식으로 변환된다 count는 createCounter의 지역변수로 일반적으로 createCounter가 종료되면 접근 할 수 없다.
그러나 createCounter함수가 내부함수를 반환하면 내부함수는 count에 대한 참조를 유지한다. 그 결과 count는 클로저에 의해 캡처된 변수가되어 가비지컬랙터에 의해 수집되지 않아서
createCounter 함수의 호출이 끝난 후에도 내부함수를 통해서 count 함수에 접근하고 업데이트 할 수 있다.

```javascript
// callback 함수 클로저 구현

function createCounter(callback) { // callback 매개변수 count 값 변경될 때 마다 호출됨
  let count = 0;
  return function () {
    count += 1;
    callback(count) // 증가된 count 값을 매개변수로 받는 callback함수 실행
  };
}

// createCounter 함수를 호출하면서 인자에 callback함수 선언
const myCounter = createCounter(function(createCounter){
    console.log('현재 카운트:' currentCount);
});

myCounter();
```

```javascript
// 객체 메소드로 클로저 구현

function createCounter(callback) {
  // callback 매개변수 count 값 변경될 때 마다 호출됨
  let count = 0;
  return {
    increment: function () {
      count += 1;
      return count;
    },
    getCurrentCount: function () {
      return count;
    },
  };
}

const myCounter = createCounter();
const myCounter2 = createCounter();

myCounter.increment(); // 1
myCounter.increment(); // 2

myCounter2.increment(); // 1
myCounter2.increment(); // 2
myCounter2.increment(); // 3

console.log(myCounter.getCurrentCount()); // 2 출력
console.log(myCounter2.getCurrentCount()); // 3 출력 //서로 영향주지 않고 독립적으로 작동 가능
```

#### 2) 클로저 사용시 유의사항

- 메모리 관리 & 성능고려 : 외부함수가 종료 후에도 메모리에 남게되므로 메모리 누수를 일으킬 수 있음
- 코드의 복잡성 : 클로저는 이해하기 어렵고 디버깅하기 힘들 수 있기 때문에 유지보수에 유의하여 사용

#### 3) 클로저를 사용하는 주요 이유

- 캡슐화(Encapsulation)

  - 캡슐화란 데이터와 그 데이터를 조작하는 함수를 하나의 단위(클래스나 모듈) 같은 구조 안에 묶는 기법
  - 클로저는 자바스크립트에서 갭슐화를 구현하는 방법
  - 함수를 통해서만 데이터에 접근하도록 제한함으로써 캡슐화를 달성

- 데이터 은닉(Data Hiding)

  - 스코프로부터 독립된 변수를 가져서 외부로부터 보호할 수 있음
  - 보호한다는 의미는 욉 코드에 의해 임의로 변경되는 것을 방지
  - 오직 정의된 함수를 통해서만 조작 가능

- 상태 유지

  - 어떤 데이터의 값이 시간이 지나도 유지하는 것을 말함
  - 클로저는 함수가 생성될 때의 환경을 '기억'하며 이를 통해 해당 환경의 변수들의 상태를 유지
  - 외부 영향으로부터 독립적

#### 4) 커링 함수

- 여러 인자를 받는 함수를 단일 인자를 받는 함수로 변환
- 함수의 재사용성과 모듈듈성을 높임
- 지연실행 및 함수의 부분 적용 가능

```javascript
// 커링함수 부분 적용
// type 인자를 받아서 함수의 일부 인자를 미리 설정
// 클로저 형성을 위해 익명함수 return하고 익명함수는 message 매개변수 받음
function createLogger(type) {
  return function (message) {
    console.log(`[${type}] ${message}`);
  };
}

const errorLogger = createLogger("ERROR"); // type의 다른 값을 기억
const infoLogger = createLogger("INFO");
const warningLogger = createLogger("WARNING");

errorLogger("이것은 에러 메시지 입니다."); // [ERROR] 이것은 에러 메시지 입니다.
infoLogger("이것은 정보 메시지 입니다."); // [INFO] 이것은 정보 메시지 입니다.
warningLogger("이것은 경고 메시지 입니다."); // [WARNING] 이것은 경고 메시지 입니다.
```

- 지연실행 : 필요할 때 까지 계산을 연기하는 것으로 커링에서는 함수가 모든 인자를 받을 때 까지 실행을 지연시키는 방식으로 적용 커링된 함수는 여러인자를 받는 함수를 인자를 하나씩 받는 여러함수의 연쇄로 변환 각 중간단계의 함수는 다음인자를 기다리며 실행을 지연시킵니다.

```javascript
// 커링함수 지연실행

function add(a) {
  return function (b) {
    return function (c) {
      return a + b + c;
    };
  };
} // a, b, c 각각의 인자가 내부함수에 사용되어 커링함수가 형성됨

console.log(add(1)(2)); // 함수 실행되지 않음 인자를 받을 때 까지 실행 지연 > 함수의 인자를 보다 유연하게 관리
console.log(add(1)(2)(3)); // 6
```

#### 5) 리덕스 미들웨어의 구조

- 커링함수는 리덕스 미들웨어 설계와 구현에 아주 적합한 모델 제공 미들웨어에서는 지연실행이 중요한 역할을 하고 커링함수는 지연실행을 효율적으로 활용할 수 있는 구조를 가지고 있습니다.
- 리엑트 라이브러리 리덕스는 커링과 지연실행을 적극적으로 활용합니다.
- 리덕스 미들웨어는 기본적으로 커링패턴을 따르는 함수들로 구성되어 있음 이러한 구조는 리덕스 미들웨어 시스템이 어떻게 작동하는 지에 대해 정확한 통찰을 제공합니다.

```javascript
// 리덕스 미들웨어는 3단계의 함수로 구성되어 있습니다.
const middleware = (store) => (next) => (action) => {
  // 미들웨어 로직
  // store함수를 매개변수로 받는데 store에는 getState 및 dispatch(action) 메소드가 포함되어 있어 미들웨어가 현재 상태를 조회하거나 새로운 액션을 디스패치 할 수 있음
  // next함수는 action을 다음 미들웨어로 전달하거나 미들웨어 체인에 맞게 리듀서로 전달하는 역할
  // dispatch된 action을 매개변수로 받음 여기서 미들웨어는 필요한 로직을 수행하고 액션을 수정하거나 추가적인 액션을 dispatch하거나 혹은 액션을 무시할 수 있음
```

미들웨어는 여러단계의 함수를 통해서 각각의 인자를 차례대로 받습니다. 이는 커링의 전형적인 패턴이고 각 단계의 함수는 실제로 해당 단계에 있는 인자가 실행될 때 까지 실행을 지연합니다.
예를 들어 액션을 처리하는 로직은 실제로 해당 액션이 디스패치 될때까지 실행되지 않습니다.
리덕스 미들웨어는 커링과 지연실행을 기반으로 하는 강력한 패턴을 사용합니다.
이를 통해서 미들웨어는 리덕스 생태계에서 아주 중요한 역할을 수행하고 application 상태관리를 더욱 유연하고 효과적으로 만듭니다.
비동기작업, 로깅, 에러처리 등 부가기능을 application에 통합 할 수 있습니다.

#### 6) 리액트에서 클로저가 구현된 사례

리액트에서 클로저는 함수 컴포넌트 혹은 hook과 관련하여 자주 언급되고 클로저는 컴포넌트 상태 props, 함수 내부 정의된 변수를 기억하고 접근 가능하게 합니다.
실제 이런 hook이 리액트에서 현재 클로저로 구현되어 있지는 않지만, 함수명 컴포넌트 자체적으로 상태를 유지 할 수가 없는데 어떻게 리액트 프레임워크 내부에서 해당 컴포넌트 상태를 관리를 관리 할 수 있고 이것이 어떻게 기술적으로 가능한지 클로저를 통해 이해할 수 있음

##### useState(hook)

**Hook이란 ? - 함수 컴포넌트에서도 상태를 갖고 수정 할 수 있는 기능**

- 클래스 컴포넌트는 해당 인스턴스에서 필요한 부분만을 업데이트하여 context 상태를 유지
- 함수 컴포넌트는 상태가 변경될 때마다 새로운 인스턴스를 생성하기 때문에 초기화된 상태만을 가질 수 있었음 > hook을 통해 이러한 제약이 사라짐 hook을 통해서 함수 컴포넌트도
- 함수 컴포넌트가 다시 실행되어도, 해당 함수의 상태값이 초기화 되지 않고 react에 의해 사리지지 않는 것
