## Recoil

```md
npm i recoil 설치 후
App.js에서 recoil 상태관리에 들어갈 컴포넌트를 <RecoilRoot></RecoilRoot>로 감싼다
```

```js
import { ReCoilRoot } from "recoil";

const App = () => {
  return (
    <RecoilRoot>
      <ComponentThatUsesRecoil />
    </RecoilRoot>
  );
};

export default App;
```

```js
const todoListState = atom({
  key: "todoListState",
  default: [],
});
// atom은 상태를 정의하는 방법이다. key에는 고유값, default에는 기본값을 설정하면 된다.
// atom은 useRecoilState, useRecoilValue, useSetRecoilState의 훅으로 사용 가능

const [todoList, setTodoList] = useRecoilState(todoListState);
// useRecoilstate는 setState와 같은 방식으로 작동한다.

const todoList = useRecoilValue(todoListState);
// useRecoilValue는 현재 상태 값만 필요한 경우 사용하면 된다.

const setTodoList = useSetRecoilState(todoListState);
// useSetRecoilState는 업데이트하는 상태값만 필요한 경우 사용
```

```md
useRecoilState : 기존 useState 와 같이 변경되는 값과 해당 값을 변경하는 함수를 반환.
useRecoilValue : 구독하는 값만 반환하는 함수이다. 값의 update 함수가 필요없을 경우 사용.
useSetRecoilState : 구독하는 값을 변경하는 함수만 반환
useResetRecoilState: 값을 기본값으로 reset 시키는 함수를 반환
```

```js
// useSetRecoilState의 경우 useState 함수처럼,
// setTodoList(todoList)와 같이 사용해도 되며, setTodoList(prev=>{todo, ...prev})로도
// 사용가능하다
```

## atom

```md
React 에서 흔히 볼수 있는 State 와 같은 개념으로 atom 의 값을 변경 시
해당 atom 을 구독하고 있는 모든 컴포넌트들이 리렌더링 되며 해당 변경된 atom의 값을
사용하게 된다.
redux 에서는 reducer 단위로 state 를 구성하였으나 atom 은 이런 reducer 단위가 아닌
더 잘게 쪼개진 state 단위로 상태로 관리할 수 있게 된다.
이때 기본값에 promise 설정시 정적인 값 혹은 동일한 유형의 값을 반환하는 Promise 는
설정이 가능하지만 외부에서 데이터를 불러오는 작업의 비동기 통신정보는 사용할 수 없다.
이런 비동기 통신 데이터는 다음에 설명할 selector 를 통해 사용.
```

```js
export const countState = atom({
  key: "countState", // 고유한 key 값 (대게 해당 atom을 생성하는 변수 명으로 지정합니다.)
  default: 0, // default : atom 의 초기값을 정의합니다. 정적인 값(int, string...), promise, 다른 atom 의 값으로 설정할 수 있습니다.
});
```

```js
//count.js recoil 세팅
import { atom } from "recoil";

export const countState = atom({
  key: "countState", // 해당 atom의 고유 key
  default: 0, // 기본값
});
```

> count를 보여주고 변경하는 컴포넌트 작용

```js
// 읽기 및 쓰기 컴포넌트
import { useRecoilState, useSetRecoilState, useResetRecoilState } from "recoil";
import { countState } from "../../recoil/count";

function ReadWriteCount() {
  const [count, setCount] = useRecoilState(countState); // useRecoilState 을 통한 value, setter 반환
  const setCountUseSetRecoilState = useSetRecoilState(countState); // 값을 변경하는 함수만 반환
  const resetCount = useResetRecoilState(countState); // 설정된 기본값으로 리셋
  return (
    <div>
      <h2>읽기 쓰기 카운트 컴포넌트</h2>
      <p>카운트 {count}</p>
      <button onClick={() => setCount(count + 1)}>숫자 증가</button>
      <button onClick={() => setCount(count - 1)}>숫자 감소</button>
      <button onClick={() => setCountUseSetRecoilState(count + 1)}>
        숫자 증가 (useSetRecoilState 사용)
      </button>
      <button onClick={() => setCountUseSetRecoilState(count - 1)}>
        숫자 감소 (useSetRecoilState 사용)
      </button>
      <button onClick={resetCount}>카운트 리셋</button>
    </div>
  );
}

export default ReadWriteCount;
```

> count를 보여주기만 하는 컴포넌트 작성

```js
// atom 을 읽기만 하는 컴포넌트
import { useRecoilValue } from "recoil";
import { countState } from "../../recoil/count";

function ReadOnlyCount() {
  const count = useRecoilValue(countState); // 구독하는 atom 의 값만 반환

  return (
    <div>
      <h2>읽기 전용 컴포넌트</h2>
      <p>카운트 {count}</p>
    </div>
  );
}

export default ReadOnlyCount;
```

## Selector

```md
1.  selector 는 atom 의 상태에 의존하는 동적인 데이터를 생성 생성합니다.
    selector 에서는 get 함수(필수항목)를 통해 atom 정보들을 1개이상 가져올 수 있습니다.
    이를 통해 atom을 조합하여 간단히 새로운 데이터를 생성할수 있습니다.
    물론 atom 의 정보가 바뀌면 해당 atom 을 의존하는 selector 도 자동으로 리랜더링이 됩니다. 또한 한개 이상의 atom정보를 업데이트 하도록 set 함수(선택항목)를 받을 수 있습니다.
```

```js
// count.js
import { atom } from "recoil";

export const countState = atom({
  key: "countState", // 해당 atom의 고유 key
  default: 0, // 기본값
});

// 기존 count 외에 input atom 을 추가해
// count, input 정보를 조합한 countInputState selector 를 생성하도록 하겠습니다.
export const inputState = atom({
  key: "inputState",
  default: "",
});

export const countInputState = selector({
  key: "countInputState",
  get: ({ get }) => {
    return `현재 카운트는 ${get(countState)} 이고 입력값은 ${get(
      inputState
    )} 입니다.`;
  },
});
```

```js
import { useRecoilState, useRecoilValue } from "recoil";
import { countState, inputState, countInputState } from "../../../recoil/count";

function SelectorCount() {
  const [count, setCount] = useRecoilState(countState); // useRecoilState 을 통한 value, setter 반환
  const [input, setInput] = useRecoilState(inputState); // useRecoilState 을 통한 value, setter 반환
  const countInput = useRecoilValue(countInputState); // useRecoilValue 을 통한 selector 의 get value 반환

  return (
    <div>
      <h2>읽기 쓰기 카운트 컴포넌트</h2>
      <p>카운트 {count}</p>
      <p>selector {countInput}</p>
      <input value={input} onChange={(e) => setInput(e.target.value)} />
      <button onClick={() => setCount(count + 1)}>숫자 증가</button>
      <button onClick={() => setCount(count - 1)}>숫자 감소</button>
    </div>
  );
}

export default SelectorCount;
```

```md
2.  selector 에서는 set 이라는 함수를 통해 여러개의 atom 정보를 동시에 수정할 수 있습니다. 해당 옵션은 필수는 아니고 필요한 경우에만 사용하면 됩니다.
    사용하는 방법을 익히기 위해 input 에서 숫자를 적으면 동시에 count, input 정보를 9999 로 변경되도록 하는 예시를 작성해 보도록 하겠습니다.
```

```js
// selector에 set함수 추가
export const countInputState = selector({
  key: "countTitleState",
  get: ({ get }) => {
    return `현재 카운트는 ${get(countState)} 이고 입력값은 ${get(
      inputState
    )} 입니다.`;
  },
  set: ({ set }, newValue) => {
    // 2번째 파라미터 에는 추가로 받을 인자를 나타냅니다.
    set(countState, Number(newValue)); // count atom 수정
    set(inputState, newValue + ""); // input atom 수정
  },
});
```

```js
import { useRecoilState } from "recoil";
import { countState, inputState, countInputState } from "../../../recoil/count";

function SelectorCount() {
  const [count, setCount] = useRecoilState(countState); // useRecoilState 을 통한 value, setter 반환
  const [input, setInput] = useRecoilState(inputState); // useRecoilState 을 통한 value, setter 반환
  const [countInput, setCountInput] = useRecoilState(countInputState);

  return (
    <div>
      <h2>읽기 쓰기 카운트 컴포넌트</h2>
      <p>카운트 {count}</p>
      <p>selector {countInput}</p>
      <input value={input} onChange={(e) => setInput(e.target.value)} />
      <button onClick={() => setCount(count + 1)}>숫자 증가</button>
      <button onClick={() => setCount(count - 1)}>숫자 감소</button>
      <button onClick={() => setCountInput("9999")}>
        selector 값 9999로 변경
      </button>
    </div>
  );
}

export default SelectorCount;
```

```md
3.  selector 에서는 비동기 호출에 대한 데이터 처리도 지원하고 있습니다. 또한 React의 suspense 를 지원하기 때문에 비동기 처리를 위해 별도의 작업이 없는 큰 장점이 존재합니다. 또한 기존의 redux 대비 비동기를 처리하는 별도의 미들웨어도 필요없고 작성 코드양도 현저히 적어 개인적으로 아주 좋아하는 부분입니다. :)
    관련 설명을 위한 예시로 github에서 recoil 의 star 갯수를 가져오는 비동기 예시를 작성해 보도록 하겠습니다.
```

```js
// recoil/recoilStar.js
import { selector } from "recoil";
```
