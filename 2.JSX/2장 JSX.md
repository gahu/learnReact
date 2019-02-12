# 코드의 이해
```JavaScript
import React, { Component } from 'react';
import logo from './logo.svg';
import './App.css';

class App extends Component {
    render() {
        return (
            <div className="App">
                <div className="App-header">
                    <img src={logo} className="App-logo" alt="logo" />
                    <h2>Welcome to React</h2>
                </div>
                <p className="App-intro">
                    To get started, edit <code>src/App.js</code> and save to reload.
                </p>
            </div>
        );
    }
}

export default App;
```

```JavaScript
import React, { Component } from 'react';
```
Node.js의 주요 특징은 코드를 모듈화하여 재사용할 수 있다는 점입니다.
Node.js 자체적으로 내장된 모듈도 많고, 다른 사람이 만든 모듈도 npm으로 쉽게 찾아서 적용할 수 있기에 Node.js를 더욱더 강력하게 만들어 주는 특징이 있습니다.
이렇게 설치된 모듈들을 js 파일에서 불러와 사용할 수 있는데, 이때 사용하는 코드는 다음과 같습니다.
```JavaScript
var fs = require('fs');
```
fs는 Node.js 내장 모듈 중 파일을 읽고 쓸 때 사용하는 모듈입니다.
ES6(ECMAScript6)에서는 모듈을 불러오는 import라는 키워드를 사용합니다.
```JavaScript
import fs from 'fs';
```

이렇게 파일을 모듈화해서 사용하는 것은 Node.js의 기능입니다. 웹 브라우저에서 사용하는 자바스크립트는 Node.js 런타임으로 실행하는 것이 아니기 때문에 자체적으로 이 기능을 지원하지 않습니다.
이때, 번들링(bundling) 도구를 이용합니다.
번들링 도구는 Browserify, RequireJS, webpack 등이 대표적입니다. 각 도구마다 특성이 다르며, 리액트 프로젝트에서는 주로 webpack을 사용하는 추세입니다. 이는 편의성과 확장성 면에서 다른 도구보다 뛰어나기 때문입니다.
![img](http://www.pro-react.com/materials/appendixA/images/picture2.png)
이런 번들링 도구를 사용하면, require(또는 import)로 모듈을 불러왔을 때 번들링되면서 모듈들을 파일 하나로 합쳐 줍니다.

```JavaScript
import logo from './logo.svg';
import './App.css';
```
위와 같이 SVG와 CSS 파일도 webpack으로 불러올 수 있습니다.
이렇게 파일들을 불러오는 것은 webpack의 로더(loader)가 담당합니다.
css-loader는 CSS 파일을 불러올 수 있고,
file-loader는 웹 폰트나 미디어 파일 등을 불러올 수 있습니다.
babel-loader는 js 파일들을 불러오면서 ES6로 작성된 코드를 ES5문법으로 변환해 줍니다.
```
ES6 코드를 변환하는 이유?
ECMAScript6를 이전 자바스크립트 문법인 ES5 형태로 변환하는 이유는 구 번전 웹 브라우저와 호환하기 위해서입니다.
현재 대부분의 최신 웹 브라우저에서는 자체적으로 ES6 코드를 실행할 수 있지만, 구 버전 웹 브라우저에서는 실행하지 않습니다.
또 Node.js LTS 버전도 이 문법을 지원하지 않기 때문에, import를 제대로 작동하려고 바벨을 사용하여 ES5로 변환하는 것입니다.
ES6는 새로 업데이트한 문법이지만, 그렇다고 해서 자체적으로 완전히 새로운 기능이 생긴 것은 아닙니다. ES5 문법을 사용하여 비슷하게 작동하도록 구현할 수 있습니다.
```
로더는 원래 직접 설치하고 설정해야 하지만, create-react-app이 이 귀찮은 작업들을 대신하는 것입니다. (node_modules/react-script)

```JavaScript
class App extends Component
```
이 코드는 App이라는 클래스를 선언합니다.
이 클래스는 리액트 라이브러리 내부에 있는 Component 클래스를 상속합니다.
```JavaScript
render() {
    return (
        <div className="App">
            <div className="App-header">
                <img src={logo} className="App-logo" alt="logo" />
                <h2>Welcome to React</h2>
            </div>
            <p className="App-intro">
                To get started, edit <code>src/App.js</code> and save to reload.
            </p>
        </div>
    );
}
```
이 함수 내부에서는 컴포넌트를 유저에게 어떻게 보일지 return합니다. 함수 내부의 이런 코드를 JSX라고 합니다.

# JSX란?
JSX는 자바스크립트의 확장 문법으로 XML과 매우 비슷하게 생겼습니다. 이런 형식으로 작성한 코드는 나중에 코드가 번들링되면서 babel-loader를 사용하여 자바스크립트로 변환합니다.
```JavaScript
var a = (
    <div>
        <h1>Awesome <b>React</b></h1>
    </div>
)
```
이 코드를 바벨로 변환하면
```JavaScript
var a = React.createElement(
    "div",
    null,
    React.Component(
        "h1",
        null,
        "Awesome ",
        React.createElement(
            "b",
            null,
            "React",
        )
    )
);
```
이런 식으로 변환하면서 컴포넌트를 트리 구조의 객체로 정의할 수 있습니다.
```
JSX도 ES6 문법인가?
JSX는 리액트용이기 때문에 공식 자바스크립트 문법은 아닙니다.
바벨이 이를 변화해 주기는 하지만, 바벨에서 여러 문법을 지원할 수 있도록 preset이란 것을 설정합니다. ES6에는 babel-preset-es2015를 적용하며, JSX를 지원하려고 babel-preset-react를 적용하는 것입니다.
아직 공식적으로 릴리스한 것은 아니지만 차기 문법인 ES2016(ES7) 문법들도 preset들을 받아서 사용할 수 있습니다.
```

# JSX 장점
### 보기 쉽고 익숙하다
결국 HTML 코드를 작성하는 것과 비슷하기 때문에 가독성도 높고 작성하기도 편합니다.
### 오류 검사
JSX에 오류가 있다면, 바벨이 코드를 변환하는 과정에서 이를 감지해 냅니다.
### 더욱 높은 활용도
JSX에서 우리가 알고 있는 div나 span 같은 HTML 태그를 사용할 수 있을 뿐만 아니라, 컴포넌트도 JSX 안에서 작성할 수 있습니다.
```JavaScript
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';

ReactDOM.render(<App />, document.getElementById('root'));
```
src/index.js 에서는 App 컴포넌트를 마치 HTML 태그 쓰듯이 작성합니다.
```
ReactDOM.render는 무엇을 하는가?
이 코드는 컴포넌트를 페이지레 렌더링하는 역할을 하며, react-dom 모듈을 불러와 사용할 수 있습니다.
이 함수의 첫 번째 파라미터에는 페이지에 렌더링할 내용을 JSX 형태로 작성하고,
두 번째 파라미터에는 해당 JSX를 렌더링할 document 내부 요소를 설정합니다.
여기서는 id가 root인 요소 안에 렌더링을 하도록 설정했는데, 이 요소는 public/index.html 파일을 열어 보면 있습니다.
```

# JSX 문법
JSX는 정말 편리한 문법이지만, 올바르게 사용하려면 몇 가지 규칙을 준수해야 합니다.
 - 감싸인 요소
 - 자바스크립트 표현
 - if문 대신 조건부 연산자
 - &&를 사용한 조건부 렌더링
 - 인라인 스타일링
 - class 대신 className
 - 꼭 닫아야 하는 태그
 - 주석

### 감싸인 요소
컴포넌트에 여러 요소가 있다면 부모 요소 하나로 꼭 감싸야 합니다.
```JavaScript
import React, { Component } from 'react';

class App extends Component {
    render() {
        return (
            <h1>리액트!</h1>
            <h2>어썸~!</h2>
        );
    }
}

export default App;
```
>Failed to compile
>./src/App.js
>Syntax error: Adjacent JSX elements must be wrapped in an enclosing tag (9:6)

라고 뜰겁니다.
이 오류는 부모 요소 하나를 작성함으로써 해결할 수 있습니다.
```JavaScript
render() {
    return (
        <div>
            <h1>리액트!</h1>
            <h2>어썸~!</h2>
        </div>
        );
    }
}
```
이는 Virtual DOM에서 컴포넌트 변화를 감지해 낼 때 효율적으로 비교할 수 있도록 컴포넌트 내부는 DOM 트리 구조 하나여야 한다는 규칙이 있기 때문입니다.

##### Fragment
리액트 v16 이상에서는 Fragment 컴포넌트가 도입되었습니다. div 같은 것으로 감싸지 않고 여러 요소를 렌더링하고 싶다면 리액트를 불러올 때 Component와 함께 Fragment를 불러와서 다음과 같이 사용합니다.
```JavaScript
import React, { Component, Fragment } from 'react';

class App extends Component {
    render() {
        return (
            <Fragment>
                <h1>리액트!</h1>
                <h2>어썸~!</h2>
            </Fragment>
        );
    }
}

export default App;
```

### 자바스크립트 표현
JSX가 단순히 DOM 요소를 렌더링하는 기능밖에 없었다면 뭔가 좀 아쉬웠을 것입니다.
JSX 안에서는 자바스크립트 표현식을 쓸 수 있습니다. 자바스크립트 표현식을 작성하려면 JSX 내부에서 코드를 { }로 감싸면 됩니다.
```JavaScript
import React, { Component, Fragment } from 'react';

class App extends Component {
    render() {
        const text = "어썸~!";
        return (
            <Fragment>
                <h1>리액트!</h1>
                <h2>{text}</h2>
            </Fragment>
        );
    }
}

export default App;
```
```
ES6의 const와 let
const는 ES6 문법에서 새로 도입한 것으로 한번 지정하고 나면 변경이 불가능한 상수를 선언할 때 사용하는 키워드고,
let은 동적인 값을 담을 수 있는 변수를 선언할 때 사용하는 키워드입니다.
ES6 이전에는 값을 담는 데 var 키워드를 사용했는데, var 키워드는 scope(해당 값을 사용할 수 있는 코드 영역)가 함수 단위입니다.
function myFunction() {
    var a = "hello";
    if(true) {
        var a = "bye";
        console.log(a); // bye
    }
    console.log(a); // bye
}
myFunction();
if문 바깥에서 var 값을 hello로 선언하고, if문 내부에서 bye로 설정했습니다.
if문 내부에서 선언했음에도 if문 밖에서 a를 조회하면 변경된 값이 나타납니다.

이런 결점을 해결해 주는 것이 바로 let과 const입니다.
function myFunction() {
    let a = 1;
    if(true) {
        let a = 2;
        console.log(a); // 2
    }
    console.log(a); // 1
}
myFunction();
let과 const는 scope가 함수 단위가 아닌 블록 단위이므로, if문 내부에서 선언한 a 값을 if문 밖의 a 값을 변경하지 않습니다.

let과 const를 사용할 때 같은 블록 내부에서 중복 선언이 불가능하다는 점에 주의하세요.
let a = 1;
let a = 2; // error: Uncaught SyntaxError: Identifier 'a' has already been declared.
그리고 const는 한번 선언하면 재설정할 수 없습니다.
const b = 1;
b = 2; // Uncaught TypeError: Assignment to constant variable.

기본적으로 const를 사용하고, 해당 값을 바꾸어야 할 때는 let을 사용하면 되겠습니다.
```

### if문 대신 조건부 연산자
JSX 내부의 자바스크립트 표현식에서 if문을 사용할 수는 없습니다.
하지만 조건에 따라 다른 것을 렌더링해야 할 때는 JSX 밖에서 if문을 사용하여 작업하거나, {} 안에 조건부(삼항) 연산자를 사용하면 됩니다.
```JavaScript
import React, { Component } from 'react';

class App extends Component {
    render() {
        const text = "어썸~!";
        const condition = true;
        return (
            <div>
                <h1>리액트!</h1>
                <h2>{text}</h2>
                {
                    condition ? '참' : '거짓';
                }
            </div>
        );
    }
}

export default App;
```

### &&를 사용한 조건부 렌더링
단순히 특정 조건을 만족할 때는 보여 주고, 만족하지 않을 때는 보여 주고 싶지 않다면
```JavaScript
{ condition ? '보여주기' : null }
{ condition && '보여주기' }
```
의 코드는 동일하게 작동합니다.

### 인라인 스타일링
리액트에서 DOM 요소에 스타일을 적용할 때는 문자열 형태로 적용할 수 없습니다.
그 대신 CSS 스타일을 자바스크립트 객체 형식으로 만들어 적용해야 합니다.
해당 객체에서 key는 camelCase로 작성합니다.
```JavaScript
import React, { Component } from 'react';

class App extends Component {
    render() {
        const text = "어썸~!";
        const condition = true;
        const style = {
            backgroundColor: 'gray',
            border: '1px solid black',
            height: Math.round(Math.random() * 300) + 50,
            width: Math.round(Math.random() * 300) + 50,
            WebkitTransition: 'all',
            MozTransition: 'all',
            msTransition: 'all'
        };

        return (
            <div>
                <h1>리액트!</h1>
                <h2>{text}</h2>
                { condition && '보여주기' }
                <div style={style}></div>
            </div>
        );
    }
}

export default App;
```
자바스크립트 객체 key에서는 '-'를 사용할 수 없으므로, background-color는 backgroundColor로 바꾸어서 작성합니다.
하지만 -ms는 예외로 대문자로 바꾸지 않고 소문자 ms로 작성합니다.

### class 대신 className
요소에 class를 지정해 보겠습니다.
```css
.my-div {
    background-color: aqua;
    font-size: 15px;
}
```
그 다음 최상위 요소에 class를 설정해 봅니다.
```JavaScript
import React, { Component } from 'react';

class App extends Component {
    render() {
        (...)
        return (
            <div class="my-div">
                <h1>리액트!</h1>
                <h2>{text}</h2>
                { condition && '보여주기' }
                <div style={style}></div>
            </div>
        );
    }
}

export default App;
```
이렇게 작성했을 때 스타일이 입혀지기는 하지만, 웹 브라우저의 콘솔 창을 확인해 보면 "Warning: Invaild DOM property \`class\`. Did you mean \`className\`?"이라는 경고 메세지를 표시할 것입니다. class 키워드는 이미 자바스크립트에 존재하는 키워드이기 때문입니다.
추가로 웹 브라우저의 DOM API에서도 자바스크립트로 클래스를 설정할 때 className 키워드를 사용합니다.
```JavaScript
<div className="my-div">
```

### 꼭 닫아야 하는 태그
JSX에서는 언제나 코드를 닫아 주어야 합니다.
```JavsScript
<form>
    First name: <br/>
    <input type="text" name="firstname"/><br/>
    Last name: <br/>
    <input type="text" name="lastname"/>
</form>
```

### 주석
일반적으로 주석을 작성할 때는 {\/* 이런 형식 \*/} 마치 자바스크립트 표현식을 사용할 때 처럼 작성합니다. 일반 자바스크립트에서 주석을 작성할 때처럼 아무 데나 // 이런 주석이나 /* 이런 주석 */ 으로 작성한다면 그 주석은 페이지에 고스란히 렌더링됩니다.
하지만 self-closed에서 />를 다음 줄에 작성할 때는 그 사이에서 주석을 {} 없이 작성할 수 있습니다.