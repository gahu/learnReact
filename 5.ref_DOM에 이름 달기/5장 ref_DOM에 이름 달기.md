일반 HTML에서 DOM 요소에 이름을 달 때는 id를 사용합니다.
```
<div id="my-element"></div>
```
특정 DOM 요소에 어떤 작업을 해야 할 때 이렇게 요소에 id를 달면 css에서 특정 id에 특정 스타일을 적용하거나 자바스크립트에서 해당 id를 가진 요소를 찾아서 작업할 수 있습니다.<br>
리액트 프로젝트에서 사용하는 public/index.html 파일에도 id가 root인 div 요소가 있습니다.

public/index.html
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <link rel="shortcut icon" href="%PUBLIC_URL%/favicon.ico">
    <title>React App</title>
</head>
<body>
    <div id="root"></div>
</body>
</html>
```
그리고 src/index.js 파일 중에는 id가 root인 요소에 리액트 컴포넌트를 렌더링하라는 코드가 있습니다.
```
(...)
ReactDOM.render(<App />, document.getElementById('root'));
```
이렇게 HTML에서 id를 사용하여 DOM에 이름을 다는 것처럼 리액트 프로젝트 내부에서 DOM에 이름을 다는 방법이 있습니다.<br>
바로 ref(reference의 줄임)개념입니다.

TIP
> **리액트 컴포넌트 안에서는 id를 사용하면 안되는가?**<br>
> 리액트 컴포넌트 안에서도 id를 사용할 수는 있습니다. JSX 안에서 DOM에 id를 달면 해당 DOM을 렌더링할 때 그대로 전달됩니다. 하지만 특수한 경우가 아니면 사용을 권장하지 않습니다.<br>
> 예를 들어 같은 컴포넌트를 여러 번 사용한다고 가정할 때, HTML에서 DOM의 id는 유일(unique)해야 하는데, 이런 상황에서는 중복 id를 가진 DOM이 여러 개 생기니 잘못된 사용입니다.<br>
> ref는 전역적으로 작동하지 않고 컴포넌트 내부에서만 작동하기 때문에 이런 문제가 생기지 않습니다.<br>
> 대부분은 id를 사용하지 않고도 원하는 기능을 구현할 수 있지만, 다른 라이브러리나 프레임워크와 함께 id를 사용해야 하는 상황이 발생할 수 있습니다. 이런 상황에서는 컴포넌트를 만들 때마다 id 뒷부분에 추가 텍스트를 붙여서(ex: button01 button02 button03 ...) 중복 id가 발생하는 것을 방지해야 합니다.

# ref는 어떤 상황에서 사용해야 하나?
정답은 "DOM을 꼭 직접적으로 건드려야 할 때" 입니다. <br>
예를 들어 일반 순수 자바스크립트 및 jQuery로 만든 웹 사이트에서 input을 검증할 때는 다음과 같이 특정 id를 가진 input에 클래스를 설정해 줍니다.

일반 HTML 예제코드(http://jsbin.com/qawucezuci/edit)
```html
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width">
    <title>Example</title>
    <style>
        .success {
            background-color: green;
        }

        .failure {
            background-color: red;
        }
    </style>
    <script>
        function validate() {
            var input = document.getElementById('password');
            input.className = '';
            if(input.value === '0000') {
                input.className = 'success';
            } else {
                input.className = 'failure';
            }
        }
    </script>
</head>
<body>
    <input type="password" id="password" />
    <button onclick="validate()">Validate</button>
</body>
</html>
```

하지만 리액트에 이런 작업은 굳이 DOM에 접근하지 않아도 state로 구현할 수 있습니다.
> ValidationSample 컴포넌트 만들기 -> input에 ref 달기 -> 버튼을 누를 때마다 input에 포커스 추가

### 예제 컴포넌트 생성
/src 디렉터리 안에 ValidationSample.css와 ValidationSample.js를 만든다.
```css
.success {
    background-color: lightgreen;
}

.failure {
    background-color: lightcoral;
}
```
```JavaScript
import React, { Component } from 'react';
import './ValidationSample.css'

class ValidationSample extends Component {
    state = {
        password: '',
        clicked: false,
        validated: false
    }

    handleChange = (e) => {
        this.setState({
            password: e.target.value
        });
    }

    handleButtonClick = () => {
        this.setState({
            clicked: true,
            validated: this.state.password === '0000'
        })
    }

    render() {
        return (
            <div>
                <input
                    type="password"
                    value={this.state.password}
                    onChange={this.handleChange}
                    className={this.state.clicked ? (this.state.validated ? 'success' : 'failure') : ''}>
                </input>
                <button onClick={this.handleButtonClick}>검증하기</button>
            </div>
        );
    }
}

export default ValidationSample;
```
input에서는 onChange 이벤트가 발생하면 handleChange를 호출하여 state의 password 값을 업데이트 합니다. button에서는 onClick 이벤트가 발생하면 handleButtonClick을 호출하여 clicked 값을 참으로 설정, validated 값을 검증 결과로 설정합니다.<br>
input의 className 값은 버튼을 누르기 전에는 비어 있는 문자열을 전달하며, 버튼을 누른 후에는 검증 결과에 따라 success 값 또는 failure 값을 설정합니다. 그리고 이 값에 따라서 input 색생이 초록색 또는 빨간색으로 나타납니다.

### App 컴포넌트에서 예제 컴포넌트 렌더링
App.js
```JavaScript
import React, { Component } from 'react';
import ValidationSample from './ValidationSample'

class App extends Component {
    render() {
        return (
            <div>
                <ValidationSample/>
            </div>
        );
    }
}

export default App;
```

### DOM을 꼭 사용해야 하는 상황
가끔 state만으로 해결할 수 없는 기능이 있습니다.
 - 특정 input에 포커스 주기
 - 스크롤 박스 조작하기
 - Canvas 요소에 그림 그리기 등

이때는 어쩔 수 없이 DOM에 직접적으로 접근해야 하는데, 바로 ref를 사용합니다.

# ref 사용
### 사용법
ref를 달아야 하는 DOM에 ref 속성을 추가할 때는 props를 설정하듯이 하면 됩니다. ref 값으로는 콜백 함수를 전달합니다. 콜백 함수는 ref를 파라미터로 가지며, 콜백 함수 내부에서 컴포넌트의 멤버 변수에 ref를 담는 코드를 작성합니다.
```JavaScript
<input ref={(ref) => {this.input=ref}}></input>
```
이렇게 하면 앞으로 this.input은 input 요소의 DOM을 가리킵니다. ref 이름은 자유롭게 지정합니다. DOM 타입과 관계없이 this.superman = ref 처럼 마음대로 지정하면 됩니다.

### 적용
기존 코드는 버튼을 누르면 포커스가 버튼으로 넘어가면서 왼쪽 input 요소의 텍스트 커서가 더 이상 보이지 않습니다.<br>
버튼을 한 번 눌렀을 때, 포커스가 다시 input쪽으로 자동으로 넘어가도록 수정합니다.

##### input에 ref 달기
```JavaScript
<input
    ref={(ref) => this.input=ref}
    (...)
/>
```

##### 버튼 onClick 이벤트 코드 수정
버튼에서 onClick 이벤트가 발생할 때 input에 포커스를 주도록 수정합니다. <br>
이제 this.input이 컴포넌트 내부의 input 요소를 가리키고 있으니, 일반 DOM을 다루듯이 코드를 작성하면 됩니다.
```JavaScript
handleButtonClick = () => {
    this.setState({
        clicked: true,
        validated: this.state.password === '0000'
    });
    this.input.focus();
}
```

# 컴포넌트에 ref 달기
리액트에서는 컴포넌트에도 ref를 달 수 있습니다.<br>
이 방법은 주로 컴포넌트 내부에 있는 DOM을 컴포넌트 외부에서 사용할 때 씁니다. 컴포넌트에 ref를 다는 방법은 DOM에 ref를 다는 방법과 똑같습니다.

### 사용법
```
<MyComponent
    ref={(ref) => {this.myComponent=ref}}
/>
```
이렇게 하면 MyComponent 내부의 메서드 및 멤버 변수에도 접근할 수 있습니다. 즉, 내부의 ref에도 접근할 수 있습니다. (예: myComponent.handleClick, myComponent.input 등)

이번에는 스크롤 박스가 있는 컴포넌트를 하나 만들고, 스크롤바를 아래로 내리는 작업을 부모 컴포넌트에서 실행해 보겠습니다.
> ScrollBox 컴포넌트 만들기 -> 컴포넌트에 ref 달기 -> ref를 이용하여 컴포넌트 내부 메서드 호출하기

### 컴포넌트 초기 설정
##### 컴포넌트 파일 생성
ScrollBox.js
```JavaScript
import React, { Component } from 'react';

class ScrollBox extends Component {
    render() {
        const style = {
            border: '1px solid black',
            height: '300px',
            width: '300px',
            overflow: 'auto',
            position: 'relative'
        };

        const innerStyle ={
            width: '100%',
            height: '650px',
            background: 'linear-gradient(white, black)'
        }

        return (
            <div
                style={style}
                ref={(ref) => {this.box=ref}}>
                <div style={innerStyle}/>
            </div>
        );
    }
}

export default ScrollBox;
```

##### App 컴포넌트에서 스크롤 박스 컴포넌트 렌더링
App.js
```JavaScript
import React, { Component } from 'react';
import ScrollBox from 'ScrollBox'

class App extends Component {
    render() {
        return (
            <div>
                <ScrollBox/>
            </div>
        );
    }
}

export default App;
```

##### 컴포넌트에 메서드 생성
컴포넌트에 스크롤바를 맨 아래쪽으로 내리는 메서드를 만듭니다.
자바스크립트로 스크롤바를 내릴 때는 DOM 노드가 가진 다음 값들을 사용합니다.
 - scrollTop: 세로 스크롤바 위치(0~350)
 - scrollHeight: 스크롤 박스 내부의 높이(650)
 - clientHeight: 스크롤 박스 외부의 높이(300)

스크롤바를 맨 아래쪽으로 내리려면 scrollHeight에서 clientHeight 높이를 빼줍니다.
```JavaScript
scrollToBottom = () => {
    const { scrollHeight, clientHeight } = this.box;
    /* 앞 코드에서는 비구조화 할당 문법을 사용했습니다.
    다음 코드와 같은 의미입니다.
    const scrollHeight = this.box.scrollHeight;
    const clientHeight = this.box.clientHeight;
    */
   this.box.srollTop = scrollHeight - clientHeight;
}
```
ES6의 비구조화 할당(destructuring assignment) 문법을 사용했습니다.

```
ES6의 비구조화 할당 문법
비구조화 할당 문법은 객체에서 특정 값을 추출하여 따로 레퍼런스를 만들 때 유용합니다. 기본 사용법은 다음과 같습니다.
const object = {
    foo: 1,
    bar: 2
};

const { foo, bar } = object;
console.log(foo, bar); // 1 2

이런 문법은 함수의 파라미터를 설정할 때도 동일하게 적용할 수 있습니다.
const object = {
    foo: 1,
    bar: 2
};

function print({foo, bar}) {
    console.log(foo, bar);
}

print(object); // 1 2

비구조화 할당 문법은 리액트 컴포넌트에서 state나 props를 참조할 때 자주 사용합니다. 주로 코드의 가독성과 편리함 때문에 사용하며, 무조건 사용해야 하지는 않습니다.

예를 들어 다음과 같이 render 함수가 있다고 가정하면
render() {
    return (
        <div>{this.props.name}</div>
        <div>{this.props.number}</div>
    )
}
props를 참조할 때마다 this.props를 반복해서 입력해야 한다는 점이 귀찮을 수도 있습니다. 이때 비구조화 할당 문법을 사용하면 다음과 같이 작성할 수 있습니다.
render() {
    return (
        <div>
            <div>{name}</div>
            <div>{number}</div>
        </div>
    )
}
```
이렇게 만든 메서드는 부모 컴포넌트인 App 컴포넌트에서 ScrollBox에 ref를 달면 사용할 수 있습니다.

### 컴포넌트에 ref 달고 내부 메서드 사용
App.js
```JavaScript
import React, { Component } from 'react';
import ScrollBox from './ScrollBox'

class App extends Component {
    render() {
        return (
            <div>
                <ScrollBox ref={(ref) => this.scrollBox=ref}/>
                <button onClick={() => this.scrollBox.scrollToBottom()}>
                    맨 밑으로
                </button>
            </div>
        );
    }
}

export default App;
```
여기에서 주의할 점이 하나 있는데, 문법상으로는 onClick = {this.scrollBox.scrollBottom} 같은 형식으로 작성해도 틀린 것은 아닙니다. 하지만 컴포넌트가 처음 렌더링될 때는 this.scrollBox 값이 undefined이므로 this.scrollBox.scrollToBottom 값을 읽어 오는 과정에서 오류가 발생합니다.<br>

화살표 함수 문법을 사용하여 아예 새로운 함수를 만들고 그 내부에서 this.scrollBox.scrollToBottom 메서드를 실행하면, 버튼을 누를 때(이미 한 번 렌더링을 해서 this.scrollBox를 설정한 시점) this.scrollBox.scrollToBottom 값을 읽어 와서 실행하므로 오류가 발생하지 않습니다.

# 정리
서로 다른 컴포넌트끼리 데이터를 교류할 때 ref를 사용한다면 이는 잘못 사용된 것입니다. 물론 할 수는 있지만, 컴포넌트에 ref를 달고 그 ref를 다른 컴포넌트로 전달하고 ....... 다른 컴포넌트에서 ref로 전달받은 컴포넌트의 메서드를 실행하고 ....... <br>

하지만 이 방법은 리액트 사상에 어긋난 설계입니다. 앱 규모가 커지면 마치 주머니속 이어폰처럼 구조가 꼬여 버려서 유지 보수가 불가능해집니다.<br>

컴포넌트끼리 데이터를 교류할 때는 언제나 데이터를 부모 <-> 자식 흐름으로 교류해야 합니다. 이는 나중에 REDUX를 사용하여 효율적으로 교류하는 방법을 배웁니다.