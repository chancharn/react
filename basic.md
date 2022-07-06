```html
CSR (Client Side Rendering)
html -> js -> react -> rendering (동작, 화면 O)
js가 전부 다운되어 리엑트 에플리케이션이 실행되기 전까지 화면이 보이지 X
리엑트 에플리케이션이 정상 실행된 후, 화면이 보이면서 유저가 인터렉션 가능

SSR (Server Side Rendering)
html -> js/rendering(동작 X, 화면상태만) -> react -> 동작 O
js가 전부 다운로드 되지 않아도, 화면은 보이지만 동작하지는 않음
js가 전부 다운로드 되고, 리엑트 에플리케이션이 정상 실행된 후, 유저가 사용 가능


REACT 핵심기능
1. 리엑트 컴포넌트 -> HTMLElement 연결
import ReactDom from 'react-dom';

2. 리엑트 컴포넌트 만들기
import React from 'react';

npm init -y
```


```js
const root = document.querySelector('#root');
const btn_plus = document.querySelector('#btn_plus');

let i = 0;

root.innerHTML= '<p>init : 0</p>';

btn_plus.addEventListener('click', () => {
  root.innerHTML = `<p>init : ${i++}</p>`
});

const component = {
  message: 'init',
  count: 0,
  render() {
    return `<p>${this.message} : ${this.count}</p>`
  };
};

function render(rootElement, component) {
  rootElement.innerHTML = component.render();
}

render(document.querySelector('#root'), component);

document.querySelector('#btn_plus').addEventListener('click', () => {
  component.message = 'update';
  component.count = component.count + 1;
  render(document.querySelector('#root'), component)
});
```

```js
const Component = props => {
  return React.createElement(
    'p', 
    null, 
    `${props.message} : ${props.count}`
  );
};
// react.createElement Api로 Component를 정의하는데, 
// <p null>`${props.message} : ${props.count}`</p>라는 요소를 만들도록 지정

ReactDOM.render(
  React.createElement(
    Component, 
    {message: 'init', count: 0}, 
    null
  ), 
  document.querySelector('#root')
);
// reactDOM.render로
// id가 root인 태그에 Component를 삽입하는데, <p null>`init : 0`</p>으로 삽입

document.querySelector('#btn_plus').addEventListener('click', () => {
  ReactDOM.render(
    React.createElement(
      Component,
      {message: "update", count: 10},
      null
    ),
    document.querySelector('#root')
  );
});
// id가 btn_plus인 요소를 클릭 시, root라는 id를 가진 태그를 <p null>`update : 10`</p>로 변경
```

```js
// component 만들는 법

// class 정의
class ClassComponent extends React.Component {
  render() {
    return <div>Hello</div>
  }
}

// class 사용
ReactDOM.render(
  <ClassComponent />,
  document.querySelector('#root')
);
```

```js
// function 정의
// 방법 1
function FunctionComponent() {
  return <div>Hello</div>;
};

// 방법 2 (ArrowFunc)
const FunctionComponent = () => {
  return <div>Hello</div>
};
// return만 있다면 return, 중괄호 생략가능
const FunctionComponent = () => <div>Hello</div>;

// 사용
ReactDOM.render(
  <FunctionComponent />,
  document.querySelector('#root')
);
```

### React.createElement
```js
React.createElement(
  type, // 태그, 이름, 문자열 | 리엑트 컴포넌트 | React.Fragment
  [props], // 리엑트 컴포넌트에 넣어주는 데이터 객체
  [...children] // 자식으로 넣어주는 요소들
);

// 1. 태그, 이름, 문자열 type
ReactDOM.render(
  React.createElement(
    'h1', 
    null, 
    `type이 "태그 이름 문자열"입니다`
  ), // createElement뿐만 아니라, 컴포넌트도 사용이 가능, 컴포넌트도 결국 createelement로 만들어짐
  document.querySelector('#root')
); 
// <div id="root"><h1 null>`type이 "태그 이름 문자열"입니다`</h1></div>

// 2. 리엑트 컴포넌트 type
// Component 변수 선언
const Component = () => {
  return React.createElement(
    'p', 
    null, 
    `type이 "리엑트 컴포넌트"입니다`
  );
};

ReactDOM.render(
  React.createElement(Component, null1, null2),
  document.querySelector('#root')
); 
// <Component null1>null2</Component> => <Component /> => <p null>`type이 "리엑트 컴포넌트"입니다`</p>

// 3. React.Fragment
ReactDOM.render(
  React.createElement(
    React.Fragment,
    null,
    `type이 "React.Fragment"입니다`
    `type이 "React.Fragment"입니다`
    `type이 "React.Fragment"입니다`
  ),
  document.querySelector("#root")
); // 하위로 들어가는 개념이 아닌, 배열 형태로 나타남
// <div id ="root">
// `type이 "React.Fragment"입니다`
// `type이 "React.Fragment"입니다`
// `type이 "React.Fragment"입니다`
// </div>

// 4. 복잡한 리엑트 엘리먼트
// 아래와 같이 html코드로 입력하면 js가 아니라서 오류가 발생하지만, babel을 통해 해결
// babel script 코드 넣어주고, <script type="text/babel">로 변경하면
// 자동으로 js(react)형식으로 만들어준다

ReactDOM.render(
  <div>
    <div>
      <h1>주제</h1>
      <ul>
        <li>React</li>
        <li>Vue</li>
      </ul>
    </div>
  </div>,
  document.querySelector("#root")
);
/*#__PURE__*/
// React.createElement("div", null, /*#__PURE__*/React.createElement("div", null, /*#__PURE__*/React.createElement("h1", null, "\uC8FC\uC81C"), /*#__PURE__*/React.createElement("ul", null, /*#__PURE__*/React.createElement("li", null, "React"), /*#__PURE__*/React.createElement("li", null, "Vue"))));

// tag, children, props -> div: tag, <h1>주제</h1>: children
// props: <div a= "a"></div>면 {a= "a"}형태로 컴포넌트에 데이터에 입력된다
```


### JSX 문법
```md
1. 최상위 요소가 하나여야 한다.
  <div>
    <div>
      <h1></h1>
    </div>
  </div>
  <div>
    <h1></h1>
  </div>
  불가능

2. 최상위 요소를 리턴하는 경우, ()로 감싸야 한다.
3. 자식들을 바로 렌더링하려면, <>자식들</>를 사용한다. => Fragment
  <>
  <div>
    <h1></h1>
  </div>
  </>

4. JS표현식을 사용하려면, {표현식}으로 사용한다.
  const title = "제목!"
  ReactDOM.render(
    <div>{ title }</div>,
    document.querySelector("#root")
  );

5. if문은 사용할 수 없고 대신, 삼항연산자 혹은 논리연산자(&&)를 사용한다.
6. style를 이용하여 인라인 스타일링 가능하다. => style라는 props사용
7. class 사용 불가, 대신 className를 사용하여 class를 적용할 수 있다.
8. 자식요소가 있으면, 닫아야한다.
  <p>hihihi</p>
```

## Props & State
```md
Props: 컴포넌트 외부에서 컴포넌트에게 주는 데이터
State: 컴포넌트 내부에서 변경할 수 있는 데이터
둘 다 변경이 발생하면, 렌더가 다시 일어날 수 있다

Render함수: props와 state를 바탕으로 컴포넌트를 그림, props와 state가 변경되면 다시 그림,
컴포넌트를 그리는 방법을 기술하는 함수가 render 함수이다.
```

### props
```js
// 함수로 Component를 생성하는 경우, 'props.'를 function의 인자에 넣어준다
function Component(props) {
  return (
    <div>
      <h1>{props.message} 이것은 함수로 만든 컴포넌트입니다.</h1>
    </div>
  );
}

// class에서는 props가 render의 인수로 들어가는게 아닌, 'this.props.'message로 표현
class Component extends React.Component {
  render() {
    return (
      <div>
        <h1>{this.props.message} 이것은 클레스로 만든 컴포넌트입니다.</h1>
      </div>
    );
  };
}

// Component 호출
ReactDOM.render(
  <Component message="안녕하세요!!!" />,
  document.querySelector("#root"),
);

// Component render 시 message값을 입력하지 않아도 기본값으로 출력되도록 초기값 설정
// 함수, 클래스 모두 적용 가능
Component.defaultProps = {
  message: "기본값",
};
Component.render(
  <Component />,
  document.querySelector("#root")
); 
// 기본값 이것은 클레스로 만든 컴포넌트입니다.

// class의 경우 기본값을 지정하는 법 (클래스에만 적용 가능)
// static defaultProps
class Component extends React.Component {
  render() {
    return (
      <div>
        <h1>{this.props.message}</h1>
      </div>
    );
  };
  static defaultProps = {
    message: "기본값 2"
  };
}
```

### state
```js
class Component extends React.Component {
  state = {
    count: 0,
  };
  // state는 객체데이터 형태로만 표현 가능
  render() {
    return (
      <div>
        <h1>{this.props.message} 이것은 클레스로 만든 컴포넌트이다.</h1>
        <p>{this.state.count}</p>
      </div>
    );
  };
  // message 초기값을 클래스 내부에 표현 가능
  static defaultProps = {
    message = "기본값",
  };
}


// setState: state값 변경을 통한 render 동작까지 
componentDidMount() {
  setTimeout(() => {
    // this.state.count = this.state.count + 1; -> 작동 안됨
    this.setState({
      count: this.state.count + 1,
    });
  }, 1000);
}

ReactDOM.render(
  <Component message="기본값 아님" />,
  document.querySelector("#root"),
);

// Class 표현 기본 방식 -> constructor, super
class Component extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      count: 0
    };
  }
  render() {
    return (
      <div>
        <h1>{this.props.message} 이것은 클레스로 만든 컴포넌트이다.</h1>
        <p>{this.state.count}</p>
      </div>
    );
  };
  static defaultProps = {
    message = "기본값",
  };
}
```

### Event Handling
```md
1. camelCase로만 사용 가능 -> onClick, onMouseEnter
2. 이벤트에 연결된 js 코드는 함수
  이벤트 = {함수}
3. 실제 DOM요소들에만 사용이 가능
  리엑트 컴포넌트에 사용시, 그냥 props로 전달됨
```
```js
class Component extends React.Component {
  state = {
    count: 0,
  };
  render() {
    return (
      <div>
        <p>{this.state.count}</p>
        <button
          onClick={() => {
            console.log("clicked");
            this.setState((state) => ({
              ...state, 
              count: state.count + 1,
            }));
          }}
        >
          클릭
        </button>
      </div>
    );
  };
}
// 작동 잘 됨

// 인라인 형식이 아닌, 별도의 메소드로 만든 코드
class Component extends React.Component {
  state = {
    count: 0,
  };
  render() {
    return (
      <div>
        <p>{this.state.count}</p>
        <button onClick={this.click}>
          클릭
        </button>
      </div>
    );
  };
  click () {
    console.log("clicked");
    this.setState((state) => ({
      ...state, 
      count: state.count + 1,
    }));
  };
}
// this가 bind되지 않아 에러 발생

class Component extends React.Component {
  state = {
    count: 0,
  };
  constructor(props) {
    super(props);
    this.click = this.click.bind(this);
    // this를 bind로 묶어준다
  }
  render() {
    return (
      <div>
        <p>{this.state.count}</p>
        <button onClick={this.click}>
          클릭
        </button>
      </div>
    );
  };
  click () {
    console.log("clicked");
    this.setState((state) => ({
      ...state, 
      count: state.count + 1,
    }));
  };
}
// 작동함

class Component extends React.Component {
  state = {
    count: 0,
  };
  render() {
    return (
      <div>
        <p>{this.state.count}</p>
        <button onClick={this.click}>
          클릭
        </button>
      </div>
    );
  }
  click = () => {
    console.log("clicked");
    this.setState((state) => ({
      ...state, 
      count: state.count + 1,
    }));
  };
}
// ArrowFunc 이용하면 bind를 위에서 해주지 않아도 됨
// ArrowFunc를 사용하길 권장

ReactDOM.render(<Component />, document.querySelector("#root"));
```

```md
initialization -> mounting(componentWillMount -> render -> componentDidMount) -> Updation(props&state 변경) -> unmounting(componentWillUnmount) -> 소멸
```

### React Router
```
npx install react-router-dom
```