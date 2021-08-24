# Day 06: React Advanced Guides

Table of contents

- [Day 06: React Advanced Guides](#day-06-react-advanced-guides)
  - [3. Context](#3-using-the-context)
  - [4. Fragment](#4-using-the-fragment)
    - [Giới thiệu](#giới-thiệu)
    - [Đặt vấn đề](#Đặt-vấn-đề)
    - [Cách sử dụng](#Cách-sử-dụng)
    - [Kết luận](#Kết-luận)
  - [7. Portals](#7-using-the-portals)
    - [Giới thiệu](#giới-thiệu)
    - [Cú pháp sử dụng](#cú-pháp-sử-dụng)
    - [Event](#event)
    - [Recap](#recap)

---
## 3. Using the Context
### Giới thiệu
Khi truyền dữ liệu tới các component bằng props thì bạn chỉ có thể truyền từ component cha sang component con. Nếu bạn muốn truyền sang component cấp bậc thấp hơn nữa như component "cháu" hoặc sang component "họ hàng xa" thì điều này rất phức tạp. Bởi vậy `Context` trong ReactJS ra đời để cải thiện khó khăn này.
Định nghĩa về React Context: Context cung cấp cho chúng ta cách để thực hiện chia sẻ dữ liệu tới các component trong cây component mà không cần truyền dữ liệu qua props theo từng cấp bậc.

### Đặt vấn đề

Lấy vd đơn giản như thế này: Mình muốn chuyển lời nhắn (message) từ Component ông sang Component cháu. Nhưng khi sử dụng truyền dữ liệu qua props thì mình bắt buộc phải gửi lời nhắn qua Component con của ông là Component cha (Ông -> Cha -> Cháu ):

```jsx
const Grandchild = (props) => {
  return <h1>>Ông bảo là: "{props.message2}"</h1>;
};
const Father = (props) => {
  return <Grandchild message2={props.message1} />;
};
 
function Grandfather() {
  const message = "Học ReactJS đi cháu";
    return (
      <div>
        <Father message1={message} />
      </div>
    ); 
  }
```
Nếu sử dụng Context thì chúng ta sẽ gửi trực tiếp từ Ông đến Cháu luôn :

```jsx
import React from "react";
 
const MessageContext = React.createContext();
 
class Grandchild extends React.Component {
  render() {
    return <h1>Ông bảo là: "{this.context}"</h1>;
  }
}
Grandchild.contextType = MessageContext;

class Grandfather extends React.Component {
  render() {
    return (
      <MessageContext.Provider value="Vào ncc-react-basic học nhé">
        <Grandchild />
      </MessageContext.Provider>
    );
  }
}
export default Grandfather;
```
Nếu bạn dùng Function thì sẽ hơi khác một chút, bạn cần sử dụng ReactHook, cụ thể là useContext:

```jsx
import React, { useContext } from "react";

const MessageContext = React.createContext();

function Grandchild() {
  const context = useContext(MessageContext);
  return (
    <div>
      <h1>Ông bảo là: "{context}"</h1>,
    </div>
  );
}

export default function Grandfather() {
  return (
    <MessageContext.Provider value="Vào ncc-react-basic học nhé">
      <Grandchild />
    </MessageContext.Provider>
  );
}
```
Bạn thấy đấy, khi dùng Context bạn sẽ không cần truyền dữ liệu qua Component Father như props, lấy dữ liệu trực tiếp luôn. Kết quả như nhau nhưng rõ ràng việc sử dụng Context sẽ đơn giản hơn nhiều.

### Cách sử dụng
React Context cung cấp cho chúng ta cơ chế định nghĩa các data store và truy xuất chúng khi cần. Về tư tưởng khá giống với Redux, còn bây giờ chúng ta làm ngay trong React. Chúng ta có thể định nghĩa và sử dụng một thứ giống như là "global state" của ứng dụng vậy.
Trước tiên, ta tìm hiểu 1 chút về Context API, sau đó sẽ đi vào cụ thể cú pháp và cách sử dụng nhé. Ở đây chúng ta có một vài API mà React cung cấp.
`React.createContext`
```jsx
const MyContext = React.createContext(defaultValue);
```
Khởi tạo một Context Object, giá trị của defaultValue là giá trị mặc định của props value trong Provider.
`Context.Provider`
```jsx
<MyContext.Provider value={/* some value */}>
```
Mỗi Context Object phải đi kèm với một Provider, nó cho phép bạn nhận về sự thay đổi của context.
`Context.Consumer`
```jsx
<MyContext.Consumer>
  {value => /* render something based on the context value */}
</MyContext.Consumer>
```
Một React component được khởi chạy mỗi khi gía trị của context thay đổi, và nhận về giá trị của context đó.
`Class.contextType`
```jsx
MyClass.contextType = MyContext;
```
contextType là một thuộc tính của class được tạo bởi React.createContext() được dùng để lấy giá trị của context.
Lưu ý như ở phần trên, với Function Component thì sử dụng Hook useContext để lấy giá trị của context.

Oke, giờ chúng ta sẽ tìm hiểu cách sử dụng Context trong ReactJS theo 3 bước:
  1.Khởi tạo object context bằng phương thức React.createContext(), sau đó chúng ta sẽ nhận được 1 object bao gồm các thuộc tính quan trọng như Provider và Consumer.
  2.Sử dụng Provider bọc quanh các component, và định nghĩa các dữ liệu bạn cần chứa trong đó (truyền giá trị vào props value). Sử dụng contextType hoặc useContext để lấy giá trị context
 
Chúng ta sẽ đi vào ví dụ cụ thể. Mình sẽ xây dựng 1 ứng dụng cho phép tăng giảm các số lến 1 đơn vị và hiển thị chúng. Trước tiên, chúng ta sẽ tiến hành khởi tạo một object context ở một Component tên là CounterContext

```jsx
import React from 'react'
const CounterContext = React.createContext();
```
Sau đó, mình sẽ khởi tạo state ban đầu và bọc quanh component cần chia sẻ bằng Provider :

```jsx
import React, {useState} from 'react'

const CounterContext = React.createContext();

export const CounterProvider = (props) => {
  const [counter, setCounter] = useState(0);
  const increaseCounter = () => {
    setCounter(counter => counter + 1)
  }
  const decreaseCounter = () => {
    setCounter(counter => counter - 1)
  }
  return (
    <CounterContext.Provider value= {
      {counter, increaseCounter, decreaseCounter}
    }>
      {props.children}
    </CounterContext.Provider>
  );
}

export default CounterContext;
```
Bây giờ sang Component App để hiển thị ra kết quả:

```jsx
import React, { useContext } from "react";
import CounterContext, { CounterProvider } from "./CounterContext";

const Child = (props) => {
  const { counter, increaseCounter, decreaseCounter } = useContext(CounterContext);
  return (
    <div>
      <h2>{counter}</h2>
      <button onClick={increaseCounter}>Increase Counter</button>
      <button onClick={decreaseCounter}>Decrease Counter</button>
    </div>
  );
};
const App = (props) => {
  return (
    <CounterProvider>
      <Child />
    </CounterProvider>
  );
}

export default App;
```
### Kết luận
Việc sử dụng React Context sẽ giúp bạn đỡ vất vả hơn rất nhiều trong việc truyền gửi dữ liệu giữa các 
Component. Hãy cố gắng tự thực hành những kiến thức trên để quen tay và hiểu hơn nhé.
## 4. Using the Fragment
### Giới thiệu
`Fragment` là một common pattern được giới thiệu trong phiên bản React 16.2.0. Nó cho phép bạn return nhiều element từ một component mà không làm sinh ra những DOM element không cần thiết.
Mặc dù chúng đã xuất hiện được một thời gian tuy nhiên chúng ta thường ít khi để ý và sử dụng nó. Trong bài viết này chúng ta cùng đi tìm hiểu cú pháp và lý do nên áp dụng `fragment` trong ReactJS nhé. 

### Đặt vấn đề
Trước khi tìm hiểu về `Fragments` chúng ta hãy cùng nhau tìm hiểu vấn đề thông qua một vài vd sau đây.
Khi lập trình ReactJS, đoạn chương trình sau sẽ bị lỗi lúc biên dịch:

```jsx
function App() {
  return (
           <p>This is</p>
           <p>a Pig</p>
           <p>that I want to render</p>
        );
    }
```
Bạn sẽ gặp thông báo lỗi " JSX parent expressions must have one parent element” khi không bọc các element JSX quanh một element nào đó. Để sửa, chúng ta đặt vào một thẻ đóng mở, vd như <div></div> thì sẽ hết lỗi:

```jsx
function App() {
  return (
          <div>
              <p>This is</p>
              <p>a Pig</p>
              <p>that I want to render</p>
          </div>
        );
    }
```
Bạn nghĩ vấn đề đã được giải quyết, rất đơn giản và chẳng khó khăn tẹo nào. Tuy nhiên, thực tế cấu trúc của chương trình thường khá phức tạp và do đó phân cấp của component ngày càng nhiều hơn. Hậu quả là, khi ReactJS render ra html, bạn sẽ rơi vào một “hố đen phân cấp” kiểu như này: 

<div class="level1">
    <div class="level2">
        <div class="level3">
            ...
            <div class="leveln">
               	<div class="black-hole ">
                    Take me out of here!
                </div>
            </div>
            ...
        </div>
    </div>
</div>

Việc phân cấp thẻ div như thế này có rất nhiều vấn đề: Thứ nhất là code html được render ra rất tệ. Thứ hai, điều quan trọng hơn, là nó khiến cho việc bạn thiết kế css gần như sẽ thất bại về mặt tổ chức. Bạn thử hình dung lúc muốn truy cập vào một class name ở thẻ div trong cùng của “hố đen phân cấp kia” thì css của bạn cũng sẽ khủng khiếp và hoành tráng không kém cạnh gì :}} .
Oke, nếu như bạn là người giỏi chịu đựng và có thể làm được những điều trên, chúng ta thử đến vấn đề thứ hai xem bạn giải quyết như thế nào.
Bạn có môt component có tên Table dùng để hiển thị bảng:

```jsx
function Table() {
  return (
        <table>
          <tr>
            <Columns />
          </tr>
        </table>
      );
    }
```
và component có tên Columns sẽ hiển thị nội dung của bảng đó. Nếu chúng ta thêm thẻ div để bao quanh JSX trong component Colums như thế này:

```jsx
function Columns() {
  return (
        <div>
          <td>Hello</td>
          <td>World</td>
        </div>
      );
    }
```
Code chạy được chứ, tất nhiên là không. Vì nó đã phá vỡ cấu trúc của tables và HTML sẽ hiển thị không hợp lệ, đây là kết quả khi thực hiện render:

<table>
  <tr>
    <div>
      <td>Hello</td>
      <td>World</td>
    </div>
  </tr>
</table>

Rõ ràng vấn đề sinh ra chỉ vì những thẻ div đóng mở không cần thiết mà ta thêm vào. Nếu chúng ta có thể render nội dung của thằng ChildComponent cùng cấp luôn với những thẻ trong thằng Component cha, thì mọi chuyện dễ dàng được giải quyết. Và React Fragement được sinh ra vì mục đích đó.
### Cách sử dụng:
Như đã nói ở trên, `fragments` cho phép bạn nhóm các phần tử vào với nhau mà không cần phải bổ sung một thẻ nào bao bọc chúng và nó cũng không thể hiện ra HTML. Cách sử dụng hết sức đơn giản, bạn chỉ cần wrap list các children bởi <React.Fragment>. Tiếp tục vd ở phần thứ nhất, chúng ta sẽ chỉnh sửa đoạn code component Columns như sau:

```jsx
function Columns() {
  return (
        <React.Fragment>
          <td>Hello</td>
          <td>World</td>
        </React.Fragment>
      );
    }
```
Đoạn HTML hiển thị hợp lệ vì không còn cặp <div> bọc các cột bên trong <tr> nữa. Rất nhanh và đơn giản đúng không:

<table>
  <tr>
    <td>Hello</td>
    <td>World</td>
  </tr>
</table>

Trường hợp phổ biến nhất để sử dụng các `fragment` là component trả về nhiều phần tử. Thay vì sử dụng <div> để bọc các phần tử lại với nhau thì chúng ta hãy sử dụng <React.Fragment> để nhóm chúng lại:

```jsx
function ChildComponent() {
        return (
            <React.Fragment>
                <div className="header"/>
                <div className="banner"/>
                <div className="content"/>
            </React.Fragment>
        );
    }

function App() {
        return (
          <div className="main">
              <div className="topnav"/>
              <ChildComponent/>
              <div className="footer"/>
          </div>
        );
    }   
```
Nếu bạn vẫn cố sử dụng thẻ <div> thay vì <React.Fragment> xem thế nào, thì kết quả render sẽ ra như thế này:

<div class="main">
    <div class="topnav"/>
    <div>
      <div class="header"/>
      <div class="banner"/>
      <div class="content"/>
    </div>
    <div class="footer"/>
</div>

Tuy nhiên, vì chúng ta dùng Fragment, nên kết quả sẽ render ra như sau:

<div class="main">
  <div class="topnav"/>
  <div class="header"/>
  <div class="banner"/>
  <div class="content"/>
  <div class="footer"/>
</div> 

Bạn đã thấy sự khác biệt chưa. Code nhìn “sạch” hơn và làm css cũng sẽ đỡ vất hơn nhiều.

Fragment có hỗ trợ khai báo từ khóa (key). Ví dụ cho trường hợp này là việc ánh xạ một collection sang một mảng các fragment, để tạo nên một danh sách các mô tả chi tiết:

```jsx
function Glossary(props) {
  return (
    <dl>
      {props.items.map(item => (
        // Without the `key`, React will fire a key warning
        <React.Fragment key={item.id}>
          <dt>{item.term}</dt>
          <dd>{item.description}</dd>
        </React.Fragment>
      ))}
    </dl>
  );
}
```
Một lưu ý nhỏ, chúng ta có thể khai báo rút gọn bằng kiểu thẻ đóng mở `<></>` . Tuy nhiên bạn không nên lạm dụng kiểu này, vì khó để check code hơn.
### Kết luận
Nhìn chung các `Fragments` có giá trị sử dụng thay thế cho một thẻ div bao bọc các phần tử. Vậy câu hỏi đặt ra là chúng ta có nên sử dụng chúng hay chỉ trong trường hợp thực sự cần thiết thôi? Đã có câu trả lời này trên StackOverflow:
  - Nó nhanh hơn một chút và sử dụng ít bộ nhớ hơn (không cần tạo thêm DOM để đánh dấu).
  - Một số cơ chế CSS như Flexbox và CSS Grid có mốt quan hệ cha-con đặc biệt và việc thêm div ở giữa khiến chúng ta khó giữ được bố cục mong muốn.
  - DOM inspector đỡ lộn xộn hơn (ví dụ <div> nằm trong <tr> ở trên)
Với những lý do đó chúng ta nên sử dụng chúng.


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
