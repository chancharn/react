### SCSS
```md
css와는 다르게 클레스에 위계를 줌으로써, 스타일을 관리하는 방식
```
```md
npm i sass
```
> App.css
```css
.App {
  text-align: center;
}

.App-logo {
  height: 40vmin;
  pointer-events: none;
}

@media (prefers-reduced-motion: no-preference) {
  .App-logo {
    animation: App-logo-spin infinite 20s linear;
  }
}

.App-header {
  background-color: #282c34;
  min-height: 100vh;
  display: flex;
  flex-direction: column;
  align-items: center;
  justify-content: center;
  font-size: calc(10px + 2vmin);
  color: white;
}

.App-link {
  color: #61dafb;
}

@keyframes App-logo-spin {
  from {
    transform: rotate(0deg);
  }
  to {
    transform: rotate(360deg);
  }
}
```
> App.scss
```css
.App {
  text-align: center;

  .logo {
    height: 40vmin;
    pointer-events: none;
  }

  @media (prefers-reduced-motion: no-preference) {
    .App-logo {
      animation: App-logo-spin infinite 20s linear;
    }
  }

  .header {
    background-color: #282c34;
    min-height: 100vh;
    display: flex;
    flex-direction: column;
    align-items: center;
    justify-content: center;
    font-size: calc(10px + 2vmin);
    color: white;
  }

  .link {
    color: #61dafb;
  }

  @keyframes App-logo-spin {
    from {
      transform: rotate(0deg);
    }
    to {
      transform: rotate(360deg);
    }
  }
  
}
```
> App.js
```js
import logo from './logo.svg';
import './App.css';
import './App.scss';
```

### App.module.css/scss/sass
```js
// App.module.css 파일 생성 후 App.css에 있는 코드를 복사하고,

import style from './App.module.css';

console.log(style);
// 콘솔 창에 입력된 object를 확인해보면,

App: "App_App__B2Ebb"
App-header: "App_App-header__PyRwd"
App-link: "App_App-link__XPeK4"
App-logo: "App_App-logo__yMrik"
App-logo-spin: "App_App-logo-spin__1up6W"
// 위와 같이 key&value 형태로 값이 있는 것을 확인할 수 있다.
```

```js
function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>
        <a
          className="App-link"
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
    </div>
  );
}

// 위와 같이 기존에 전역적으로 className에 스타일 값을 지정하였다면, 아래와 같이 직관적으로 스타일 값을 js스타일로 지정할 수 있다.

function App() {
  return (
    <div className={style["App"]}>
      <header className={style["App-header"]}>
        <img src={logo} className={style["App-logo"]} alt="logo" />
        <p>
          Edit <code>src/App.js</code> and save to reload.
        </p>
        <a
          className={style["App-link"]}
          href="https://reactjs.org"
          target="_blank"
          rel="noopener noreferrer"
        >
          Learn React
        </a>
      </header>
    </div>
  );
}
```
> Button
```js
// Button.jsx
import styles from './Button.module.css';

const Button = (props) => <button className={styles["button"]} {...props} />;

export default Button;
```
```js
// Button.module.css
.button {
  background: transparent;
  border-radius: 3px;
  border: 2px solid palevioletred;
  color: palevioletred;
  margin: 0 1em;
  padding: 0.25em 1em;
  font-size: 20px;
}
```
```js
import Button from './components/Button';

function App() {
  return (
    <Button>버튼!</Button>
  );
}
```

> 버튼을 누르면 스타일이 바뀌었다가, 1초 후 돌아오게 동작
```js
// loading 클래스 스타일 입력함
import React from 'react';
import styles from './Button.module.css';

class Button extends React.Component {
  state = {
    loading : false,
  };

  render() {
    return (
      <button
        onClick={this.startLoading}
        className={
          this.state.loading
            ? `${styles["button"]} ${styles["loading"]}`
            // 두개 이상의 style를 적용하려면 클래스를 따로 입력해줘야하는데 템플릿스트링으로 한번에 표현
            : styles["button"]
            // loading가 true면 위 false면 아래라는 논리 연산자
        }
        {...this.props}
      />
    );
  }

  // 이벤트(onClick)에 bind된 함수는 ArrowFunc로 사용해야 함
  startLoading = () => {
    this.setState({
      loading: true,
    });
    setTimeout(() => {
      this.setState({
        loading: false,
      });
    }, 1000);
  };
}

export default Button;
```

> 위 코드가 너무 복잡하기 때문에 classNames라는 모듈로 해결
> classNames
```md
npm i classNames
```
```js
import classNames from 'classNames';

class Button extends React.Component {
  render() {
    console.log(classNames("foo", "bar"));
    console.log(classNames({foo: true}, {bar: false}));
    // foo만 출력
    console.log(classNames("bar", 1, 0, null, false, undefined, { bar: null }, ""));
    // "bar", 1을 제외한 나머지는 출력되지 않음
    console.log(classNames(styles["button"], styles["loading"]));
    // 위에서 말한 값형태로 출력이 됨
  }
}
export default Button;
```

> 더 간결하게
```js
import classNames from 'classNames/bind';

const cx = classNames.bind(styles);
console.log(cx('button', 'loading'));

class Button extends React.Component {
  state = {
    loading : false,
  };

  render() {

    const {loading} = this.state;

    return (
      <button
        onClick={this.startLoading}
        className={cx('button', {loading : loading})}
        // loading가 참이면 loading 클래스를 불러옴
        {...this.props}
      />
    );
  }

  startLoading = () => {
    this.setState({
      loading: true,
    });
    setTimeout(() => {
      this.setState({
        loading: false,
      });
    }, 1000);
  };
}

export default Button;
```

### Styled Components
> css, module.css가 아닌 별도의 라이브러리
```js
npm i styled-components
```
```js
import logo from './logo.svg';
// import './App.css';
// import './App.scss';
import styles from './App.module.css';
// import Button from './components/Button';
import StyledButton from './components/styledButton';

function App() {
  return (
    <div className={styles["App"]}>
      <header className={styles["App-header"]}>
        <img src={logo} className={styles["App-logo"]} alt="logo" />
        <p>
          <StyledButton>버튼</StyledButton>
        </p>
      </header>
    </div>
  );
}

export default App;
```
```js
import styled from 'styled-components';

const StyledButton = styled.button`
  background: transparent;
  border-radius: 5px;
  border: 2px solid palevioletred;
  color: palevioletred;
  margin: 0 1em;
  padding: 0.25em 1em;
  font-size: 20px;
`;

export default StyledButton;
```
```md
``사이에 css와 같은 스타일을 입힐 수 있음
```
> props를 지정하여 스타일주기
```js
import logo from './logo.svg';
// import './App.css';
// import './App.scss';
import styles from './App.module.css';
// import Button from './components/Button';
import StyledButton from './components/styledButton';

function App() {
  return (
    <div className={styles["App"]}>
      <header className={styles["App-header"]}>
        <img src={logo} className={styles["App-logo"]} alt="logo" />
        <p>
          <StyledButton>버튼</StyledButton>
          <StyledButton primary={true}>버튼</StyledButton>
        </p>
      </header>
    </div>
  );
}

export default App;
```
```js
import styled, { css } from 'styled-components';

const StyledButton = styled.button`
  background: transparent;
  border-radius: 5px;
  border: 2px solid palevioletred;
  color: palevioletred;
  margin: 0 1em;
  padding: 0.25em 1em;
  font-size: 20px;

  ${props => props.primary && 
    css`
    background: palevioletred;
    color: white;
  `}
`;
// props 표현식은 함수 형태

export default StyledButton;
```

```js
// App.js에서 상속받아 사용
import logo from './logo.svg';
import styles from './App.module.css';
import StyledButton from './components/styledButton';
import styled from 'styled-components';

const PrimaryStyledButton = styled(StyledButton)`
  background: palevioletred;
  color: blue;
`;

function App() {
  return (
    <div className={styles["App"]}>
      <header className={styles["App-header"]}>
        <img src={logo} className={styles["App-logo"]} alt="logo" />
        <p>
          <StyledButton>버튼</StyledButton>
          <StyledButton primary={true}>버튼</StyledButton>
          <PrimaryStyledButton>버튼</PrimaryStyledButton>
        </p>
      </header>
    </div>
  );
}

export default App;
```

```js
import logo from './logo.svg';
import styles from './App.module.css';
import StyledButton from './components/StyledButton';
import styled, { createGlobalStyle } from 'styled-components';
import StyledA from './components/StyledA';

const PrimaryStyledButton = styled(StyledButton)`
  background: palevioletred;
  color: blue;
`;

const UppercaseButton = (props) => (<button {...props} children={props.children.toUpperCase()} />);

const MyButton = (props) => (
  <button className={props.className} children={`MyButton ${props.children}`} />
);

const StyledMyButton = styled(MyButton)`
  background: transparent;
  border-radius: 5px;
  border: 2px solid ${props => props.color || 'palevioletred'};
  color: ${props => props.color || 'palevioletred'};
  margin: 0 1em;
  padding: 0.25em 1em;
  font-size: 20px;

  :hover {
    border: 2px solid red;
  }

  ::before {
    content: '@';
  }
`;

// 위에서는 하나하나 조작을 했다면, 전역으로 조작도 가능하다.
const GlobalStyle = createGlobalStyle`
button {
  color: yellow;
}
`;

function App() {
  return (
    <div className={styles["App"]}>
      <GlobalStyle />
      <header className={styles["App-header"]}>
        <img src={logo} className={styles["App-logo"]} alt="logo" />
        <p>
          <StyledButton>버튼</StyledButton>
          <StyledButton primary={true}>버튼</StyledButton>
          <PrimaryStyledButton>버튼</PrimaryStyledButton>
          <StyledButton as="a" href="/">버튼</StyledButton>
          {/* as: a태그를 사용하겠다는 의미, a자리에는 특정 태그 뿐만 아니라 컴포넌트도 입력이 가능 */}
          <StyledButton as={UppercaseButton}>button</StyledButton>
          <StyledMyButton color="green">button</StyledMyButton>
          <StyledA href="https://google.com">태그</StyledA>
        </p>
      </header>
    </div>
  );
}

export default App;
```
```js
import styled from 'styled-components';

const StyledA = styled.a.attrs(props => ({
  target: '_BLANK',
}))`
  color: ${(props) => props.color};
`;

// 링크 클릭 시 새창이 뜨게 하는 target와 같은 기능을 컴포넌트 내 attrs에서 미리 작업을 해줄 수 있다.

export default StyledA;
```

### Ant Design
```md
npm i antd
```

```js
import 'antd/dist/antd.css'; 
// 전역 스타일을 index.js에 import './index.css'; 바로 윗줄에 추가
```

```
원하는 작업 코드 App.js에 복붙
```

```js
// 아이콘 패키지를 따로 설치해줘야 함
npm install --save @ant-design/icons
```

```js
import logo from './logo.svg';
import './App.css';
import { Calendar } from 'antd';
import { HighlightOutlined } from '@ant-design/icons';

function App() {
  return (
    <div className="App">
      <header className="App-header">
        <img src={logo} className="App-logo" alt="logo" />
        <p>
        <HighlightOutlined />
        </p>
        <Calendar fullscreen={false} />
      </header>
    </div>
  );
}

export default App;
```

```js
import { Row, Col } from 'antd';

const colStyle = () => ({
  height: 50,
  backgroundColor: 'red',
  opacity: Math.round(Math.random() * 10) / 10,
});

function App() {
  return (
    <div className="App">
      <Row
        style={{
          height: 300,
        }} 
        justify="start"
        align="top"
      >
        <Col span={8}/>
        <Col span={8} offset={8} />
      </Row>
      <Row gutter={16}>
        <Col span={8} style={colStyle()} />
        <Col span={8} style={colStyle()} />
        <Col span={8} style={colStyle()} />
      </Row>
      <Row>
        <Col span={6} style={colStyle()} />
        <Col span={6} style={colStyle()} />
        <Col span={6} style={colStyle()} />
        <Col span={6} style={colStyle()} />
      </Row>
    </div>
  );
}

export default App;

// Ant Design은 한 줄을 24개의 칸으로 나눈다 그래서 col span 값을 모두 더하면 24
// <Col span={24중에 어느정도 차지할지 정하는 수} />
// <Row gutter={16 + 8*n의 정수} /> -> 최소 16부터 시작 나누는 줄의 두께 px
// <Col offset={24중 건너띄고 싶은 정수} />
// justify="좌우 정렬"
// align="위아래 정렬"
```