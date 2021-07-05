# 02: React Base Features & Syntax

Table of contents

- [02: React Base Features & Syntax](#02-react-base-features--syntax)
  - [1. Preparation](#1-preparation)
  - [2. JSX](#2-jsx)
  - [3. Component Basics](#3-component-basics)
    - [Classed-base component](#classed-base-component)
    - [Functional component](#functional-component)
    - [Reusing component](#reusing-component)
  - [4. Props](#4-props)
  - [5. Children Property](#5-children-property)
  - [6. State](#6-state)
    - [Class-based component](#class-based-component)
      - [Update state](#update-state)
    - [Functional Component](#functional-component-1)
  - [7. Stateless and Stateful component](#7-stateless-and-stateful-component)
    - [Stateless component](#stateless-component)
    - [Stateful component](#stateful-component)
  - [8. Handling Event with Methods](#8-handling-event-with-methods)
  - [9. Passing Method References between Components](#9-passing-method-references-between-components)
  - [10. Two way binding](#10-two-way-binding)
  - [Summary](#summary)
  - [Time to Practice](#time-to-practice)

---

## 1. Preparation

Đầu tiên để bắt đầu chúng ta phải khởi tạo project và chạy trên localhost phải không nào! (Nếu các bạn chưa có Nodejs thì cài [Nodejs](https://nodejs.org/en/) trước nha.)  
Chúng ta khởi tạo và run react project với [create-react-app](https://create-react-app.dev/docs/getting-started/) như sau:

```
npx create-react-app react-sample-app
cd react-sample-app
npm start
```

Cấu trúc thư mục:

```
react-sample-app
├── README.md
├── node_modules
├── package.json
├── .gitignore
├── public
│   ├── favicon.ico
│   ├── index.html
│   ├── logo192.png
│   ├── logo512.png
│   ├── manifest.json
│   └── robots.txt
└── src
    ├── App.css
    ├── App.js
    ├── App.test.js
    ├── index.css
    ├── index.js
    ├── logo.svg
    ├── reportWebVitals.js
    └── setupTests.js
```

## 2. JSX

React giới thiệu JSX, đây là một cú pháp mở rộng cho javascript. Thành phần cơ bản nhất trong jsx là Element.

```JSX
// fileName.jsx
const name = 'thaibm';
const element = <h1 className="title">Hello, {name}</h1>;
```

Babel biên dịch JSX thành những câu gọi hàm `React.createElement()`. Do vậy đoạn code trên cũng tương đương với:

```JSX
const name = 'thaibm';
const element = React.createElement(
    'h1',
    {className: 'title'},
    `Hello, ${name}!`
);
```

Kết quả sau khi render: `<h1 class="title">Hello, thaibm!</h1>`.

**_Một số lưu ý:_**

```JSX
const element = (
    <div className="app">
        <h1 className="title">Hello, thaibm!</h1>
    </div>
)
```

Trong ví dụ trên các bạn để ý `<div className="app"></div>` là parentElement còn `<h1 className="title">Hello, thaibm!</h1>` là childElemnt.

Ngoài ra các bạn thấy ta có `className` chứ không phải `class` như khi code HTML nha. Trông thì khá giống HTML, nhưng đó lại ko phải HTML. `className` là một trong những HTMLAttributes, các bạn có thể xem thêm trong

```ts
// /node_modules/@types/react/index.d.ts
interface HTMLAttributes<T> extends AriaAttributes, DOMAttributes<T> {
  // Standard HTML Attributes
  className?: string;
  hidden?: boolean;
  id?: string;
  placeholder?: string;
  style?: CSSProperties;
  ...
}
```

## 3. Component Basics

React cung cấp cho chúng ta hai cách để khai báo một component:

### Classed-base component

```JSX
class App extends React.Component {
  render() {
    return (
      <div className="App">
        <h1>React Sample App</h1>
      </div>
    );
  }
}
```

### Functional component

```JSX
function App() {
  return (
    <div className="App">
      <h1>React Sample App</h1>
    </div>
  );
}
```

### Reusing component

Chúng ta khởi tạo một component là Person:

```JSX
// components/person/Person.js
const Person = () => {
    return <p>I am Iron man!</p>;
}

export default Person;
```

Sau đó import ở nơi cần sử dụng và dùng thôi hiha. Cú pháp khá giống như việc gọi component trong Angular hoặc Vue nha.

```JSX
import Person from './components/person/Person';

class App extends React.Component {
  render() {
    return (
      <div className="App">
        <h1>React Sample App</h1>
        <Person />
        {/* or */}
        <Person></Person>
        {/* or call many times if you want */}
        <Person />
        <Person />
      </div>
    );
  }
}
```

> Note: Always start component names with a capital letter.

Chú ý là luôn bắt đầu component với chữ cái in hoa nha vì React coi những thẻ bắt đầu với chữ thường là DOM tags.

## 4. Props

Component cho phép truyền vào input, nhìn khá giống với HTML attributes và chúng thì được gọi là Props. Cách sử dụng prop như sau:

```JSX
// components/person/Person.js
const Person = (props) => {
  return <p>I am {props.name}!</p>;
};
```

```JSX
// App.js
<Person name="Iron man"/>
```

> Note: Props are Read-Only

Chúng ta ko được thay đổi giá trị của props.

## 5. Children Property

Chúng ta có thể truyền content vào giữa opening và closing tags, đó là children prop:

```JSX
// components/person/Person.js
const Person = (props) => {
  return (
    <div>
      <p>I am {props.name}!</p>
      <p>{props.children}</p>
    </div>
  );
};
```

```JSX
// App.js
<Person name="Iron man">Love you 3000!</Person>
```

## 6. State

Giả sử chúng ta cần một biến trong nội bộ component, có thể thay đổi được và được quản lý hoàn toàn bởi component đó, không thể sài props phải không nào. Và react cung cấp state cho phép chúng ta làm việc đó.

### Class-based component

Với **class-based component** chúng ta sử dụng state như sau:

```jsx
class App extends React.Component {
  // State Declaration
  state = {
    person: {
      name: 'Iron man',
    },
    statement: 'Love you 3000!',
  };

  render() {
    return (
      <div className='App'>
        <h1>React Sample App</h1>
        {/* State Usage */}
        <Person name={this.state.person.name}>{this.state.statement}</Person>
      </div>
    );
  }
}
```

#### Update state

Để update giá trị cho state, chúng ta sử dụng `setState()` function (không được phép thay đổi trực tiếp state nha, phải modify qua `setState()`):

```jsx
// App.js
<button
  onClick={() =>
    this.setState({
      person: { name: 'Morgan Stark' },
    })
  }
>
  Change person
</button>
```

Khi click vào button, hàm `setState()` được gọi, giá trị của state được cập nhật và update lên view (re-render).  
`setState()` nhận vào một object cập nhật cho state hiện tại. Bên cạnh đó nó cũng **merge** giá trị mới với giá trị cũ. Ta thấy khi gọi `setState()` trong ví dụ trên, object truyền vào không hề có `statement` property nhưng `statement` cũng không bị mất đi. React chỉ cập nhật giá trị cho `person`.

> 1. Do Not Modify State Directly.
> 2. State Updates May Be Asynchronous
> 3. State Updates are Merged

### Functional Component

Đối với **functional component**, để cài đặt state chúng ta phải sử dụng một react hook là `useState()`:

```jsx
// App.js
import React, { useState } from 'react';
import './App.css';
import Person from './components/person/Person';
const App = () => {
  // State Declaration
  const [person, setPerson] = useState({ name: 'Iron man' });
  const [statement, setStatement] = useState('Love you 3000!');

  return (
    <div className='App'>
      <h1>React Sample App</h1>
      {/* State Usage */}
      <Person name={person.name}>{statement}</Person>

      <button onClick={() => setPerson({ name: 'Morgan Stark' })}>
        Change person
      </button>
    </div>
  );
};
export default App;
```

useState() nhận vào giá trị khởi tạo cho state và return một array gồm 2 value: current state và function để update state.

```js
function useState<S>(initialState: S | (() => S)): [S, Dispatch<SetStateAction<S>>];
```

Ở ví dụ trên, mình sử dụng destructuring `[person, setPerson]`, lúc này `person` là current state và `setPerson()` là function để update `person`.

## 7. Stateless and Stateful component

### Stateless component

Stateless component là những component không có internal state, làm việc với stateless component được khuyến khích sử dụng càng nhiều càng tốt trong dự án của bạn vì chúng không có internal logic thay đổi state tùm lum. Nhận vào một số input và trả ra content theo một cấu trúc đã được định sẵn. Nó cũng giống với khái niệm _pure function_ trong functional programing.  
Person component là một stateless component:

```JSX
// components/person/Person.js
const Person = (props) => {
  return (
    <div>
      <p>I am {props.name}!</p>
      <p>{props.children}</p>
    </div>
  );
};
```

### Stateful component

Stateful component hay còn gọi là `smart components` hoặc `container components`. Dễ hiểu từ tên gọi, chúng có state. Các bạn có thể thấy một ví dụ cho Stateful component là App component trong các ví dụ bên trên.

**Lời khuyên** là cố gắng sử dụng stateless component nhiều nhất có thể, Stateful component là component bao lấy những stateless component đó. Điều đó giúp cho application của bạn dễ quản lý và maintain. Luồng data sẽ rành mạch, rõ ràng. Bên cạnh đó cũng rất tường mình về vị trí của main logic cũng là nơi data được thay đổi.

> **Have as many pure functional presentational components as possible!**

## 8. Handling Event with Methods

Ở ví dụ trên, mình có handle onClick event bằng một arrow function. Tuy nhiên logic trong function đó không phải lúc nào cũng đơn giản như vậy phải không nào. Chúng ta sẽ tách đống logic đó ra một method rồi gọi lại sau:

```jsx
class App extends React.Component {
  // State Declaration
  state = {
    person: {
      name: 'Iron man',
    },
    statement: 'Love you 3000!',
  };

  changePerson = () => {
    this.setState({
      person: { name: 'Morgan Stark' },
    });
  };

  render() {
    return (
      <div className='App'>
        <h1>React Sample App</h1>
        {/* State Usage */}
        <Person name={this.state.person.name}>{this.state.statement}</Person>

        <button onClick={() => this.changePerson()}>Change person</button>
      </div>
    );
  }
}
```

Và với functional component

```jsx
const App = () => {
  // State Declaration
  const [person, setPerson] = useState({ name: 'Iron man' });
  const [statement, setStatement] = useState('Love you 3000!');

  const changePerson = () => {
    setPerson({ name: 'Morgan Stark' });
  };

  return (
    <div className='App'>
      <h1>React Sample App</h1>
      {/* State Usage */}
      <Person name={person.name}>{statement}</Person>

      <button onClick={() => changePerson()}>Change person</button>
    </div>
  );
};
```

## 9. Passing Method References between Components

Chúng ta có thể pass method đến child component dưới dạng prop:

Với functional component

```jsx
// App.js
<Person name={person.name} click={changePerson}>
  {statement}
</Person>
```

Với class-based component có một chút khác biệt

```jsx
<Person name={this.state.person.name} click={this.changePerson.bind(this)}>
  {this.state.statement}
</Person>
```

Vì hàm `changePerson` của chúng ta ở classed-base component có sử dụng `this` khi gọi `this.setState()`, `this` mà chúng ta cần chính là App component nên chúng ta phải bind `this` vào function trong trường hợp này.  
Hoặc nếu không muốn sử dụng `bind()` chúng ta có thể sử dụng arrow function:

```jsx
<Person name={this.state.person.name} click={() => this.changePerson()}>
  {this.state.statement}
</Person>
```

Sau đó, ta sử dụng ở child component là Person.js như sau:

```jsx
// ./components/Person.js
const Person = (props) => {
  return (
    <div>
      <p onClick={props.click}>I am {props.name}!</p>
      <p>{props.children}</p>
    </div>
  );
};
```

hoặc call trong method:

```jsx
const Person = (props) => {
  const clickHandle = () => {
    props.click();
  };

  return (
    <div>
      <p onClick={clickHandle}>I am {props.name}!</p>
      <p>{props.children}</p>
    </div>
  );
};
```

## 10. Two way binding

Để thêm two way binding chúng ta sẽ sử dụng onChange để lắng nghe thay đổi và value để bind data:

```jsx
// Person.js
const Person = (props) => {
  const clickHandle = () => {
    props.click();
  };

  return (
    <div>
      <p onClick={clickHandle}>I am {props.name}!</p>
      <p>{props.children}</p>
      <input type='text' onChange={props.change} value={props.name} />
    </div>
  );
};
```

Và trong file App.js, chúng ta thêm một function để cập nhật state khi có value thay đổi:  
(Lưu ý: mình viết ở dạng functional component nha)

```jsx
const changeNameHandle = (event) => {
  setPerson({ name: event.target.value });
};
```

```jsx
<Person name={person.name} click={changePerson} change={changeNameHandle}>
  {statement}
</Person>
```

Bây giờ, khi bạn thay đổi giá trị trong input, text được thay đổi. Ngược lại, khi bạn click để đổi person's name, value của input cũng sẽ được thay đổi theo.

## Summary

Tổng kết day-02 có những topic sau quan trọng cần ghi nhớ nha:

1. [JSX](#2-jsx)
2. [Component](#3-Component-Basics)
3. [Props](#4-props), [Children Property](#5-children-property), [State](#6-state)
4. [Handling Event, Methods](#7-handling-event-with-methods), [Passing Method References between Components](#9-passing-method-references-between-components)
5. [Two way binding](#10-two-way-binding)

## Time to Practice

Các bạn hãy cố gắng tự thực hành những kiến thức trên để quen tay và hiểu hơn nha!
