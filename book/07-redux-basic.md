# 07: Redux Basic

__Table of Contents__

1. [Getting Started](#1-getting-started)
    - [1.1. What is Redux?](#11-what-is-redux)
    - [1.2. Why we need Redux?](#12-why-we-need-redux)
    - [1.3. Terminology](#13-terminology)
2. [Redux Data Flow](#2-redux-data-flow)
    - [2.1. Visualize Data Flow](#21-visualize-data-flow)
    - [2.2. Break into details](#22-break-into-details)
3. [State, Action, Reducer and Store](#3-state-action-reducer-and-store)
    - [3.1. State](#31-state)
    - [3.2. Action](#32-action)
      - [Action Type](#action-type)
      - [Action Payload](#action-payload)
      - [Action Creator](#action-creator)
    - [3.3. Reducer](#33-reducer)
      - [Reducer Rules](#reducer-rules)
      - [Root Reducer](#root-reducer)
      - [Split](#split)
      - [Combine](#combine)
    - [3.4. Store](#34-store)
      - [Create Store](#create-store)
4. [Dispatching Action](#4-dispatching-action)
5. [Additional: Middleware and Redux Thunk](#5-additional-middleware-and-redux-thunk)
    - [5.1. Middleware](#51-middleware)
    - [5.2. Redux Thunk](#52-redux-thunk)
---
## 1. Getting Started

### 1.1. What is Redux?

#### Definition:
**Redux** là một **predictable state management tool** (công cụ quản lý trạng thái) cho các ứng dụng Javascript. 

Nó giúp bạn viết các ứng dụng hoạt động một cách nhất quán, chạy trong các môi trường khác nhau (client, server and native) và dễ dàng để test.

#### Installation:
Để cài phiên bản ổn định và mới nhất, ta dùng:
```bash
# NPM: 
npm install redux
# Yarn:
yarn add redux
```

### 1.2. Why we need Redux?

Khi chúng ta viết những phần mềm **nhỏ** thì việc quản lý state (trạng thái) qua các thư viện hoặc tool là không cần thiết và phức tạp. Các component lúc này đều có thể dễ dàng chuyền nhau data.

> Ví dụ, trong _React_ để chia sẻ data thông qua các components, một state phải live trong component cha. Một method để update chính state này sẽ được cung cấp bởi chính component cha này và pass như props đến các components con.

```jsx
import React from 'react'

export default function List() {
    // State được live. Một mảng chứa các todo
    const todos = []
    // Method để update state
    const toggleComplete = id => {
        const newTodos = [...todos]
        newTodos.forEach((todo, index) => {
            if (index === id) {
                todo.complete = !todo.complete
            }
        })
        setTodos(newTodos)
    }
    
    return (
        <div>
            <ul>
                {
                    <!-- Chuyền state và các method cho component con --> 
                    todos.map((todo, index) => (
                        <ListItem todo={todo} key={index} id={index} checkComplete={toggleComplete}/>
                    ))
                }
            </ul>
        </div>
    )
}
```

Okay! :metal: Giờ hãy tưởng tượng rằng nếu state này phải được chia sẻ giữa các component cách khá xa nhau trong một cây components :sweat_smile:
> Lúc này từ _ListItem_ ta giả sử chuyền cho component _ListItem1_ trong nó rồi cho _ListItem2_ rồi cho _ListItem3_ để thực hiện method trên :dizzy_face:
```jsx
<>
// Tại file cha (chuyền lần 1)
//...
<ListItem todo={todo} key={index} id={index} checkComplete={toggleComplete}/>

// Chuyền tới file con (lần 2)
<ListItem1 todo={todo} key={index} id={index} checkComplete={toggleComplete}/>

// Chuyền tiếp (lần 3)
<ListItem2 todo={todo} key={index} id={index} checkComplete={toggleComplete}/>

// Chuyền nốt (lần 4)
<ListItem3 todo={todo} key={index} id={index} checkComplete={toggleComplete}/>
</>
```
:tired_face: Pha xử lý hết sức cồng kềnh và mệt mỏi phải không? Sẽ ra sao nếu như app của bạn có nhiều state và hàng trăm component? :cold_sweat:

Điều này sẽ khiến state trở nên khó hơn trong việc duy trì, less predictable và việc quản lý state trong app sẽ trở nên bừa bộn cũng như app trở nên vô cùng phức tạp. Đó là lý do tại sao chúng ta cần một state management tool như **Redux**.

#### **Cách Redux xử lý địa ngục chuyền prop này:**
Có thể gói gọn trong một hình ảnh luôn :no_mouth:

<img src="https://miro.medium.com/max/1024/1*XHMeHrC4tdwTSHWRalm_lQ.png" alt="Cách Redux đấm Prop Passing Hell" title="With and Without Redux" width="500" height="300" style="display: block; margin-left: auto; margin-right: auto;"/>

Ez hơn nhiều rồi!! Thay vì chuyền với thay đổi tứ tung loạn xạ thì ta nhét state vào một thứ gọi là store, khi có component nào thay đổi state thì chỉ việc update lại store. Cũng từ store ta cũng sẽ dễ dàng chuyền cho những component những state nó cần!

__TL;DR:__ Do yêu cầu cho các ứng dụng single-page sử dụng Javascript ngày càng trở lên phức tạp nên code của chúng ta  ngày càng có nhiều prop, method, state và component phải quản lý hơn nên có một công cụ để điều khiển là rất cần thiết

### 1.3. Terminology

Trước khi bắt tay vào sử dụng Redux chúng ta nên nắm được một số thuật ngữ mà nó sử dụng:
- __Reducer__: Nơi thực hiện các action để thay đổi state
- __Action__: Các hành động miêu tả một điều gì đó xảy ra trong ứng dụng (thêm, sửa, xóa,...) và là một object chứa type và payload
    - __Action Creator__: Các hàm tạo ra các action
    - __Action Type__: Tên gọi của action đó
    - __Action Payload__: Dữ liệu mà action mang theo (có thể có hoặc không)
- __Dispatch__: Việc kích hoạt các hành động
- __Store__: Nơi lưu trữ state của ứng dụng

Mình sẽ giải thích kỹ hơn nhưng khái niệm này ở những phần sau, còn hiện tại các bạn cứ hiểu nôm na nó là như vậy đã nhé :smile:

## 2. Redux Data Flow

### 2.1. Visualize Data Flow:
Khi bạn tìm hiểu trên mạng, rất nhiều khả năng là các bạn đã nhìn qua hình ảnh động này rồi:

<img src="https://images.viblo.asia/9848348f-9fa5-444e-9911-7d1137c35889.gif" alt="Unidirectional Data Flow With Redux In Swift." style="max-width: 450px; max-height: 300px; display: block; margin-left: auto; margin-right: auto; ">

Nó cùng phần nào khái quát được toàn bộ luồng dữ liệu trong redux rồi! Dưới đây sẽ là phần giải thích kỹ hơn về các thứ 

### 2.2. Break into details:

- Khởi tạo ban đầu:
    - Một `store` sẽ được tạo ra chứa **một** `root reducer` của toàn app
    - `store` sẽ gọi đến hàm `reducer` này một lần và lưu giá trị trả về thành `state` mới
    - Khi UI được render lần đầu, các component sẽ truy cập vào `state` hiện tại trong `store` để dùng các dữ liệu mà render
- Lúc có hoạt động:
    - Khi có gì xảy ra, như là khi người dùng bấm 1 nút
    - Một event được sinh ra và gọi đến `actionCreator`
    - Từ đó ta có được một `action` chứa tên và dữ liệu (`type` và `payload`)
    - Sau đó, `action` sẽ được gửi đi (`dispatch`) tới `store` để xử lý
    - `store` sẽ chạy lại hàm `reducer` với `state` cũ và `action` vừa được gửi tới rồi sau đó lưu và trả về giá trị `state` mới
    - `store` sẽ báo hiệu cho các bộ phận của UI rằng store đã được cập nhật
    - Các component đó sẽ kiểm tra những dữ liệu nó cần có thay đổi không. Nếu có thì sẽ re-render với dât mới, không thì thôi :)

## 3. State, Action, Reducer and Store

### 3.1. State
`state` của một __Redux__ app luôn được viết dưới dạng `object` và `array` thuần Javascript. Vậy tức là **không có** `class`, các type dựng sẵn như `Map`,`Date`, các `function` hoặc bất cứ cái gì ko phải JS thuần.
> Phần lớn ta sẽ thường thấy các state được viết thành một object chứa mảng các object. VD một state của 1 todo app:

```javascript
const todoState = {
  todos: [
    { id: 0, text: 'Learn React', complete: true },
    { id: 1, text: 'Learn Redux', complete: false},
    { id: 2, text: 'Build something', complete: false}
  ],
}
```
> Đoạn code trên chỉ là một ví dụ thôi, state là gì hoàn toàn phụ thuộc vào ứng dụng của bạn! `const todoState = 0` cũng có thể xảy ra mà!

### 3.2. Action

`action` là những object JS thuần chứa trường `type` và `payload` (optional). Như đã nói ở trên, bạn có thể tưởng tượng như `action` như một sự kiện miêu tả cái gì đó xảy ra trong ứng dụng.
> Ta có thể dễ dàng nghĩ ra các `action` thường dùng như là thêm, sửa, xóa, chọn, xóa tất, chọn tất,... Ví dụ:

```javascript
{ type: 'ADD_TODO', payload: { todo } }
{ type: 'UPDATE_TODO', payload: { todo } }
{ type: 'DELETE_TODO', payload: { todoID } }
{ type: 'DELETE_ALL_TODO', payload: {} }
{ type: 'TOGGLE_TODO', payload: { todoID } }
```
#### Action Type
`type` là trường chứa tên của action và phải là `string`.

`store` sẽ không chú ý đến nội dung bạn ghi gì trong trường `type`. 
> Điều này có nghĩa là nếu bạn ghi `type` là 'ABC' cho `action` thêm todo chẳng hạn, thì chương trình chạy ngon lành nếu trong reducer bạn cũng viết `action.type = 'ABC'`. (Phần reducer sẽ rõ hiểu hơn)

Nhưng viết như vậy sẽ rất khổ cho bạn và mọi người trong team khi debug. Cho nên hãy đặt tên cho `type` sao cho dễ đọc và miêu tả rõ được action làm gì nhé!
#### Action Payload
`payload` là trường chứa dữ liệu kèm theo của action. Trường `payload` là không bắt buộc nhưng chúng ta  nên viết vào cho nó quy củ. Nó có thể chứa `number`, `string` hoặc `object`

#### Action Creator
Chỉ đơn thuần là một hàm trả về object `action`

> Vậy tại sao lại phải viết thêm làm chi cho mệt? Sao không viết trực tiếp action luôn? Câu trả lời là để dễ dàng sử dụng lại đoạn code cho nhiều chỗ khác nhau, để code "clean" hơn và dễ dàng debug hơn rất nhiều. Thay vì viết một object với một đống ngoặc, chữ và các dấu phẩy ta chỉ cần viết một hàm là xong. Ví dụ:

```javascript
export function addTodo(todo) {
  return { type: 'ADD_TODO', payload: { todo }};
}
export function updateTodo(todo) {
  return { type: 'UPDATE_TODO', payload: { todo }};
}
export function deleteTodo(todoID) {
  return { type: 'DELETE_TODO', payload: { todoID }};
}
```

**Bonus**: Để code thêm "clean" ta có thể dùng `enum` để viết cho các `string` trong `type`. Điều này sẽ loại bỏ được những trường hợp viết sai chính tả. Rất hữu dụng khi viết reducer, debug và làm việc nhóm

```javascript
export enum ActionTypes {
  ADD_TODO = "ADD_TODO",
  UPDATE_TODO = "UPDATE_TODO",
  DELETE_TODO = "DELETE_TODO",
}

export function addTodo(todo) {
  return { type: ActionTypes.ADD_TODO, payload: { todo }};
}
export function updateTodo(todo) {
  return { type: ActionTypes.UPDATE_TODO, payload: { todo }};
}
export function deleteTodo(todoID) {
  return { type: ActionTypes.DELETE_TODO, payload: { todoID }};
}
```

### 3.3. Reducer
`reducer` là những hàm lấy `state` hiện tại và các `action` làm đối số, để trả về `state` mới. Đây là nơi các logic được thực hiện trong chương trình hay còn gọi là nơi các `action` được thực thi.
> Mọi reducer đều cần có `state` khởi tạo, đồng thời ta có thể viết luôn logic xử lý của action

```javascript
const initialState = [];

export default function reducer(state = initialState, action) {
  let newTodos = [...state];

  switch (action.type) {

    case ActionTypes.ADD_TODO:
      newTodos.push(action.payload.todo);
      return newTodos;

    case ActionTypes.UPDATE_TODO:
      const editedTodos = newTodos.map((todo) =>
        todo.id === action.payload.todo.id
          ? { ...todo, name: action.payload.todo.name }
          : todo
      );
      return editedTodos;

    case ActionTypes.DELETE_TODO:
      const deletedTodo = newTodos.filter(
        (todo) => todo.id !== action.payload.todoID
      );
      return deletedTodo;
    
    default:
      return state;
  }
}
```
#### Reducer Rules

Khi viết `reducer` ta nên tuân thủ theo một số quy luật sau

1. Các reducer chỉ có thể tính toán `state` mới dựa trên `state` cũ và `action` đưa tới
2. Chúng **không** được phép __trực tiếp__ thay đổi `state` hiện tại. Thay vào đó, phải làm một cập nhật bất biến bằng cách tạo copy của `state` hiện tại rồi mới thay đổi bản sao đó __(IMPORTANT!!!)__
3. Chúng **không** được thực hiện những logic không đồng bộ hay một số hiệu ứng phụ khác (save file, gọi request ajax http, sửa đối số, tạo ra những giá trị ngẫu nhiên như `Math.random()`)

Lý do nên tuân thủ:
1. Để không mất đi tính predictable. Khi output một hàm chỉ dựa trên các đối số input thì sẽ dễ dàng hiểu và test code hơn.
2. Tránh được việc UI không cập nhật các giá trị mới nhất, dễ dàng hơn trong việc debug và tạo test
3. Để tránh việc ứng dụng hoạt động không như ý muốn. Khi điều chỉnh đối số rất dễ xảy ra điều này

#### Root Reducer

Là hàm chịu trách nhiệm thực hiện tất cả `action` và tính toán toàn cục `state` và sẽ là thứ được chuyền đến cho `store`. Một ứng dụng **Redux** có thể được `split` thành nhiều `slice reducer` nhưng rồi cũng phải `combine` sao cho chỉ có một `root reducer`. 

#### Splitting and Combining
##### Split:

Trong hầu hết các dự án thực tế, một ứng dụng sẽ có rất nhiều chức năng. Vậy, chúng ta nên chia `reducer` thành nhiều các `reducer` nhỏ khác nhau dựa trên phần `state` mà chúng làm việc với.

Các `reducer` nhỏ này gọi là một `slice reducer`
> Ví dụ bài todo app có CRUD thông thường và filter thì reducer có thể được chia như sau:

```javascript
// CRUD Todo Reducer
const initialState = [];

export default function todoReducer(state = initialState, action) {
  let newTodos = [...state];

  switch (action.type) {

    case ActionTypes.ADD_TODO:
      newTodos.push(action.payload.todo);
      return newTodos;

    case ActionTypes.UPDATE_TODO:
      const editedTodos = newTodos.map((todo) =>
        todo.id === action.payload.todo.id
          ? { ...todo, name: action.payload.todo.name }
          : todo
      );
      return editedTodos;

    case ActionTypes.DELETE_TODO:
      const deletedTodo = newTodos.filter(
        (todo) => todo.id !== action.payload.todoID
      );
      return deletedTodo;
    
    default:
      return state;
  }
}
```
```javascript
// Filter Todo Reducer
const initialState = {
  status: 'All',
  colors: []
}

export default function filtersReducer(state = initialState, action) {
  switch (action.type) {
    case ActionTypes.FILTER_CHANGE: {
      return {...state, status: action.payload }
    }
    default:
      return state
  }
}
```
##### Combine:

Như đã nói ở trên, `store` sẽ chỉ chứa **một** `root reducer` của toàn app cho nên ta sẽ phải tập hợp (`combine`) các `slice reduce` lại thành một `root reducer`
> Ví dụ

```javascript
import todosReducer from './features/todos/todosSlice'
import filtersReducer from './features/filters/filtersSlice'

export default function rootReducer(state = {}, action) {
  return {
    todos: todosReducer(state.todos, action),
    filters: filtersReducer(state.filters, action)
  }
}
```
 **Bonus**: Thay vì dùng cách trên, ta có thể dùng hàm `combineReducer` có sẵn của Redux để tiết kiệm thời gian
 ```javascript
 import { combineReducers } from 'redux'

import todosReducer from './features/todos/todosSlice'
import filtersReducer from './features/filters/filtersSlice'

export const rootReducer = combineReducers({
  todos: todosReducer,
  filters: filtersReducer
})
 ```

### 3.4. Store
`store` là nơi tập hợp của `state`, `action` và `reducer` để làm những công việc sau:
- Chứa `state` hiện tại
- Có thể truy cập vào `state` qua `store.getState()`
- Có thể thay đổi `state` qua `store.dispatch(action)`
- Thêm và bỏ các hàm callback vào trong biến `listener` (một mảng) qua `store.subcribe(listener)` và hàm `unsubscribe` trong nó

#### Create Store

Để tạo một `store` rất đơn giản, chỉ cần dùng hàm `createStore` có sẵn của Redux rồi cho `root reducer` vào là xong rồi!!
```javascript
import { createStore } from 'redux'
import rootReducer from './reducer'

export const store = createStore(rootReducer)
```

Sau đó là bọc toàn bộ app trong component `<Provider>`, đây là component giúp tất cả các component trong app tiếp cận được `store`. Như này:

```jsx
import React from 'react';
import ReactDOM from 'react-dom';
import './index.css';
import App from './App';
import { store } from './store/store';
import { Provider } from 'react-redux';

ReactDOM.render(
  <React.StrictMode>
    <Provider store={store} >
      <App />
    </Provider>
  </React.StrictMode>,
  document.getElementById('root')
);
```
> Và thế là có store để dùng, không phải lo nghĩ gì nữa. Nhưng nếu bạn muốn tìm hiểu kĩ hơn về `createStore` thì đây nhé:

```javascript
function createStore(reducer, preloadedState) {
  let state = preloadedState
  const listeners = []

  function getState() {
    return state
  }

  function subscribe(listener) {
    listeners.push(listener)
    return function unsubscribe() {
      const index = listeners.indexOf(listener)
      listeners.splice(index, 1)
    }
  }

  function dispatch(action) {
    state = reducer(state, action)
    listeners.forEach(listener => listener())
  }

  dispatch({ type: '@@redux/INIT' })

  return { dispatch, subscribe, getState }
}
```

Đoạn code trên sẽ chỉ rõ được các công việc đã nêu ở trên của `store` được viết ở đâu và như thế nào

Ngoài ra, nó còn có một đối số nữa là `preloadedState`. Nó chỉ đơn thuần là nơi để ta có thể cho dữ liệu khởi tạo ban đầu vào `store`. Các dữ liệu có thể được gửi từ server hay trong `localStorage`.
> Ví dụ như là lấy dữ liệu của todo app từ `localStorage` rồi nhét nó vào `preloadedState`
```javascript
import { createStore } from 'redux'
import rootReducer from './reducer'

let preloadedState
const persistedTodosString = localStorage.getItem('todos')

if (persistedTodosString) {
  preloadedState = {
    todos: JSON.parse(persistedTodosString)
  }
}

const store = createStore(rootReducer, preloadedState)
```

## 4. Dispatching Action

Để có thể update `state` ta cần phải đưa `action` vào `store` rồi sau đó để `store` có thể đưa các `action` vào cho `reducer` nó phải dùng đến hàm `dispatch` trong `store` ví dụ như:
```javascript
import store from './store'
//...
store.dispatch(addTodo(todo))
```
Mỗi lần ta gọi đến `store.dispatch(action)`:
- `store` sẽ gọi đến `rootReducer` và sau đó là các `slice reducer`
- `store` lưu giá trị `state` mới bên trong
- `store` gọi đến các callback trong `listener` nếu có

Nhưng cách sử dụng `dispatch` như trên là không nên vì như thế code sẽ rất cồng kềnh và hơn nữa bạn không cần phải trực tiếp truy cập `store` như vậy vì đã có `<Provider>` rồi

Cách thay thế chính là sử dụng Hook `useDispatch` của `react-redux`
> Ví dụ một cách `dispatch`
```jsx
import React, { FormEvent, useState } from 'react'
import { useDispatch } from 'react-redux'
import { addTodo } from '../actions/todoActions';

export const AddTodoForm = (props) => {
    const { todos } = props
    const dispatch = useDispatch()
    const [newTodoName, setNewTodoName] = useState('');

    const handleSubmit = (e) => {
        e.preventDefault()
        const newTodo = {
            id: todos.length,
            name: newTodoName,
            complete: false,
            deadline: '123456'
        }
        dispatch(addTodo(newTodo));
        setNewTodoName("");
    }

    return (
        <form onSubmit={handleSubmit}>
            <input type="text" required value={newTodoName} onChange={e => setNewTodoName(e.target.value)} placeholder="Enter" />
            <button type="submit">Create</button>
        </form>
    )
}
```

## 5. Additional: Middleware and Redux Thunk
> Mình sẽ giới thiệu qua phần này thôi nhé :3
### 5.1. Middleware
Trong thực tế, nhiều trường hợp ta phải điều chỉnh cách hoạt động của `dispatch`. Để làm vậy **Redux** cho chúng ta một thứ gọi là `middleware`

`Middleware` cho phép ta kết nối tới một bên thứ 3 giữa lúc `dispatch` một `action` và lúc nó tới được `reducer`. Vậy nó thường được dùng trong những việc như logging, crash report, call API, routing,...

Sơ đồ hoạt động quen thuộc có thêm middleware nè:

<img src="https://topdev.vn/blog/wp-content/uploads/2019/05/redux-workflows.gif" style="max-width: 450px; max-height: 300px; display: block; margin-left: auto; margin-right: auto; background: white">

Để dùng `middleware` ta có thể sử dụng một enhancer có sẵn của Redux đó là `applyMiddleware`
> Ở ví dụ dưới mình sẽ dùng `applyMiddleware` để tích hợp `reduxThunk`
```javascript
import { createStore, applyMiddleware } from "redux";
import thunk from "redux-thunk";
import rootReducer from "../reducers/todo-reducer";

export let store = createStore(rootReducer, applyMiddleware(thunk));
```
### 5.2. Redux Thunk

Redux Thunk cho phép trả về các `action` là một `function` thay vì chỉ là một `object`, nó được đặt trước thời điểm reducer nhận request để nhận biết các `action` có trả về một `object` hay không, nếu đó là một object, Thunk sẽ chuyển action đó đến `reducer` như thường lệ, nếu `action` trả về là một `function`, Redux Thunk sẽ "chặn" `action` đó lại và đợi cho đến khi một lệnh asynchronous nào đó trong `function` hoàn tất và trả về kết quả
> Một ví dụ sử dụng `thunk` để callAPI
```javascript
export const addTodo = (todo) => {
  return async (dispatch) => {
    try {
      const res = await postTodoAPI(todo);
      dispatch(actionCreator.addTodo(res.data));
    } catch (err) {
      alert(err);
    }
  };
};
```
> Ở VD trên, trước khi dispatch addTodo thì chương trình phải đợi postTodoAPI thực hiện xong và trả về giá trị

---
Mình xin giới thiệu Redux đến đây thôi, nếu các bạn muốn tìm hiểu kỹ càng hơn thì bấm vào [đây](https://redux.js.org/tutorials/essentials/part-1-overview-concepts) nhé