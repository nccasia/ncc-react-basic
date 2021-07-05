# 05: React Hooks

Table of contents

- [05: React Hooks](#05-react-hooks)
  - [1. Introducing Hooks](#1-introducing-hooks)
  - [2. Using the State Hook](#2-using-the-state-hook)
    - [Giới thiệu](#giới-thiệu)
    - [Cú pháp sử dụng](#cú-pháp-sử-dụng)
    - [Ví dụ giữa state trong Class component và `useState` Hook](#ví-dụ-giữa-state-trong-class-component-và-usestate-hook)
    - [Recap](#recap)
  - [3. Using the Effect Hook](#3-using-the-effect-hook)
    - [Giới thiệu](#giới-thiệu-1)
    - [Cú pháp sử dụng](#cú-pháp-sử-dụng-1)
    - [`useEffect` chỉ chạy khi `componentDidMount`?](#useeffect-chỉ-chạy-khi-componentdidmount)
    - [`useEffect` chỉ chạy khi `componentDidMount` và `componentDidUpdate`?](#useeffect-chỉ-chạy-khi-componentdidmount-và-componentdidupdate)
    - [`useEffect` chỉ chạy khi `componentWillUnmount`?](#useeffect-chỉ-chạy-khi-componentwillunmount)
    - [Recap](#recap-1)
  - [4. Using the Context Hook](#4-using-the-context-hook)
    - [Giới thiệu](#giới-thiệu-2)
    - [Cú pháp sử dụng](#cú-pháp-sử-dụng-2)
    - [Ví dụ giữa context trong Class component và `useContext` Hook](#ví-dụ-giữa-context-trong-class-component-và-usecontext-hook)
    - [Recap](#recap-2)
  - [5. Building your own Hooks](#5-building-your-own-hooks)
    - [Cách khai báo cơ bản](#cách-khai-báo-cơ-bản)
    - [Ví dụ mẫu](#ví-dụ-mẫu)
    - [Hãy sử dụng Hook theo cách của bạn](#hãy-sử-dụng-hook-theo-cách-của-bạn)
  - [6. Additional Hooks](#6-additional-hooks)
    - [`useReducer` Hook](#usereducer-hook)
    - [`useCallback` Hook](#usecallback-hook)
    - [`useMemo` Hook](#usememo-hook)
    - [`useRef` Hook](#useref-hook)
    - [Các Additional Hooks còn lại?](#các-additional-hooks-còn-lại)

---

## 1. Introducing Hooks

Theo như định nghĩa trên trang chủ của React: _Hooks là các hàm (hay các API) mà cho phép bạn “hook into (móc vào)” trạng thái của React và các tính năng vòng đời từ các hàm components. Hooks không hoạt động bên trong classes — chúng cho phép bạn sử dụng React không cần classes._

Hook không hề ảnh hưởng tới các khái niệm cũ của React, và bạn hoàn toàn có thể sử dụng chúng song song với React Class Components trong cùng dự án mà không hề sợ xung đột nào xảy ra (trừ khi là lỗi do code của bạn, hihi).

Nếu bạn muốn thực sự hiểu ý nghĩa và lý do cần sử dụng Hook thì có thể đọc thêm [tại đây](https://medium.com/@dan_abramov/making-sense-of-react-hooks-fdbde8803889).

**Cần chú ý một số điều là:**

- Hook chỉ dùng cho functional component, không dùng cho class component.
- Bạn có thể dùng nhiều hooks trong 1 component, nhưng phải được khai báo trên cùng của component đó, tuyệt đối không được khai báo trong: vòng lặp, phép điều kiện hay các functions con.
- Đối với `useEffect`, việc gọi API cần được kiểm tra điều kiện thực hiện, nếu không hàm sẽ gửi các requests liên tục. Ví dụ bạn cần phải kiểm tra dữ liệu chưa tồn tại thì mới request GET dữ liệu.

Cho tới thời điểm hiện tại (02/07/2021) dựa trên trang chính thức của React, có các Hooks sau:

- [Basic Hooks](https://reactjs.org/docs/hooks-reference.html#basic-hooks)
  - [`useState`](https://reactjs.org/docs/hooks-reference.html#usestate)
  - [`useEffect`](https://reactjs.org/docs/hooks-reference.html#useeffect)
  - [`useContext`](https://reactjs.org/docs/hooks-reference.html#usecontext)
- [Additional Hooks](https://reactjs.org/docs/hooks-reference.html#additional-hooks)
  - [`useReducer`](https://reactjs.org/docs/hooks-reference.html#usereducer)
  - [`useCallback`](https://reactjs.org/docs/hooks-reference.html#usecallback)
  - [`useMemo`](https://reactjs.org/docs/hooks-reference.html#usememo)
  - [`useRef`](https://reactjs.org/docs/hooks-reference.html#useref)
  - [`useImperativeHandle`](https://reactjs.org/docs/hooks-reference.html#useimperativehandle)
  - [`useLayoutEffect`](https://reactjs.org/docs/hooks-reference.html#uselayouteffect)
  - [`useDebugValue`](https://reactjs.org/docs/hooks-reference.html#usedebugvalue)

## 2. Using the State Hook

### Giới thiệu

Ngày xửa ngày xưa, khi nhắc tới stateful components, ta lại nhắc tới tạo ra một class component trong React. Ai cũng biết functional component là **"stateless"** (không có state).

Nhưng với `useState` Hook, các bạn có trong tay ma lực biến mọi thứ trở nên **"stateful"**. Hiểu nôm na là giờ mình <u>dùng được state trong functional components</u> nhé các bạn :joy:. Nhìn chung thì cũng dễ hiểu lắm nhé.

### Cú pháp sử dụng

`useState` Hook cho phép chúng ta khai báo 1 local state trong functional component với cú pháp:

```jsx
const [state, setState] = useState(initialStateValue);
```

Trong đó:

- **state:** định nghĩa tên biến để lưu giá trị của state, giá trị của state có thể là đơn giá trị, một object, hay một mảng, .v.v.
- **setState:** là hàm dùng để thay đổi giá trị của state, việc cập nhật giá trị của state bắt buộc phải thông qua hàm này nha!
- **initialStateValue:** là giá trị khởi tạo của state, giá trị này chỉ có ý nghĩa duy nhất 1 lần khi state được "sinh ra" thui nha!

### Ví dụ giữa state trong Class component và `useState` Hook

Giả sử ngày xưa, bạn code một ứng dụng "ăn chuối" kiểu kiểu như này :laughing::

```jsx
import React, { Component } from 'react';

class BananaEating extends Component {
  constructor(props) {
    super(props);
    this.state = {
      bananas: 0,
    };
  }
  eatOneBanana() {
    const { bananas } = this.state;
    this.setState({ bananas: bananas + 1 });
  }
  render() {
    const { bananas } = this.state;
    return (
      <div>
        <p>Bạn đã ăn {bananas} quả chuối!!!</p>
        <button onClick={this.eatOneBanana.bind(this)}>
          Đói quá, ăn thêm quả nữa :D
        </button>
      </div>
    );
  }
}
```

Chà, trông khá "cồng kềnh" nhỉ, giờ hãy xem đoạn code trở nên ngắn gọn, rõ ràng, sạch sẽ hơn sau khi sử dụng `useState` như nào nhé:

```jsx
import React, { useState } from 'react';

const BananaEating = () => {
  const [bananas, setBananas] = useState(0);
  const eatOneBanana = () => setBananas(bananas + 1);

  return (
    <div>
      <p>Bạn đã ăn {bananas} quả chuối!!!</p>
      <button onClick={eatOneBanana}>Đói quá, ăn thêm quả nữa =)))))</button>
    </div>
  );
};
```

Magiccccccccc!!!! :joy:

### Recap

- `useState` giúp functional component có thể dùng state.
- `useState` nhận vào 1 giá trị khởi tạo và trả về một mảng 2 phần tử [state, setState].
- `useState` áp dụng **replacing** thay vì **merging** như bên class component.
- Initial state callback chỉ thực thi 1 lần đầu.

## 3. Using the Effect Hook

### Giới thiệu

Nhớ cái thuở ấy, bạn phải ngồi quản lý vòng đời của class component với cả tá phương thức như `componentDidMount`, `componentDidUpdate`, `componentWillUnmount`, .v.v. Code trong components của bạn trở nên thật hỗn loạn và đáng sợ. Thậm chí đôi lúc ta còn phải lặp lại code ở các phương thức lifecycle khác nhau, xong còn [gặp vấn đề](https://reactjs.org/docs/hooks-effect.html#explanation-why-effects-run-on-each-update) nữa chứ! :cry:

Nhưng ngày đó sẽ không còn nữa, một mình `useEffect` giờ đây có thể "cân hết" toàn bộ các phương thức trên, bạn sẽ dễ dàng quản lý lifecycle của bất kì functional component nào.

### Cú pháp sử dụng

`useEffect` Hook cho phép chúng ta khải báo sử dụng 1 effect với cú pháp như sau:

```jsx
useEffect(handlerFunction, paramsArray?)
```

Trong đó:

- **handlerFunction:** là hàm thực thi một công việc nào đó khi effect được khởi chạy
- **paramsArray (optional):** là một mảng các tham số để theo dõi, khi giá trị các tham số này thay đổi, effect mới được khởi chạy.

Thông thường, khi không cung cấp `paramsArray`, effect sẽ được chạy trong mọi lần component re-render. Điều này có thể dẫn tới một số vấn đề không mong muốn về hiệu năng do `handlerFunction` sẽ được gọi rất nhiều lần một cách mất kiểm soát (thậm chí là vô hạn). Vì thế, bạn có thể tham khảo một số cách sử dụng thông dụng ở phần dưới nhé.

### `useEffect` chỉ chạy khi `componentDidMount`?

Ta chỉ cần truyền `paramsArray` là 1 mảng rỗng, như vậy effect sẽ chỉ được gọi duy nhất 1 lần sau khi component hoàn thành việc render ra màn hình:

```jsx
useEffect(() => {
  console.log('Component đã được render!');
}, []);
```

### `useEffect` chỉ chạy khi `componentDidMount` và `componentDidUpdate`?

Thay vì mảng rỗng, `paramsArray` giờ sẽ nhận vào 1 biến chứa giá trị cần theo dõi khi nó được update để thực thi effect:

```js
useEffect(() => {
  console.log('Effect được gọi rùi nè!');
  setFullName({ name: 'Hoa Tran', familyName: 'HTB' });
}, [fullName.name]);
```

### `useEffect` chỉ chạy khi `componentWillUnmount`?

Trên thực tế, `handlerFunction` sẽ có thể return 1 function khác, và function này sẽ được thực thi khi component đó được unmount:

```jsx
useEffect(() => {
  console.log('Component đã được render!');

  // return 1 function, sẽ được gọi ngay khi componentWillUnmount
  return () => {
    console.log('Component chuẩn bị unmount!');
  };
}, []); // Nhớ thêm paramsArray để tránh việc effect bị gọi vô số lần nhé!
```

Đó, đơn giản như đan rổ phải không các bạn? :laughing:

### Recap

- `useEffect` giúp quản lý lifecycle trong functional components dễ dàng.
- `useEffect` mặc định không có `paramsArray` sẽ thực thi trong mọi lần re-render của component.
- Cần quản lý tốt điều kiện thực thi của `useEffect` để tránh việc chạy effect không mong muốn.
- `handlerFunction` trong `useEffect` có thể return 1 function khác, được chạy khi component unmount.

## 4. Using the Context Hook

### Giới thiệu

Như ta đã đã biết... À khoan, nếu chưa thì bạn nên tìm hiểu trước [React Context](https://reactjs.org/docs/context.html) là gì rồi quay lại đây đọc tiếp nha :blush:

Về cơ bản, Context cung cấp ta cách để có thể chia sẻ các dữ liệu "toàn cục" trong cây components dễ dàng hơn mà không cần phải truyền props xuống từng nhánh một. Đương nhiên, bạn có thể dùng Redux hay MobX, nhưng đó là các <u>thư viện hỗ trợ bên ngoài</u> cần cài đặt thêm, chứ không phải mặc định của React.

Trước đây, trong class components, bạn có thể truy cập context thông qua `contextType` hoặc sử dụng `Context.Consumer`. Còn bây giờ, đối với functional components, bạn có thể sử dụng `useContext` Hook như một sự thay thế hoàn hảo!

### Cú pháp sử dụng

`useContext` Hook cho phép chúng ta truy cập tới 1 context như sau:

```jsx
const value = useContext(Context);
```

Trong đó:

- **value:** sẽ là biến chứa giá trị context hiện tại được trả về.
- **Context:** là React Context mà bạn đã tạo trước đó.

Đơn giản phải không nào, bạn giờ có thể lấy trực tiếp giá trị của context và sử dụng nó như một biến thông thường trong function rồi. :happy:

### Ví dụ giữa context trong Class component và `useContext` Hook

Mình sẽ ví dụ để làm một bộ đếm Counter quen thuộc nhé! Trước tiên thì đối với cả 2 cách, bạn sẽ đều cần phải tạo trước 1 React Context ha:

```jsx
import React from 'react';

// Tạo 1 context mới với giá trị khởi tạo rỗng - bạn có thể truyền vào nếu bạn muốn
const CounterContext = React.createContext();
export default CounterContext;
```

Tiếp đến là code ở nơi sử dụng `Provider` cũng giống nhau như sau:

```jsx
import React from 'react';
import CounterContext from './CounterContext';
import CounterInfo from './CounterInfo';

const App = () => {
  // Mình tận dụng những gì đã học ở trên vô đây luôn ha ^^!
  const [count, setCount] = useState(0);
  const increase = () => {
    setCount(count + 1);
  };
  const decrease = () => {
    setCount(count - 1);
  };

  // Nhớ bọc ngoài bởi Provider của Context nếu muốn sử dụng nhé!
  return (
    <div>
      <CountContext.Provider value={{ count, increase, decrease }}>
        <CounterInfo />
      </CountContext.Provider>
    </div>
  );
};

export default App;
```

OK, xong phần base chung rồi, giờ điểm khác nhau của chúng ta đó chính là component `Counter` đang được bọc bên trong `CounterContext.Provider`.

Đối với cách sử dụng class component, ta có đoạn code như sau:

```jsx
import React from 'react'
import Counter from './CounterContext'

class CounterInfo extends React.Component {
	render() {
    return (
      <Counter.Consumer>
        {{{ count, increase, decrease }} => (
          <div className="wrapper">
            <button onClick={decrease}>-</button>
            <span>{count}</span>
            <button onClick={increase}>+</button>
          </div>
        )}
      </Counter.Consumer>
    )
  }
}
```

Chà, trông cũng rõ ràng, dễ hiểu ha, nhưng chưa là gì so với việc sử dụng `useContext` Hook đâu ạ, cùng xem nhé:

```jsx
import CounterContext from './CounterContext';

const CounterInfo = () => {
  // Trông chả khác gì local variables, bạn dễ dàng sử dụng ở bất cứ đâu trong function ^^
  const { count, increase, decrease } = useContext(CounterContext);
  return (
    <div className='wrapper'>
      <button onClick={decrease}>-</button>
      <span>{count}</span>
      <button onClick={increase}>+</button>
    </div>
  );
};
```

Easy again, right? :100:

### Recap

- `useContext` giúp ta sử dụng React Context bên trong functional component.
- Vẫn cần phải tạo React Context và bọc component bên trong Provider mới có thể truy cập được context.
- `useContext` khiến cho việc sử dụng context linh hoạt bên trong component, hệt như sử dụng các local variables thông thường.

## 5. Building your own Hooks

Tới thời điểm này, các bạn hẳn đã thấy được ý nghĩa to lớn mà Hook đem lại cho chúng ta. Và còn ý nghĩa hơn thế, lập trình viên có thể tự định nghĩa ra Hook của riêng mình để thực hiện một số chức năng nào đó, nó sẽ thường được sử dụng để chia sẻ logic giữa các components.

### Cách khai báo cơ bản

Việc định nghĩa một Hook cơ bản là như sau:

```jsx
const useSomething = (input?) => {
  // Bạn có thể làm bất cứ việc gì tại đây
  return output;
};

export default useSomething;
```

Và sử dụng chúng như các Hook mặc định của Javascript

```jsx
import useSomething from './useSomething';

const exampleComponent = () => {
  const output = useSomething(input);
  // ... code xử lý khác
  return output;
};

export default useSomething;
```

**Trong đó, cần chú ý:**

- Hook bản chất là một function thông thường.
- Tên của Hook bắt buộc phải bắt đầu bằng chữ `use`.
- Hook có thể sử dụng nhiều Hooks khác bên trong nó.
- Hooknhận vào `input` (hoặc không), thực hiện công việc nhất định, và return `output` (hoặc không).
- `output` của Hook có thể là đơn giá trị, một object, một mảng, .v.v.
- Hook là độc lập trong mỗi component sử dụng, nó chỉ chia sẻ logic xử lý chung, không chia sẻ state hay effects bên trong.

### Ví dụ mẫu

Ở đây mình sẽ có một ví dụ mẫu cho các bạn, trong ví dụ này, mình tạo ra 2 bộ đếm Counter <u>có chung logic xử lý</u>, chỉ khác nhau UI. Vì thế, mình sẽ cần tạo ra 1 custom Hook để chia sẻ logic chung cho 2 bộ đếm đó:

```jsx
import React, { useState } from 'react';

/*
 * 1 bộ đếm sẽ có state để quản lý số đếm là count,
 * với giá trị khởi tạo được truyền vào Hook là initialCount.
 *
 * 2 hàm tăng, giảm là incrementCount và decrementCount,
 * chúng sẽ gọi tới hàm setCount để thay đổi state.
 *
 * Cuối cùng trả về output cần cho nơi sử dụng Hook,
 * ở đây là giá trị số đếm và 2 hàm thay đổi giá trị.
 */
const useCounter = (initialCount) => {
  const [count, setCount] = useState(initialCount);
  const incrementCount = () => setCount(count + 1);
  const decrementCount = () => setCount(count - 1);

  return [count, incrementCount, decrementCount];
};
```

Giả sử ta có 2 bộ đếm là `Counter1` và `Counter2`, chúng sẽ xử dụng lại logic của `useCounter` như sau:

```jsx
import useCounter from './useCounter';

export const Counter1 = () => {
  /*
   * useCounter nhận vào initialCount là giá trị khởi tạo của state
   * Sau đó nó return 1 mảng 3 phần từ [count, incrementCount, decrementCount]
   * Vì thế ở đây ta sử dụng Array Destructing để nhanh chóng lấy ra được 3 phần tử đó
   */
  const [count, incrementCount, decrementCount] = useCounter(0);
  return (
    <div>
      <button onClick={decrementCount}>-</button>
      {count}
      <button onClick={incrementCount}>+</button>
    </div>
  );
};

export const Counter2 = () => {
  // Tương tự như trên, chỉ khác mỗi input nha ^^
  const [count, incrementCount, decrementCount] = useCounter(100);
  return (
    <div>
      <div onClick={decrementCount}>-</div>
      {count}
      <div onClick={incrementCount}>+</div>
    </div>
  );
};
```

Thật dễ dàng và nhanh gọn phải không ạ? Và giờ ta chỉ cần sử dụng 2 bộ đếm kia một cách "bình thường như cân đường hộp sữa" mà thôi :laughing:

```jsx
import { Counter1, Counter2 } from './counters';

const App = () => {
  return (
    <div>
      <Counter1 />
      <br />
      <Counter2 />
    </div>
  );
};

export default App;
```

### Hãy sử dụng Hook theo cách của bạn

Phần trên chỉ là ví dụ mẫu vô cùng đơn giản về một tình huống mà bạn có thể tự custom Hook cho riêng mình. Trên thực tế, việc đưa ra quyết định có tạo Hook mới hay không tuỳ thuộc nhiều vào tình huống của dự án cũng như kinh nghiệm của chính bạn trong công việc.

Vì thế, hiểu cách tạo ra nó là một chuyện, còn dùng nó như nào, ra sao một cách hợp lý lại là một câu chuyện khác. Bạn chỉ có thể tự có đáp án cho riêng mình bằng cần bắt đầu tiếp cận và sử dụng Hook trong các dự án của mình mà thôi. Good luck! :four_leaf_clover:

## 6. Additional Hooks

Như đã nói ở [Phần 1](#1-introducing-hooks), React ngoài cung cấp 3 Basic Hooks thì cũng còn thêm 7 Additional Hooks nữa.

Gọi là **"Additional Hooks (Hooks bổ sung)"** là bởi các Hook này thường chỉ được sử dụng trong một số trường hợp chuyên biệt nhất định. Vậy nên bạn cũng đừng tự làm bản thân stress khi cố gắng học chúng nhé! :laughing:

Phần này mình xin phép nói qua và trực tiếp vào vấn đề của các Hooks nhé!

### `useReducer` Hook

Nói đơn giản và dễ hiểu thì, `useReducer` là phiên bản nâng cấp của `useState` và cách thức hoạt động của nó giống với [React-Redux reducer](https://redux.js.org/tutorials/fundamentals/part-3-state-actions-reducers#writing-reducers):

Ta sẽ xem qua một ví dụ đầy đủ như sau:

```jsx
// Tạo 1 hàm init động để thực hiện Lazy initialization
const init = (initialCount) => {count: initialCount}

// Khai báo 1 reducer và định nghĩa các logic thực hiện ứng với mỗi loại action
const reducer = (state, action) {
  switch (action.type) {
    case 'INCREMENT':
      return {count: state.count + 1}
    case 'DECREMENT':
      return {count: state.count - 1}
    case 'RESET':
    	return init(action.payload)
    default:
      throw new Error()
  }
}

const Counter = ({ initialCount }) => {
  /*
   * useReducer ở đây nhận đầu vào lần lượt 3 tham số là:
   * 1. reducer đã được định nghĩa trước đó
   * 2. Giá trị khởi tạo ban đầu initialCount
   * 3. Hàm init để trả về initialState đối với giá trị khởi tạo ban đầu
   *
   * Có thể truyền initialState trực tiếp mà không cần hàm init
   * useReducer(reducer, initialState)
   *
   * useReducer trả về 1 mảng gồm:
   * 1. state là biến chứa giá trị hiện tại của state
   * 2. dispatch là hàm kích hoạt một action nào đó trong reducer để thay đổi state
   */
  const [state, dispatch] = useReducer(reducer, initialCount, init)
  return (
    <>
      Count: {state.count}
      <button
        onClick={() => dispatch({type: 'RESET', payload: initialCount})}
      >
        Reset
      </button>
      <button onClick={() => dispatch({type: 'DECREMENT'})}>-</button>
      <button onClick={() => dispatch({type: 'INCREMENT'})}>+</button>
    </>
  )
}
```

Ví dụ đầy đủ phía trên đã có giải thích chi tiết về việc sử dụng `useReducer` Hook ra sao. Lời khuyên dành cho bạn là nên tìm hiểu [React-Redux](https://react-redux.js.org/tutorials/quick-start) và sử dụng thư viện đó trong việc xử lý các vấn đề tương tự

### `useCallback` Hook

Hiểu đơn giản, `useCallback` Hook cho phép bạn lưu lại **sự tồn tại của một hàm** vào bộ nhớ giữa các lần re-render của component và chỉ thay đổi khi các sự phụ thuộc được thay đổi. Điều này có tính tương đồng với `shouldComponentUpdate` trong class component.

Vì thế, bạn có thể sử dụng `useCallback` để ngăn việc khởi tạo lại một hàm nào đó mỗi lần component được render lại. Điều này có ý nghĩa khi function đó được truyền vào như callback props của nhiều components con.

Hmmmm, cái này cũng có vẻ khó hiểu, thế thì ta lại đụng vô code sử dụng `useCallback` để hiểu dễ hơn nhé:

```jsx
/*
 * React.memo ở đây để ngăn việc re-render Button không cần thiết
 * Do ta sử dụng Button là con của Counter, nên thông thường,
 * việc Counter re-render sẽ kéo theo Button cũng bị re-render.
 * Bạn chưa cần tập trung vào React.memo ở trong chương này nha!
 */
const Button = React.memo(({ children, onClick }) => {
  useEffect(() => {
    console.log('Button được render lại nè!');
  }, [onClick]);

  return <button onClick={onClick}>{children}</button>;
});

const Counter = () => {
  const [count, setCount] = useState(0);
  const [otherCounter, setOtherCounter] = useState(0);

  const increment = () => {
    setCount(count + 1);
  };
  const incrementOtherCounter = () => {
    setOtherCounter(otherCounter + 1);
  };

  functionsCounter.add(increment);
  functionsCounter.add(incrementOtherCounter);

  console.log(functionsCounter.size);

  return (
    <>
      Count: {count}
      <Button onClick={increment}>+</Button>
      <Button onClick={incrementOtherCounter}>+ (other)</Button>
    </>
  );
};
```

Được rồi, ở đoạn code này ta chưa hề sử dụng `useCallback`, bạn thử đoán xem mỗi lần ấn 1 trong 2 `button` kia, ta sẽ nhận được kết quả nào trên console? Guess itttttt! :laughing:

Vâng, và mỗi lần ấn chỉ cần 1 nút thôi, `functionsCounter.size` đã tăng thêm 2 đơn vị rồi. Điều này chứng tỏ, mỗi lần re-render là 2 functions `increment` và `incrementOtherCounter` lại được tạo mới. Không những thế, cả 2 components `Button` cũng liên tục bị re-render lại. Well, đây chắc chắn là điều không hề tốt chút nào!

Vậy nên, để giải quyết vấn đề này, ta thay lại một số dòng code có sử dụng `useCallback` như sau:

```jsx
// ...
const increment = useCallback(() => {
  setCount(count + 1);
}, [count]);

const incrementOtherCounter = useCallback(() => {
  setOtherCounter(otherCounter + 1);
}, [otherCounter]);
// ...
```

Sau khi sử dụng `useCallback`, hàm `increment` chỉ được tạo lại khi `count` thay đổi, tương tự hàm `incrementOtherCounter` cũng sẽ chỉ tạo lại khi `otherCounter` thay đổi mà thôi. Thật tuyệt phải không nào? ^^

### `useMemo` Hook

Hiểu đơn giản, `useMemo` Hook cho phép bạn lưu lại **kết quả tính toán của một hàm** vào bộ nhớ giữa các lần re-render của component và chỉ thay đổi khi các sự phụ thuộc được thay đổi. Điều này có tính tương đồng với `shouldComponentUpdate` trong class component.

Vì thế, bạn có thể sử dụng `useMemo` để ngăn việc thực thi lại hàm mỗi lần component render lại. Điều này có ý nghĩa khi hàm của bạn thực hiện các thao tác nặng và tốn tài nguyên.

Chà, có vẻ hơi khó hiểu chút, vậy thì, ta sẽ so sánh code đơn giản giữa trường hợp sử dụng `useMemo` và không sử dụng nhé:

```jsx
const WithoutUseMemo = ({ products }) => {
  const [state, setState] = useState(0);

  /*
   * Giả sử việc tính toán của hàm calculateBigTotalPrice vô cùng lâu,
   * nó sẽ luôn được thực thi mỗi lần WithoutUseMemo re-render.
   * Điều này có thể gây ra vấn đề về hiệu năng của ứng dụng
   */
  const totalPrice = calculateBigTotalPrice(products);
};

const WithUseMemo = ({ products }) => {
  const [state, setState] = useState(0);

  /*
   * useMemo sẽ giúp cache lại kết quả của lần tính toán trước đó.
   * Hàm calculateBigTotalPrice sẽ chỉ được thực thi khi products thay đổi.
   */
  const totalPrice = useMemo(
    () => calculateBigTotalPrice(products),
    [products]
  );
};
```

### `useRef` Hook

`useRef` tạo ra một đối tượng ref với thuộc tính `current` với một giá trị khởi tạo. Đối tượng này tồn tại bền vững xuyên suốt vòng sống của component (không như các biến cục bộ).

```jsx
const refContainer = useRef(initialValue);
```

Khác với state, đối tượng do `useRef` trả về có thể được sử dụng như 1 biến của lớp, việc thay đổi nó **không làm component re-render.** Thông thường, `useRef` còn được sử dụng chính để tham chiếu tới class component/DOM một cách dễ dàng.

Để hiểu hơn về việc tham chiếu tới class component/DOM trong React, bạn nên tìm hiểu thêm về [React Refs](https://reactjs.org/docs/refs-and-the-dom.html).

Ví dụ đơn giản nhất về `useRef` là sử dụng nó để focus vào 1 input bất kì:

```jsx
const TextInputWithFocusButton = () => {
  // Khởi tạo ref với giá trị ban đầu là null
  const inputEl = useRef(null);
  const onButtonClick = () => {
    /*
     * Thuộc tính current trỏ tới đối tượng input đã được mount
     * vì thế ta có thể sử dụng các phương thức mặc định của input trong JS
     */
    inputEl.current.focus();
  };

  return (
    <>
      <input ref={inputEl} type='text' />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
};
```

Hiện tại, `ref` chỉ có thể truy xuất vào class component/DOM. Funtional component bản chất không tạo ra instance nào nên không thể truy cập thông qua `ref` được. Tiếc thật đấy :sweat:.

Đương nhiên, cũng có 1 số hướng xử lý cho vấn đề này, các bạn có thể tìm hiểu thêm về [Forwarding Refs](https://reactjs.org/docs/forwarding-refs.html) và [`useImperativeHandle` Hook](https://vi.reactjs.org/docs/hooks-reference.html#useimperativehandle) nhé!

### Các Additional Hooks còn lại?

Hiện tại chúng ta chỉ còn `useImperativeHandle`, `useLayoutEffect` và `useDebugValue` mà thôi. Những Hooks này rất hiếm khi sử dụng và chưa mang lại quá nhiều ý nghĩa cho lắm nên mình sẽ không nói ở đây.

Nếu muốn xem thêm, các bạn có thể truy cập trực tiếp đường link tới các Hooks đó trong document React mình đã gán ở [phần 1](#1-introducing-hooks) nha.
