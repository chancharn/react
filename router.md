### Single Page Application
```md
spa 라우팅 패키지 설치
npm i react-router-dom
```

```js
import { BrowserRouter, Route } from 'react-router-dom';
import Home from './pages/Home'
import Profile from './pages/Profile'
import About from './pages/About'

function App() {
  return (
    <BrowserRouter>
      <Route path="/" component={Home} />
      <Route path="/about" component={About} />
      <Route path="/profile" component={Profile} />
    </BrowserRouter>
  );
}

export default App;
```

```md
Route 컴포넌트의 경로(path)와 component를 설정하고 BrowserRouter 컴포넌트로 감싸준다.
브라우저에서 요청한 경로에 Route의 path가 있으면, component를 보여준다.

about 컴포넌트를 불러오기 위해 /about를 path에 입력하면,
path="/", path="/about" 이 두개의 컴포넌트(home, about)를 모두 불러오게 된다.
```

```js
<Route path="/" exact component={Home} />
// 위와 같이 exact를 입력해주면, '/'의 경우에만 home 컴포넌트를 불러오게 된다.
```

### 동적라우팅1
```md
/, /profile, /about와 같이 특정 path 뿐만 아니라, 동적으로 라우팅을 처리하는 경우
```

```js
function App() {
  return (
    <BrowserRouter>
      <Route path="/profile" exact component={Profile} />
      <Route path="/profile/:id" component={Profile} />
    </BrowserRouter>
  );
}
// 위와 같이 /:000으로 작성한 후 profile.jsx 파일에서 인자로 props를 입력하면, console창에 props값들이 나온다.
```

```js
// 크롬 브라우저 검사 창에서 match.params.id에 {name:id} 형태로 값이 있는 상태를 확인할 수 있는데, 여기서 id값을 사용하면 된다.
export default function Profile(props) {
  const id = props.match.params.id;
  console.log(id, typeof id);
  return (
    <div>
      <h2>Profile 입니다.</h2>,
      {id && <p>id는 {id}입니다.</p>}
    </div>
  );
}
// 여기서, id && 은 id가 존재하면 이라는 논리연산자이다.
```

### 동적라우팅2
```md
위와 다르게 /profile/:id 형태가 아닌,
/profile?name=mark 와 같은 query-string 
1번과 같은 {name:id}와 같은 객체 데이터 형식은 사용하기 용이하지만, 
2번과 같은 경우는 내장객체인 URLSearchParams를 통해 사용해야 한다.

단점은 키 값을 한번 가공하고 사용해야 하고, 코드를 모두 숙지하고 있어야 할 뿐아니라, 브라우저 내장객체라 호환되지 않는 브라우저도 있다.
```

```js
export default function About(props) {
  const searchParams = props.location.search;
  console.log(searchParams);
  const obj = new URLSearchParams(searchParams);
  console.log(obj.get("name"));
  return <div>About 입니다.</div>
}
```

```md
query-string로 해결 가능
npm i query-string -S
```

```js
import queryString from 'query-string';

export default function About(props) {
  const searchParams = props.location.search;
  console.log(searchParams);

  // URLSearchParams 방식
  // const obj = new URLSearchParams(searchParams);
  // console.log(obj.get("name"));

  const query = queryString.parse(searchParams);
  console.log(query);
  return (
    <div>
      <h2>About 입니다.</h2>,
      {query.name && <p>name은 {query.name}입니다.</p>}
    </div>
  )
}
```

### Switch Component
```md
Switch
여러 Route 중 순서대로 '먼저' 맞는 하나만 보여준다.
exact를 사용하지 않고 로직 구현 가능.
path가 맞지 않으면 보여지는 컴포넌트를 설정하여, Not Found 페이지를 만들 수 있다.
위에서 보듯, 먼저 맞는 하나만 보여주기 때문에
구체적인 path를 위에서부터 내림차순으로 작성해주어야 한다.
예를 들어,
<Route path='/' component={Home}>가 가장 위에 있다면,
Home 컴포넌트만 불러 옴.
```

```js
import { BrowserRouter, Route, Switch } from 'react-router-dom';
import Home from './pages/Home';
import Profile from './pages/Profile';
import About from './pages/About';
import NotFound from './pages/NotFound'

function App() {
  return (
    <BrowserRouter>
      <Switch>
        <Route path="/profile/:id" component={Profile} />
        <Route path="/profile" component={Profile} />
        <Route path="/about" component={About} />
        <Route path="/" exact component={Home} />
        {/* 구체적인 경로부터 내림차순으로 적어주되, Home 경로에는 exact를 넣어주어야 한다. */}
        <Route component={NotFound} />
      </Switch>
    </BrowserRouter>
  );
}

export default App;
```

```js
export default function NotFound() {
  return <div>페이지를 찾을 수 없습니다.</div>
}
```

### Link
```md
html의 a태그와 같은 역할을 하는 태그이지만, a태그를 이용하면 데이터를 다시 불러오는 반명, react component의 기능을 그대로 구현하여 데이터를 불러오지 않고 페이지가 이동이 가능하다.
```

```js
import { BrowserRouter, Route, Switch, Link } from 'react-router-dom';
import Home from './pages/Home';
import Profile from './pages/Profile';
import About from './pages/About';
import NotFound from './pages/NotFound'
import Links from './component/Links';

function App() {
  return (
    <BrowserRouter>
      <Links />
      <Switch>
        <Route path="/profile/:id" component={Profile} />
        <Route path="/profile" component={Profile} />
        <Route path="/about" component={About} />
        <Route path="/" exact component={Home} />
        {/* 구체적인 경로부터 내림차순으로 적어주되, Home 경로에는 exact를 넣어주어야 한다. */}
        <Route component={NotFound} />
      </Switch>
    </BrowserRouter>
  );
}

export default App;
```

```js
import { Link } from "react-router-dom";

export default function Links() {
  return (
    <ul>
      <li>
        <Link to ="/">Home</Link>
      </li>
      <li>
        <Link to ="/profile">Profile</Link>
      </li>
      <li>
        <Link to ="/profile/1">Profile/1</Link>
      </li>
      <li>
        <Link to ="/about">About</Link>
      </li>
      <li>
        <Link to ="/about?name=mark">About?name=mark</Link>
      </li>
    </ul>
  );
}
```

### Navigation Links
```md
import {NavLink} from 'react-router-dom'; 에서 import할 수 있음.
active 상태를 가지기 때문에 클래스이름, 스타일 등을 변경 가능.
Route의 path처럼 동작하기 때문에 exact가 있음.
```

```js
import { NavLink } from "react-router-dom";

const colorGr = { color : 'green' };

export default function NavLinks() {
  return (
    <ul>
      <li>
        <NavLink to ="/" exact activeStyle={colorGr}>Home</NavLink>
      </li>
      {/* exact를 넣어주지 않으면 루트 경로라서 다른 경로를 눌러도 색이 유지됨 */}
      <li>
        <NavLink to ="/profile" exact activeStyle={colorGr}>Profile</NavLink>
      </li>
      <li>
        <NavLink to ="/profile/1" activeStyle={colorGr}>Profile/1</NavLink>
        {/* exact를 넣지 않아도 된다. */}
      </li>
      <li>
        <NavLink 
          to ="/about" 
          activeStyle={colorGr} 
          inActive={(match, location) => {
            console.log(location);
            return match !== null && location.search === "";
          }}
        >
          About
        </NavLink>
        {/* 위의 profile와는 다르게 querystring가 구분이 되지 않기 때문에 따로 inActive라는 함수를 지정해 주어야 한다. */}
      </li>
      <li>
        <NavLink 
          to ="/about?name=mark" 
          activeStyle={colorGr}
          inActive={(match, location) => {
            return match !== null && location.search === "?name=mark";
          }}
        >
          About?name=mark
        </NavLink>
      </li>
    </ul>
  );
}
```

### js형식으로 라우팅 (Login페이지 & LoginButton컴포넌트 참고)
```js
import LoginButton from "../component/LoginButton";

// export default function Login(props) {
//   return (
//     <div>
//       <h2>Login 페이지입니다.</h2>
//       <LoginButton {...props} />
//       {/* props를 넣어주지 않으면, LoginButtom 컴포넌트에서 props.history.push...로 진행되는 명령을 받아오s함. */}
//     </div>
//   )
// }
// withRouter이라는 dom component를 사용하면 넣지 않아도 된다.

// withRouter 사용
export default function Login() {
  return (
    <div>
      <h2>Login 페이지입니다.</h2>
      <LoginButton />
    </div>
  )
}; 
```

```js
// export default function LoginButton(props) {
//   function login() {
//     setTimeout(() => {
//       props.history.push("/");
//     }, 1000);
//   }
//   return <button onClick={login}>로그인하기</button>
// };

// withRouter 사용
import { withRouter } from "react-router-dom";

export default withRouter (function LoginButton(props) {
  function login() {
    setTimeout(() => {
      props.history.push("/");
    }, 1000);
  }
  return <button onClick={login}>로그인하기</button>
});
```


### Redirect component
```js
import { Redirect } from 'react-router-dom';
//jsx
<Redirect to="/" />
```

```js
import { BrowserRouter, Route, Switch, Redirect } from 'react-router-dom';
import Home from './pages/Home';
import Profile from './pages/Profile';
import About from './pages/About';
import NotFound from './pages/NotFound'
// import Links from './component/Links';
import NavLinks from './component/NavLinks';
import Login from './pages/Login';

const isLogin = true;

function App() {
  return (
    <BrowserRouter>
      {/* <Links /> */}
      <NavLinks />
      <Switch>
        {/* <Route path="/login" component={Login} /> */}
        <Route path="/login" render={() => (isLogin ? <Redirect to="/" /> : <Login />)} />
        {/* isLogin이 참이면 Home으로 거짓이면 Login페이지로 */}
        <Route path="/profile/:id" component={Profile} />
        <Route path="/profile" component={Profile} />
        <Route path="/about" component={About} />
        <Route path="/" exact component={Home} />
        {/* 구체적인 경로부터 내림차순으로 적어주되, Home 경로에는 exact를 넣어주어야 한다. */}
        <Route component={NotFound} />
      </Switch>
    </BrowserRouter>
  );
}

export default App;
```