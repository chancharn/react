# 상태관리

# Hooks

> 함수형 컴포넌트에서도 상태 관리를 할 수 있는 useState, 그리고 렌더링 직후 작업을 설정하는 useEffect 등의 기능등을 제공하여 기존의 함수형 컴포넌트에서 할 수 없었던 다양한 작업을 할 수 있다.

## useState

```js
const [value, setValue] = useState(0);
```

```md
useState 는 가장 기본적인 Hook 으로서, 함수형 컴포넌트에서도 가변적인 상태를 지니고 있을 수 있게 해준다.
useState의 인자 값은 기본값으로 '0' 으로 설정하겠다는 의미다.
이 함수가 호출되고 나면 '배열'을 반환한다.
그 배열의 첫번째 원소는 현재 상태 값이고, 두번째 원소는 상태를 설정하는 '함수'다.
호출할 땐 useState가 아닌 두번째 원소인 setValue를 파라미터에 넣어서 호출하게 되면 전달받은 파라미터에 의해 value 값이 바뀌게 되고 컴포넌트는 정상적으로 리렌더링 됩니다.
```

> 표현방식 1

```js
// counter.js
import React, { useState } from "react";

const Counter = () => {
  const [value, setValue] = useState(0);
  return (
    <div>
      <p>
        현재 카운터 값은 <b>{value}</b>입니다.
      </p>
      <button onClick={() => setValue(value + 1)}>+1</button>
      <button onClick={() => setValue(value - 1)}>-1</button>
    </div>
  );
};
// 이와 같이 onClick 이벤트에서 setValue라는 함수를 실행시키고 setValue의 인자를 'value + 1'로 받아서 다시 현재 값인 value에 'value + 1'값이 들어간다.
```

> 표현방식 2

```js
// counter.js
import React, { useState } from 'react';

export default function App() {
  const [value, setValue] = useState(0);
  const PlusBtn = {() => {setValue(value + 1)}};
  const MinusBtn = {() => {setValue(value - 1)}};
  return
    <div className="App">
      <p>number : {value}</p>
      <button onClick={PlusBtn}>+</button>
      <button onClick={MinusBtn}>-</button>
    </div>
};
```

> 두개 이상의 useState

```js
// info.js
import React, { useState } from 'react';

const info = () => {
  const [name, setName] = useState('');
  const [age, setAge] = useState('');

  const onChangeName = setName(e.target.value);
  const onChangeAge = setAge(e.target.value);
  return
    <div>
      <input value={name} onChange={onChangeName} />
      <input value={age} onChange={onChangeAge} />
    </div>
    <div>
      <div>이름 : {name}</div>
      <div>나이 : {age}</div>
    </div>
};

// useState의 함수들(두번째 인자)의 인자로 'number + 1'이 아닌, e.target.value로 지정하고, input태그에서 입력받는 name와 age를 value로 보내고 그 value가 e.target.value로 들어가고 결국 현재 state값이 된다.
```

## useEffect

```js
useEffect(() => {
  1console.log('mounted');
  2return () => {
    console.log('unmounted');
  }
}, [3]);
// 최초 한번만 실행하려면 1(componentWillMount, componentDidmount), 업데이트 시킬 때마다 실행하려면 3(componentDidUpdate), 제거(언마운트)되기 전, 혹은 업데이트 되기 직전에 실행하려면 2(componentWillUnmount)
```

```md
useEffect 는 리액트 컴포넌트가 렌더링 될 때마다 특정 작업을 수행하도록 설정 할 수 있는 Hook 이다.
클래스형 컴포넌트의 componentDidMount 와 componentDidUpdate 를 합친 형태이다.
```

```js
import React, { useState, useEffect } from "react";

const Info = () => {
  const [name, setName] = useState("");
  const [nickname, setNickname] = useState("");
  useEffect(() => {
    console.log("렌더링이 완료되었습니다!");
    console.log({
      name,
      nickname,
    });
  }, []);

  const onChangeName = (e) => {
    setName(e.target.value);
  };

  const onChangeNickname = (e) => {
    setNickname(e.target.value);
  };

  return (
    <div>
      <div>
        <input value={name} onChange={onChangeName} />
        <input value={nickname} onChange={onChangeNickname} />
      </div>
      <div>
        <div>
          <b>이름:</b> {name}
        </div>
        <div>
          <b>닉네임: </b>
          {nickname}
        </div>
      </div>
    </div>
  );
};

export default Info;
```

```md
결과:
렌더링이 완료되었습니다.

> {name: "", nickname: ""}
```

> 마운트 될 때만 실행시키고 싶은 경우

```md
만약 useEffect 에서 설정한 함수가 컴포넌트가 화면에 가장 처음 렌더링 될 때만 실행되고 업데이트 할 경우에는 실행 할 필요가 없는 경우엔
함수의 두번째 파라미터로 비어있는 배열을 넣어주시면 됩니다.
즉 입력값이 업데이트 되더라도 콘솔값이 실행되지 않는다.
```

```js
useEffect(() => {
  console.log("마운트 될 때만 실행됩니다.");
}, []);
```

> 특정 값이 업데이트 될 때만 실행하고 싶을 때

```js
useEffect(() => {
  console.log(name);
}, [name]);
// 배열 안에는 useState 를 통해 관리하고 있는 상태를 넣어줘도 되고,
// props 로 전달받은 값을 넣어주어도 됩니다.
```

```md
useEffect 는 기본적으로 렌더링 되고난 직후마다 실행되며, 두번째 파라미터 배열에 무엇을 넣느냐에 따라 실행되는 조건이 달라집니다.
만약 컴포넌트가 언마운트되기 전이나, 업데이트 되기 직전에 어떠한 작업을 수행하고 싶다면 useEffect 에서 뒷정리(cleanup) 함수를 반환해주어야 합니다.
Info 컴포넌트의 useEffect 부분을 다음과 같이 수정해보세요.
```

```js
// Info.js
import React, { useState, useEffect } from "react";

const Info = () => {
  const [name, setName] = useState("");
  const [nickname, setNickname] = useState("");
  useEffect(() => {
    console.log("effect");
    console.log(name);
    return () => {
      console.log("cleanup");
      console.log(name);
    };
  });

  const onChangeName = (e) => {
    setName(e.target.value);
  };

  const onChangeNickname = (e) => {
    setNickname(e.target.value);
  };

  return (
    <div>
      <div>
        <input value={name} onChange={onChangeName} />
        <input value={nickname} onChange={onChangeNickname} />
      </div>
      <div>
        <div>
          <b>이름:</b> {name}
        </div>
        <div>
          <b>닉네임: </b>
          {nickname}
        </div>
      </div>
    </div>
  );
};

export default Info;
```

```js
// App.js
import React, { useState } from "react";
import Info from "./Info";

const App = () => {
  const [visible, setVisible] = useState(false);
  return (
    <div>
      <button
        onClick={() => {
          setVisible(!visible);
        }}
      >
        {visible ? "숨기기" : "보이기"}
      </button>
      <hr />
      {visible && <Info />}
    </div>
  );
};

export default App;
```

```md
컴포넌트가 나타날 때 콘솔에 effect 가 보이고, 사라질 때 cleanup 이 보여지게 된다.
그 다음엔, 한번 인풋에 이름을 적어보고 콘솔에 어떤 결과가 나타나는지 확인해보세요.
렌더링이 될 때마다 뒷정리 함수가 계속 보여지고 있는 것을 확인 할 수 있다. 그리고, 뒷정리 함수가 호출 될 때에는 업데이트 되기 직전의 값을 보여주고 있다.
만약에, 오직 언마운트 될 때만 뒷정리 함수를 호출하고 싶다면 useEffect 함수의 두번째 파라미터에 비어있는 배열을 넣으면 된다.
```

## useRef

```md
컴포넌트 별로 데이터를 관리해야 할 경우, useState도 데이터를 저장하지만 useState는 화면 렌더링과 관련된 데이터를 저장하는 공간이라서 state를 바꾸면 컴포넌트가 리렌더링되고 맙니다. 화면 리렌더링과는 관련 없는 데이터를 저장하는 경우 useRef를 사용
```

```js
import React, { useCallback } from "react";
let data = 0;
// 컴포넌트 간 데이터를 공유해야 할 경우 위와같이 변수 지정
const Basic = () => {
  let data = 0;
  // 컴포넌트가 리렌더링되는 경우, let data가 다시 실행되어 '0'으로 초기화가 됨
  const onClick = useCallback(() => {
    data++;
  }, [data]);
  return <div onClick={onClick}>Basic</div>;
};
export default Basic;
```

```js
import React, { useCallback, useRef } from "react";
const Basic = () => {
  const dataRef = useRef(0);
  const onClick = useCallback(() => {
    dataRef.current++;
  }, []);
  return <div onClick={onClick}>Basic</div>;
};
export default Basic;
```

```md
useRef로 생성한 데이터는 리렌더링 여부와 상관없이 같은 값이 유지됩니다. 또한 그 값을 바꾸더라도 화면이 리렌더링되지 않습니다. 초깃값은 useRef로 생성할 때 인수로 넣어주면 됩니다. 여기서 특이한 점은 dataRef의 값을 가져올 때 dataRef.current로 접근해야 한다는 것입니다.
```

```md
useRef를 활용한다면 useEffect에서 componentDidUpdate 효과를 낼 수 있습니다. componentDidMount를 무시하는 방법입니다.
```

```js
import React, { useEffect, useRef } from "react";
const Basic = () => {
  const mountRef = useRef(false);
  useEffect(() => {
    if (mountRef.current) {
      console.log("updated!");
    } else {
      mountRef.current = true;
    }
  });
  return <div>Basic</div>;
};
export default Basic;
```

```md
useEffect는 mount될 때도 한 번 실행되는데 그 때는 mountRef가 false입니다. 따라서 if 문이 동작하지 않습니다. 단, else문에서 mountRef를 true로 바꾸어놨기 때문에 다음 리렌더링때부터는 if문 내부가 실행됩니다.

정리하자면 useRef는 그 안의 데이터가 바뀌어도 화면을 리렌더링하지 않지만, 리렌더링 후에도 데이터를 유지시켜줍니다.
```

## useContext

```md
함수형 컴포넌트에서도 Context를 사용할 수 있는 훅이다.
```

```js
// ContextSample.js
import React, { createContext, useContext } from "react";

const ThemeContext = createContext("black");
const ContextSample = () => {
  const theme = useContext(ThemeContext);
  const style = {
    width: "24px",
    height: "24px",
    background: theme,
  };
  return <div style={style} />;
};

export default ContextSample;
```
