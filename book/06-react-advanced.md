# Day 06: React Advanced Guides

Table of contents

- [Day 06: React Advanced Guides](#day-06-react-advanced-guides)
  - [7. Portals](#7-using-the-portals)
    - [Giới thiệu](#giới-thiệu)
    - [Cú pháp sử dụng](#cú-pháp-sử-dụng)
    - [Event](#event)
    - [Recap](#recap)

---

## 7. Using the Portals

### Giới thiệu

`Portals` là một API nghe có vẻ khá xa lạ và ít được sử dụng, tuy nhiên mình dám chắc là trong quá trình phát triển ứng dụng web với React bạn đã sử dụng nó rồi, mà chưa để ý thôi.

Vậy `Portal` là gì, `Portal` cho phép chúng ta render một phần HTML độc lập với component tree, để mình giải thích chút nhé:
Bình thường trong project reactjs ta có file index.html

```html
<html>
  <head></head>
  <body>
    <div id="root"></div>
  </body>
</html>
```

Và tại app.js ta có:

```jsx
ReactDOM.render(<App />, document.getElementById("root"));
```

Toàn bộ ứng dụng sẽ được nằm trong thẻ div với id là root.
Tiếp theo giả sử ta có như sau, bên trong App:

```jsx
function App() {
  return (
    <div className="app-wrapper">
      <HomePage />
      <Footer />
    </div>
  );
}
```

Nếu các component đều sử dụng phương thức render thì nội dung trong các child-component là HomePage và Footer sẽ nằm trong thẻ div với class là app-wrapper, tương tự với các level tiếp theo.

Việc render nội dung như vậy nhìn chung rất bình thường, tuy nhiên một số trường hợp ta muốn tạo ra một component, có style không bị ảnh hưởng bởi thành phần parent của nó bất kể level mà nó được render, ví dụ như Modal, hay Tooltip chẳng hạn.

### Cú pháp sử dụng

Thay vì sử dụng như thường lệ:

```jsx
import React from "react";

function Modal({ children }) {
  return <div>{children}</div>;
}

export default Modal;
```

Ta sẽ sử dụng `React Portal` như sau:

```jsx
import React from "react";
import ReactDOM from "react-dom";

function Modal({ children }) {
  return ReactDOM.createPortal(
    <div id="modal-wrapper">{children}</div>,
    document.querySelector("body")
  );
}

export default Modal;
```

Khi đó file index.html sẽ có nội dung như sau:

```html
<html>
  <head></head>
  <body>
    <div id="root"></div>
    <div id="modal-wrapper"></div>
  </body>
</html>
```

Vì vậy phần style của modal-wrapper sẽ không bị ảnh hưởng bởi level mà Modal được render bên trong component tree của project, mà chỉ ảnh hưởng bởi global style.

### Event

Lưu ý rằng, mặc dù `Portal` được sinh ra cùng với với thẻ div root trên **_DOM_**, có style không bị ảnh hưởng bởi component tree, tuy nhiên về event lại thể hiện như một phần tử bên trong component tree. Như ví dụ sau:

```jsx
import React from "react";
import ReactDOM from "react-dom";

function Modal({ children }) {
  return ReactDOM.createPortal(
    <div id="modal-wrapper">{children}</div>,
    document.querySelector("body")
  );
}

function Wrapper({ handleClick }) {
  return (
    <div className="wrapper" onClick={handleClick}>
      <Modal />
    </div>
  );
}
```

Mặc dù ở DOM thẻ div id="modal-wrapper" sẽ nằm ngoài div root, tuy nhiên **_handleClick_** vẫn sẽ được gọi khi event được thực hiện trên Modal, dựa theo component tree. Vì vậy việc kiểm soát những event như vậy sẽ phức tạp hơn. Vì vậy chỉ nên dùng `Portal` khi thật sự cần thiết.

### Recap

- `Portal` cho phép chúng ta render một phần HTML độc lập với component tree.
- Cần cẩn thận kiểm soát khi sử dụng `event`.
- Chỉ nên sử dụng `Portal` khi cần thiết.
