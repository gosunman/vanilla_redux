# 😎 개념 미리 정리하기

- 리덕스는 가장 많이 사용하는 리액트 상태 관리 라이브러리이다.
- 리덕스를 사용하면 상태 업데이트 로직을 분리시켜서 더욱 효율적으로 관리할 수 있다.
- 컴포넌트끼리 같은 상태를 공유해야 할 때도 쉽게 할 수 있다.
- 단순히 전역 상태를 관리하기만 한다면 Context API를 사용하는 것만으로도 충분하다.
- 하지만 프로젝트가 커진다면 리덕스를 쓰는 편이 더 좋다.
- 코드의 유지 보수성도 높여주고 작업 효율도 극대화해주기 때문이다.
- 추가러 편리한 개발자 도구도 지원하며, 미들웨어라는 기능을 제공하여 비동기 작업을 훨씬 효율적으로 관리할 수 있게 해준다.

## 액션

- 상태에 어떠한 변화가 필요할 때 발생하는 것이 액션이다.
- 액션은 하나의 객체로 표현된다.
- 액션 객체는 type 필드를 반드시 가지고 있어야 한다.
- type 필드의 값이 이 액션의 이름이라고 생각하면 된다.

```js
{
  type: "ADD_TODO",
  data: {
    id: 1,
    text: 'learning redux'
  }
}
```

## 액션 생성 함수

- 액션 객체를 만들어 주는 함수이다.
- 어떤 변화를 일으켜야 할 때마다 액션 객체를 만들어야 한다.
- 편리하면서도 형식을 위반하지 않기 위해 액션 생성 함수를 정의한다.

```js
function addTodo(data){
  return {
    type:'ADD_TODO',
    data
  }
}
```

## 리듀서

- 리듀서는 변화를 일으키는 함수이다.
- 액션을 만들어 발생시키면 리듀서가 현재 상태와 액션 객체를 받아 처리한다.
- 두 값을 받아서는 새로운 상태를 만들어서 반환한다.

```js
const initialState = {
  conter: 1
}

function reducer(state=initialState, action) {
  switch (action.type){
    case INCREMENT:
      return {
        counter: state.counter + 1
      }
    default:
      return state
  }
}
```

## 스토어

- 한 개의 프로젝트는 단 하나의 스토어만 가질 수 있다.
- 스토어 안에는 현재 상태와 리듀서가 들어가 있고, 중요한 내중 함수를 지닌다.

## 디스패치

- 디스패치는 스토어의 내장 함수 중 하나이다.
- 디스패치는 '액션을 발생시키는 것'이라고 이해하면 된다.
= 이 함수는 dispatch(action)과 같은 형태로 액션 객체를 넣어 호출한다.
- 이 함수가 호출되면 스토어는 리듀서 함수를 실행시켜서 새로운 상태를 만든다.

## 구독

- 구독도 스토어의 내장 함수 중 하나이다.
- 구독 함수 안에 리스너 함수를 파라미터로 넣어서 호출한다.
- 이 함수는 액션이 디스패치되어 상태가 업데이트 될 때마다 호출된다.

```js
const listener = () => {
  console.log('update complete')
}
const unsubscribe = store.subscribe(listener);
unsubscribe(); // 추후 구독을 비활성화할 때 함수를 호출
```

# 😋 리액트 없이 쓰는 리덕스

```js
// index.css
.toggle {
  border: 2px solid black;
  width: 64px;
  height: 64px;
  border-radius: 32px;
  box-sizing: border-box;
}

.toggle.active {
  background: yellow;
}

// index.html
<!DOCTYPE html>
<html>
  <head>
    <title>Parcel Sandbox</title>
    <meta charset="UTF-8" />
    <link rel="stylesheet" type="text/css" href="index.css" />
  </head>
  <body>
    <div class="toggle"></div>
    <hr />
    <h1>0</h1>
    <button id="increase">+1</button>
    <button id="decrease">-1</button>
    <script src="./index.js"></script>
  </body>
</html>

// index.js
import { createStore } from "redux";

const divToggle = document.querySelector(".toggle");
const counter = document.querySelector("h1");
const btnIncrease = document.querySelector("#increase");
const btnDecrease = document.querySelector("#decrease");

// 액션 이름
const TOGGLE_SWITCH = "TOGGLE_SWITCH";
const INCREASE = "INCREASE";
const DECREASE = "DECREASE";

// 액션 생성 함수
const toggleSwitch = () => ({
  type: TOGGLE_SWITCH
});
const increase = (difference) => ({
  type: INCREASE,
  difference
});
const decrease = () => ({
  type: DECREASE
});

// 초깃값 설정
const initialState = {
  toggle: false,
  counter: 0
};

// 리듀서 함수 정의
function reducer(state = initialState, action) {
  switch (action.type) {
    case TOGGLE_SWITCH:
      return {
        ...state, // 불변성
        toggle: !state.toggle
      };
    case INCREASE:
      return {
        ...state, // 불변성
        counter: state.counter + action.difference
      };
    case DECREASE:
      return {
        ...state, // 불변성
        counter: state.counter - 1
      };
    default:
      return state;
  }
}

// 스토어 생성
const store = createStore(reducer);

// Render 함수 만들기
const render = () => {
  const state = store.getState(); // 현재 상태 부르기
  // 토글 처리
  if (state.toggle) {
    divToggle.classList.add("active");
  } else {
    divToggle.classList.remove("active");
  }
  // 카운터 처리
  counter.innerText = state.counter;
};

render();
store.subscribe(render);

// 구독하기
const listener = () => {
  console.log("update requested");
};

const unsubscribe = store.subscribe(listener);
// 추후에는 subscribe 함수 대신 react-redux 라이브러리를 사용할 예정

// unsubscribe(); // 추후 구독을 비활성화 할 때 함수를 호출

// 액션 발생시키기
divToggle.onclick = () => {
  console.log("divToggle click");
  store.dispatch(toggleSwitch());
};
btnIncrease.onclick = () => {
  console.log("btnIncrease click");
  store.dispatch(increase(1));
};
btnDecrease.onclick = () => {
  console.log("btnDecrease click");
  store.dispatch(decrease());
};
```

# 😂 리덕스의 세 가지 규칙

## 단일 스토어

- 하나의 애플리케이션에는 하나의 스토어가 있다.

## 읽기 전용 상태

- 리덕스의 상태는 읽기 전용이다.
- 리덕스의 상태를 업데이트 할 때는 기존의 객체를 건드리지 않고 새로운 객체를 생성해주어야 한다.
- 내부적으로 데이터가 변경되는 것을 감지하기 위해 얕은 비교 검사를 하기 때문이다.

## 리듀서는 순수한 함수

- 순수한 함수란
  1. 이전 상태와 액션 객체를 파라미터로 받는다.
  2. 파라미터 외의 값에는 의존하면 안된다.
  3. 이전 상태를 건드리지 않고 새로운 상태 객체를 만들어서 반환한다.
  4. 똑같은 파라미터로 호출된 리듀서는 언제나 똑같은 결과를 반환한다.
  
## 주의사항

- 따라서 리듀서 내부에 Date 함수를 사용하여 시각을 가져오거나 하면 파라미터가 같아도 다른 결과가 나오기 때문에 사용하면 안된다.
- 네트워크 요청과 같은 비동기 작업은 미들웨어를 통해 관리한다.
