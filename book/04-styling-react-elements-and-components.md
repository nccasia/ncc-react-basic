# Day 04: Styling React Elements and Components

Table of contents

- [Day 04: Styling React Elements and Components](#day-04-styling-react-elements-and-components)
  - [1. Adding Styling with Stylesheets](#1-adding-styling-with-stylesheets)
  - [2. Setting Class Name Dynamically](#2-setting-class-name-dynamically)
  - [3. Working with CSS Modules](#3-working-with-css-modules)
  - [4. CSS Modules and Media Queries](#4-css-modules-and-media-queries)
  - [5. Inline Styles](#5-inline-styles)
  - [6. Setting Styles Dynamically](#6-setting-styles-dynamically)
  - [7. Additional: Using Radium for Pseudo and Media Queries](#7-additional-using-radium-for-pseudo-and-media-queries)
  - [8. Additional: Using Styled Components and Dynamic Styles](#8-additional-using-styled-components-and-dynamic-styles)

---

## 1. Adding Styling with Stylesheets

Cách 1 đó là import một file css. Mình tạo mới một file Person.css như sau:

```css
/* ./component/person/Person.css */
.person {
  padding: 20px;
  border: 1px solid #eeeeee;
  border-radius: 4px;
  margin-bottom: 30px;
  width: 60%;
}

.person:hover {
  box-shadow: 0 2px 4px #cccccc;
}
```

Sau đó chúng ta import vào file Person.js rồi dùng như bình thường thôi:

```jsx
import "./Person.css";

const Person = (props) => {
  const clickHandle = () => {
    props.click();
  };

  return (
    <div className="person">
      <p onClick={clickHandle}>I am {props.name}!</p>
      <p>{props.children}</p>
      <input type="text" onChange={props.change} value={props.name} />
    </div>
  );
};

export default Person;
```

## 2. Setting Class Name Dynamically

Bạn có một dòng trạng thái, khi ấn vào button thì dòng trạng thái đó sẽ được đánh dấu nền màu đỏ.

```jsx
import ‘./Status.css’;

function Status() {
  return (
    <div className="App">
      <button onClick={changeStatus}>Change</button>
      <p className="status-text">This is the status</p>
    </div>
  );
}
```

```css
/* ./Status.css */
.status-text {
  background-color: red;
}
```

Nếu css như này dòng trạng thái sẽ luôn có màu đỏ khi chưa được click vào button, vì vậy chúng ta cần dùng đến className dynamically.

```jsx
import "./Status.css";

export function Status() {
  const [status, setStatus] = useState(false);

  const changeStatus = () => setStatus(!status);

  const statusClassName = status ? "status-text" : "";

  return (
    <div className="App">
      <button onClick={changeStatus}>Change</button>
      <p className={statusClassName}>This is the status</p>
    </div>
  );
}
```

Như vậy, chỉ khi status = true, background của dòng trạng thái mới chuyển thành màu đỏ.

## 3. Working with CSS Modules

Khi code một dự án lớn, số lượng file, code quá nhiều, bạn không thể nhớ được mình đã đặt biết bao nhiêu className cho các thẻ HTML. Đôi khi các className có thể bị trùng nhau, style bị ghi đè không mong muốn dẫn đến conflict, ảnh hưởng tới nhau.
Vì vậy CSS Modules ra đời để giúp chúng ta module hóa các stylesheet thành các file css nhỏ và scoped locally to the component (\*).

`scoped locally to the component (*)`: tất cả class name, selectors, animations trong css modules files sẽ chỉ hợp lệ ở component import nó và không ảnh hưởng tới thành phần các trong website.

Để sử dụng css module chúng ta import file css như dưới đây và sử dụng các class css dưới dạng styles.{tên class}.

```jsx
import styles from "./app.module.css";

export function App() {
  return (
    <div className="App">
      <img src="./imge.png" alt="item" className={styles.item} />
      <p className={styles.price}>$100.000</p>
      <button className={styles.btn}>Buy it</button>
    </div>
  );
}
```

```css
/* ./app.module.css */
.item {
  width: 500px;
  height: 400px;
  border-radius: 5px;
}
.price {
  font-size: 16px;
  font-weight: 500;
  color: #000000;
}
.btn {
  font-size: 20px;
  padding: 5px 10px;
  background-color: #ff8181;
}
```

## 4. CSS Modules and Media Queries

Media Query giúp chúng ta áp dụng những CSS rules khác nhau cho những thiết bị có kích cỡ màn hình khác nhau như desktop, table, smartphone.

```css
//…

@media only screen and (max-width: 600px) {
  .item {
    width: 90%;
    height: 30%;
    border-radius: 5px;
  }
}
```

Khi kích thước chiều ngang màn hình nhỏ hơn hoặc bằng 600px, thẻ img sẽ được set lại width và height sao cho phù hợp với màn hình.

## 5. Inline Styles

Ngoài ra chúng ta có thể sử dụng inline style như sau:

```jsx
// App.js
// ...
const btnStyle = {
  backgroundColor: "#20232a",
  color: "#61dafb",
  padding: "8px 16px",
  border: "none",
  borderRadius: "4px",
  cursor: "pointer",
};
// ...
<button onClick={() => changePerson()} style={btnStyle}>
  Change person
</button>;
```

**Lưu ý** là style attribute nhận vào một Javascript object, các properties follow camelCased nha.

## 6. Setting Styles Dynamically

Chúng ta có thể thay đổi style của thẻ p dựa vào state hiện tại, nếu là true style là stateSuccess còn nếu false thì style sẽ là stateFail.

```jsx
export function App() {
  // App.js

  const [state,setState] = useState(false)
  // ...

  const stateFail = {
    //...
    backgroundColor: "red",
  }

  const stateSuccess = {
    //...
    backgroundColor: "green",
  }
 //...
  <p style={state ? stateSuccess : stateFail}>This is state now</p>
  <button onClick={() => changeState()} style={btnStyle}>
      Change state
  </button>
```

## 7. Additional: Using Radium for Pseudo and Media Queries

Để sử dụng radium bạn cần phải cài thư viện radium bằng lệnh `npm i radium` hoặc `yarn add radium`.
Để sử dụng cần bọc bên ngoài 1 thẻ StyleRoot. Bạn có thể sử dụng Pseudo và Media Queries như ví dụ dưới đây.

```jsx
import { StyleRoot } from "radium";

export default function App() {
  // App.js
  // ...
  const imgStyle = {
    height: "400px",
    width: "500px",
    borderRadius: "5px",
    ":hover": {
      transform: "scale(1.2)",
      width: "600px",
    },
    "@media (max-width: 500px)": {
      width: "90%",
      height: "30%",
    },
  };

  return (
    <StyleRoot>
      <div className="App">
        // ...
        <img src=”./img.png” alt="item" style={imgStyle} />
      </div>
    </StyleRoot>
  );
}
```

## 8. Additional: Using Styled Components and Dynamic Styles

Để cài thư viện styled component chúng ta dùng lệnh `npm i styled-components` hoặc `yarn add styled-components`.

Để sử dụng chúng ta cần import như ví dụ bên dưới.

```jsx
import styled from "styled-components";

export function App() {
  // App.js
  // ...
  const Button = styled.button`
    background: ${(props) => (props.primary ? "black" : "white")};
    font-size: 16px;
    padding: 5px 10px;
    border-radius: 3px;
  `;

  return (
    <div>
      <Button>Normal</Button>
      <Button primary>Primary</Button>
    </div>
  );
}
```

Chúng ta sử dụng Dynamic Styles bằng cách sử dụng props để thay đổi style của button.
