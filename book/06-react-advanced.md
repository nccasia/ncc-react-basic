# Day 06: React Advanced Guides

Table of contents

- [Day 06: React Advanced Guides](#day-06-react-advanced-guides)
  - [3. Context](#3-using-the-context)
  - [4. Fragment](#4-using-the-fragment)
    - [Giới thiệu](#giới-thiệu)
    - [Đặt vấn đề](#Đặt-vấn-đề)
    - [Cách sử dụng](#Cách-sử-dụng)
    - [Kết luận](#Kết-luận)
  - [5. React Lifecycle](#5-react-lifecycle)
    - [Giới thiệu](#giới-thiệu)
    - [Các phương thức về lifecycle trong class component](#Các-phương-thức-về-lifecycle-trong-class-component) 
      -[constructor()](#constructor()) 
      -[getDerivedStateFromProps()](#getDerivedStateFromProps()) 
      -[getSnapshotBeforeUpdate()](#getSnapshotBeforeUpdate()) 
      -[shouldComponentUpdate()](#shouldComponentUpdate()) 
      -[componentDidUpdate()](#componentDidUpdate()) 
      -[componentDidMount()](#omponentDidMount()) 
      -[componentDidCatch()](#componentDidCatch()) 
      -[componentWillUnmount()](#componentWillUnmount()) 
      -[render()](#render())
    - [Thay thế Component lifecycle methods bằng React Hooks](#Thay-thế-Component-lifecycle-methods-bằng-React-Hooks) 
      -[componentDidMount()](#componentDidMount()-1)  
      -[componentDidUpdate()](#componentDidUpdate()-1) 
      -[componentWillUnmount()](#componentWillUnmount()-1) 
      -[shouldComponentUpdate()](#shouldComponentUpdate()-1) 
      -[getDerivedStateFromProps()](#getDerivedStateFromProps()-1)
    - [Recap](#recap)
  - [6. PropTypes](#6-PropTypes) 
    -[Giới thiệu](#giới-thiệu-1)
    -[Cách sử dụng](#cách-sử-dụng) 
    -[Recap](#recap-1)
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
### 5. React Lifecycle

## Giới thiệu

React cho phép bạn định nghĩa components theo class hoặc function. Để định nghĩa React component bằng class, bạn cần kế thừa React.Component

```jsx
class Welcome extends React.Component {
  render() {
    return <h1>Hello, ReactJs</h1>;
  }
}
```

Phương thức duy nhất mà bạn phải định nghĩa trong các lớp con kế thừa từ React.Component là `render()`

The Component Lifecycle

- Bạn có thể tưởng tượng Lifecycle của component giống như con người vậy. Sinh ra(Mounting) => Phát triển(Updating) => Mất đi(Unmounting)

- Mỗi component có một vài phương thức vòng đời(lifecycle methods) mà bạn có thể ghi đè để chạy code vào từng thời gian cụ thể.

- Mouting:
  Những phương thức sau được gọi theo thứ tự khi một instance của một component được tạo và gắn vào DOM:

* constructor()
* static getDerivedStateFromProps()
* render()
* componentDidMount()

- Updating:
  Việc cập nhật được xảy ra khi có sự thay đổi về `props` hoặc `state`. Nhưng phương thức sau được gọi theo thứ thự khi một component được render lại

* static getDerivedStateFromProps()
* shouldComponentUpdate()
* render()
* getSnapshotBeforeUpdate()
* componentDidUpdate()

- Unmounting:
  Phương thức này được gọi khi một component đang bị gỡ ra khỏi DOM

* componentWillUnmount()

- Error Handling(Xử lí lỗi)
  Những phương thức này sẽ được gọi khi có lỗi trong quá trình render, trong một phương thức lifecycle ở trên hoặc in hàm khởi tạo(constructor) của bất kì component con nào

* static getDerivedStateFromError()
* componentDidCatch()

## Các phương thức về lifecycle trong class component

# constructor()

`constructor()` cho React component được gọi trước khi component được gắn vào DOM. Khi thực thi constructor, chúng ta nên gọi `super(props)` trước bất kì câu lệnh nào khác. Nếu không, this.props sẽ là `undefined` trong constructor và có thể dẫn đến bugs.

Thông thường, constructor được sử dụng với 2 mục đích như sau:

- Khởi tạo các local state(https://reactjs.org/docs/state-and-lifecycle.html) thông qua việc gán this.state bằng một object.
- Ràng buộc các phương thức xử lí sự kiện cho 1 instance

Lưu ý:

- Constructor là nơi duy nhất mà bạn nên gán state 1 cách trực tiếp(this.state). Còn trong tất cả các phương thức còn lại bạn cần sử dụng this.setState() nhé.
- Bạn cần tránh xử lí side-effects trong constructor. Thay vào đó, chúng ta nên sử dụng componentDidMount().

```jsx
constructor(props) {
  super(props);
  this.state = { counter: 0 };
  this.handleClick = this.handleClick.bind(this);
}
```

# getDerivedStateFromProps()

- static getDerivedStateFromProps()

```jsx
static getDerivedStateFromProps(props, state)
```

getDerivedStateFromProps được gọi ngay trước khi `render()` được gọi, trong cả lần gắn đầu tiên và những lần update kế tiếp. Phương thức này tồn tại cho các trường hợp hiếm sử dụng khi mà state phụ thuộc vào sự thay đổi của props theo thời gian. Ví dụ, phương thức này có thể tiện lợi cho việc thực thi `<Transition />` component khi mà so sánh children trước và kế tiếp để xác định xem cái nào sẽ được đưa ra hoặc đưa vào.

- Tương tự constructor(), chúng ta cũng ko nên "Cause Side-Effect" ở đây.

# getSnapshotBeforeUpdate()

```jsx
getSnapshotBeforeUpdate(prevProps, prevState);
```

getSnapshotBeforeUpdate() được gọi ngay trước khi kết quả của hàm `render()` được gắn vào DOM. Nó giúp cho component có thể giữ 1 sô thông tin từ DOM trước khi nó bị thay đổi. Và giá trị trả về của phương thức này sẽ được truyền như là một tham số của `componentDidUpdate()`

```jsx
class User extends React.Component {
  constructor() {
    super();
    this.state = {
      email: "example@gmail.com",
    };
  }
  componentDidMount() {
    this.setState({ email: this.props.email });
  }
  getSnapshotBeforeUpdate(prevProps, prevState) {
    document.getElementById("previous").innerHTML =
      "Previous Email: " + prevState.email;
  }
  componentDidUpdate() {
    document.getElementById("new").innerHTML =
      "Current Email: " + this.state.email;
  }
  render() {
    return (
      <div>
        <div id="previous">Previous Email: </div>
        <div id="new">New Email: </div>
      </div>
    );
  }
}
```

# shouldComponentUpdate()

```jsx
shouldComponentUpdate(nextProps, nextState);
```

Sử dụng phương thức này để cho component của bạn thoát khỏi update lifecycle và ngăn chặn việc render lại của component đó nếu không có sự thay đổi thật sự nào về state và props. Như chúng ta đã biết thì việc render lại xảy ra khi `state` và `props` thay đổi. Phương thức này đượv gọi ngay trước `render()`. Giá trị mặc định của nó là true, nếu trả về true, component sẽ bị render lại và ngược lại.
Đây là phương thức giúp chúng ta tối ưu hóa performance. Bạn có thể xem xét sử dụng nó một cách hợp lí.

Một ví dụ đơn giản:

```jsx
  shouldComponentUpdate(nextProps, nextState) {
  if (this.props.level !== nextProps.level) {
    return true;
  }
  if (this.state.age !== nextState.age) {
    return true;
  }
  return false;
}
```

**Lưu ý**: Phương thức này sẽ không được gọi trong lần render đầu tiên hoặc khi `forceUpdate()` được sử dụng.
Nếu phương thức này trả về false, cả `render()` và `componentDidUpdate()` sẽ không được gọi.

# componentDidUpdate()

```jsx
componentDidUpdate(prevProps, prevState, snapshot);
```

`componentDidUpdate()` được gọi ngay sau khi việc update diễn ra. Phương thức này sẽ không được gọi trong lần render đầu tiên đâu,vì nó chỉ nằm trong giai đoạn updating của component mà thôi nhé.

Phương thức này phù hợp cho việc thực thi các network request miễn là bạn phải so sánh props hiện tại vs props trước đó nha.
Bạn có thể gọi `setState()` trực tiếp ở đây nhưng lưu ý là phải gói nó trong điều kiện nào đó, nếu không bạn gây ra vòng lặp vô hạn đó và điều này gây ảnh hưởng đến performance.

Ngoài ra, nếu component của bạn thực thi phương thức `getSnapshotBeforeUpdate()`, giá trị trả về của phương thức này sẽ được truyền xuống tham số thứ 3 là `snapshot` của `componentDidUpdate()`. Nếu không, `snapshot` sẽ là undefined

```jsx
componentDidUpdate(prevProps) {
  if (this.props.userID !== prevProps.userID) {
    // đừng quên so sánh nhé, nếu userID không thay đổi sau khi update, thì việc fetchData là không cần thiết
    this.fetchData(this.props.userID);
  }
}
```

# componentDidMount()

`componentDidMount()` sẽ được gọi ngay sau khi component đã được mounted (inserted into the tree). Có nghĩa là khi đó component đã được insert vào virtual DOM(DOM ảo) và chuẩn bị được update lên real DOM(DOM thật). Đây là lúc thích hợp để bạn load data từ remote endpoint, từ localstorage, cookie... (Cause Side Effect).
Bạn có thể `setState()` trực tiếp được ở trong đây nha. Mặc dù nó sẽ trigger an extra rendering, nhưng nó sẽ xảy ra trược khi trình duyệt update lên màn hình. Điều này đảm bảo mặc dù `render()` được gọi 2 lần nhưng người dùng sẽ không thể thấy được state trung gian.
Lúc này nhiều bạn sẽ tự hỏi là: tại sao nên call api ở đây nhưng lại không call `setState()`?? Chúng ta hoàn toàn có thể call `setState()` bên trong `then` block của Promise kèm điều kiện bên ngoài khi call HTTP request nha.

```jsx
  componentDidMount() {
  fetch('https://jsonplaceholder.typicode.com/posts')
    .then(res => res.json())
    .then(data => this.setState({posts: data}))
    .catch(err => console.error(err));
}
```

# componentDidCatch()

```jsx
componentDidCatch(error, info);
```

`componentDidCatch()` được gọi sau khi có lỗi được đưa ra bởi các component con. Nó nhận vào 2 tham số

1. error: Lỗi được đưa ra
2. info: Một object với một `componentStack` key chứa thông tin về component đưa ra lỗi đó

```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    logErrorToMyService(error, errorInfo);
  }

  render() {
    if (this.state.hasError) {
      return <h1>Something went wrong.</h1>;
    }

    return this.props.children;
  }
}
```

# componentWillUnmout()

-componentWillUnmout()

`componentWillUnmount()`sẽ được gọi nay trước khi component bị gỡ khỏi DOM. Đây là nơi hợp lí để chúng ta thực hiện cleanup, như là clear timers, hủy bỏ networks request,......
**Lưu ý** Bạn không nên gọi `setState()` trong phương thức này nhé. Bởi vì component sẽ không bao giờ được render lại nữa đâu.

```jsx
class Clock extends React.Component {
  constructor(props) {
    super(props);
    this.state = { date: new Date() };
  }
  tick() {
    this.setState({
      date: new Date(),
    });
  }

  componentDidMount() {
    this.timerID = setInterval(() => this.tick(), 1000);
  }

  componentWillUnmount() {
    clearInterval(this.timerID);
  }

  render() {
    return (
      <div>
        <h2>Now: {this.state.date.toLocaleTimeString()}.</h2>
      </div>
    );
  }
}
```

# render()

Như đã trình bày ở trên, `render()` làm hàm duy nhất bắt buộc đối trong class component.

`render()` có thể trả về một trong số các type sau:

- React Element: Thông thương được tạo qua `JSX`. Ví dụ `<div />` hoặc `<MyComponent />` là những React Elements mà sẽ được giúp React render một DOM node or một component theo người dùng định nghĩa.
- Array and fragments: Để bạn trả về nhiều elements từ render.
- Portals: Cho phép bạn render children vào một cây con DOM khác.
- Strings and numbers: Chúng được coi như là text nodes trong DOM.
- Boolean or null: Không render ra gì cả.
  Sau đó, react sẽ render Child components, cũng theo một vòng như trên.

## Thay thế Component lifecycle methods bằng React Hooks

Ngoài cách viết component bằng class, chúng ta cũng có thể viết component bằng function nữa, được gọi là các functional Component. Và đối với functional Component, chúng ta có lẽ đều quan thuộc với Hooks. Vậy chúng ta có thể thay thế được các phương thức lifecycle bằng hooks được không? Câu trả lời là có nhé :)

Chúng ta sẽ cùng đi đến từ ví dụ dưới đây nhé

# componentDidMount()

```jsx
function MyExample() {
  const [count, setCount] = useState(0);
  useEffect(() => {
    document.title = `You clicked ${count} times`;
  }, []);
  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

Ở ví dụ trên, `useEffect` với tham số thứ 2 là một mảng rỗng([]), để thông báo cho cho `useEffect` biết rằng nó chỉ được thực thi một lần duy nhất mà thôi, đó là khi Component được gắn vào DOM ảo.

# componentDidUpdate()

- TH1: Hooks được gọi sau mỗi lần render

```jsx
function MyExample() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  });

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

Như chúng ta đã thấy ở ví dụ trên, tham số thứ 2 của `useEffect` đã bị trống, nghĩa là nó sẽ được gọi sau mỗi lần render lại.

- TH2: Hooks được gọi khi dependencies bị thay đổi

```jsx
function MyExample() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    document.title = `You clicked ${count} times`;
  }, [count]);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

Ở vị dụ trên, chúng ta thấy tham số thứ 2 là 1 mảng các dependencies, và nó sẽ tự động được gọi bất cứ khi nào một trong các dependencies thay đổi.

# ComponentWillUnmount()

```jsx
function Clock() {
  const [date, setDate] = React.useState(new Date());
  React.useEffect(() => {
    const timerID = setInterval(() => {
      setDate(new Date());
    }, 1000);
    return () => {
      clearInterval(timerID);
    };
  }, []);
  return (
    <div>
      <h2>Now: {date.toLocaleTimeString()}.</h2>
    </div>
  );
}
```

Ở ví dụ trên chúng ta cũng thấy `useEffect` với tham số thứ 2 là một mảng trống([]) giống như ví dụ ở componentDidMount vậy. Tuy nhiên có thêm một callback được trả về trong `useEffect`, và hàm callback sẽ được thực thi trước khi component được gỡ khỏi DOM.

# shouldComponentUpdate()

Đây là một phương thức giúp tránh việc render lại khi không cần thiết nhằm mục đích tối ưu hóa chương trình.
Đối với hooks, Bạn có thể sử dụng `React.PureComponent` hoặc `React.Memo` để ngăn chặn việc render của các components.

```jsx
const MyComponent = React.memo(function MyComponent(props) {
  // Component sẽ chỉ được render lại nếu props thay đổi
});
```

# getDerivedStateFromProps()

```jsx
function ScrollView({ row }) {
  const [isScrollingDown, setIsScrollingDown] = useState(false);
  const [prevRow, setPrevRow] = useState(null);

  if (row !== prevRow) {
    setIsScrollingDown(prevRow !== null && row > prevRow);
    setPrevRow(row);
  }
  return `Scrolling down: ${isScrollingDown}`;
}
```

Ở ví dụ trên chúng ta muốn lưu giá trị của row props vào biến prevRow để chúng ta có thể so sánh ở những lượt render tiếp theo.

## Recap

React Lifecycle là một kiến thức nền tảng cực kì quan trọng đối với bất kì ai sử dụng ReactJs trong lập trình. Chúng ta cần ghi nhớ và hiểu được các nội dung:

- Vòng đời của một component trong React
- Các phương thức về lifecyle của class component
- Thay thế các phương lifecycle bằng React Hooks trong functional component

### 6. PropTypes
### Giới thiệu

Khi chúng ta xây dựng các ứng dụng lớn, chúng ta có thể gặp khó khăn với việc kiểm tra kiểu dữ liệu (typechecking). Chúng ta có thể lựa chọn `Flow` hoặc `Typescript` để giúp chúng ta thực hiện việc typecheck. Nếu chúng ta không sử dụng các công cụ trên trên thì React cũng có khả năng typechecking được xây dựng sẵn. Và đó là `PropTypes` - 1 props đặc biệt giúp chúng ta kiểm tra các kiểu dữ liệu của các props mà component nhận vào.

### Cách sử dụng

```jsx
import PropTypes from "prop-types";

MyComponent.propTypes = {
  // Bạn có thể thấy, props có kiểu giống với kiểu dữ liệu trong Javascript.
  optionalArray: PropTypes.array,
  optionalBool: PropTypes.bool,
  optionalFunc: PropTypes.func,
  optionalNumber: PropTypes.number,
  optionalObject: PropTypes.object,
  optionalString: PropTypes.string,
  optionalSymbol: PropTypes.symbol,

  optionalNode: PropTypes.node,

  // React Element
  optionalElement: PropTypes.element,

  optionalElementType: PropTypes.elementType,

  // Bạn có thể khai báo nó là một instance của một class nữa.

  optionalMessage: PropTypes.instanceOf(Message),

  // Có thể là Enum nữa, Enum có thể hiểu là 1 biến lưu tập hợp các hằng nhé
  optionalEnum: PropTypes.oneOf(["News", "Photos"]),

  // Nó thể là một object chứa một trong số các type
  optionalUnion: PropTypes.oneOfType([
    PropTypes.string,
    PropTypes.number,
    PropTypes.instanceOf(Message),
  ]),

  // Một mảng với kiểu dữ liệu cụ thể, ở đây là number chẳng hạn
  optionalArrayOf: PropTypes.arrayOf(PropTypes.number),

  // Một object với kiểu dữ liệu cụ thể, ở đây là number
  optionalObjectOf: PropTypes.objectOf(PropTypes.number),
};
```

Ví dụ trên minh họa cơ bản cách sử dụng `PropTypes` của React. Bạn có thể sử dụng nó đối với cả Class Component và Functional Component.

- Class Component

```jsx
import PropTypes from "prop-types";
class Example extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
Example.propTypes = {
  name: PropTypes.string,
};
```

- Functional Component

```jsx
import PropTypes from "prop-types";
function Example({ name }) {
  return <h1>Hello, {name}</h1>;
}
Example.propTypes = {
  name: PropTypes.string,
};
```

Thật là dễ hiểu đúng không nào, bởi vì chúng ta đã quen với việc khai báo kiểu dữ liệu với biến ở các ngôn ngữ lập trình khác như C, C++, Java,...

- Default Prop Values
  Chắc chắn các bạn sẽ thắc mắc là liệu chúng ta có thể truyền giá trị mặc định với `PropTypes` không bởi vì Javascript cho phép chúng ta truyền giá trị mặc định cho tham số của một hàm mà. Câu trả lời là hoàn toàn có nhé. Chúng ta cùng xem ví dụ sau.

```jsx
class Example extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}</h1>;
  }
}
Greeting.defaultProps = {
  name: "NCC",
};
```

Việc sử dụng ` defaultProps` sẽ đảm bảo props sẽ luôn có sẵn một giá trị, kể cả nếu nó không được mô tả ở các Component cha. Và việc kiểm tra kiểu của `propTypes` xảy ra sau khi `defaultProps` được xác định, nên việc kiểm tra kiểu cũng sẽ được áp dụng cho `defaultProps`

### Recap

- `propTypes` là một công cụ rất hữu ích cho chúng ta trong việc định nghĩa, ràng buộc và kiểm tra kiểu dữ liệu cho các props trong một Component. Nó sẽ giúp chúng ta kiểm tra lỗi dễ hơn, giúp cho những người mới vào project dễ dàng làm quen và hiểu nhanh hơn các Component và chức năng của nó. Các bạn nên xem xét và sử dụng `propTypes` nhé!


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
