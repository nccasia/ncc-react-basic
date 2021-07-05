# 03: Conditional and List Rendering

Table of contents

- [03: Conditional and List Rendering](#03-conditional-and-list-rendering)
  - [1. Conditional Rendering](#1-conditional-rendering)
    - [1. Sử dụng toán tử `&&`:](#1-sử-dụng-toán-tử-)
    - [2. Sử dụng Conditional (ternary) operator: `condition ? exprIfTrue : exprIfFalse`](#2-sử-dụng-conditional-ternary-operator-condition--expriftrue--expriffalse)
    - [3. Sử dụng `if - else` thông thường](#3-sử-dụng-if---else-thông-thường)
  - [2. List Rendering](#2-list-rendering)
    - [2.1. List rendering basic syntax](#21-list-rendering-basic-syntax)
    - [2.2. List rendering advanced](#22-list-rendering-advanced)
  - [Summary](#summary)
  - [Time to Practice](#time-to-practice)

---

## 1. Conditional Rendering

Khác với Angular hay VueJS cung cấp cho chúng ta các directive như \*ngIf hay v-if, React không có những directive như vậy và chúng ta sẽ sử dụng js conditional (if else).

> Everything is Javascript!

Preparation (Lưu ý mình lấy ví dụ với functional component nha, với classed-base component sẽ có một vài khác biêt các bạn thử thực hành cho quen tay nhé hihe):

```jsx
// App.js
// Add showPerson and togglePerson function
const [showPerson, setShowPerson] = useState(false);

const togglePerson = () => {
  setShowPerson(!showPerson);
};
```

Có cách để sử dụng như sau:

### 1. Sử dụng toán tử `&&`:

```jsx
// App.js
return (
  <div className='App'>
    <h1>React Sample App</h1>
    <button onClick={togglePerson} style={btnStyle}>
      Toggle Person
    </button>
    {showPerson && (
      <Person name={person.name} click={changePerson} change={changeNameHandle}>
        {statement}
      </Person>
    )}
  </div>
);
```

Trong Javascript, khi thực thi condition đầu tiên nếu false thì sẽ không thực hiện condition tiếp theo.

### 2. Sử dụng Conditional (ternary) operator: `condition ? exprIfTrue : exprIfFalse`

```jsx
{
  showPerson ? (
    <Person name={person.name} click={changePerson} change={changeNameHandle}>
      {statement}
    </Person>
  ) : null;
}
```

### 3. Sử dụng `if - else` thông thường

```jsx
let personElement = null;

if (showPerson) {
  personElement = (
    <Person name={person.name} click={changePerson} change={changeNameHandle}>
      {statement}
    </Person>
  );
}

return (
  <div className='App'>
    <h1>React Sample App</h1>
    <button onClick={togglePerson} style={btnStyle}>
      Toggle Person
    </button>
    {personElement}
  </div>
);
```

Hoặc

```jsx
function Greeting(props) {
  const isLoggedIn = props.isLoggedIn;
  if (isLoggedIn) {
    return <UserGreeting />;
  }
  return <GuestGreeting />;
}
```

Với 3 cách trên, tùy vào từng ngữ cạnh cụ thể các bạn hãy chọn ra các thức phù hợp nha.

## 2. List Rendering

### 2.1. List rendering basic syntax

Để render một list chúng ta sẽ sử dụng `Array.map()` function. Chi tiết về map() các bạn xem lại trong day-01 nha.
Mình sẽ lấy một ví dụ ngắn, đơn giản như sau

```jsx
const numbers = [1, 2, 3, 4, 5];

return (
  <ul>
    {numbers.map((number, index) => (
      <li key={index}>{number}</li>
    ))}
  </ul>
);
```

Keys giúp React xác định được item nào được add, changed hoặc removed. Chú ý là mỗi key trong một vòng lặp phải khác nhau và nếu thiếu key console sẽ báo lỗi đó nha.

> Key must be unique

### 2.2. List rendering advanced

Giờ chúng ta quay trở lại với ví dụ về person ban đầu. Khởi tạo với một mảng persons:

```jsx
// App.js
const [persons, setPersons] = useState([
  { id: 1, name: 'Iron man' },
  { id: 2, name: 'Doctor Strange' },
  { id: 3, name: 'Captain America' },
]);
```

và return:

```jsx
return (
  <div className='App'>
    <h1>React Sample App</h1>
    <button onClick={togglePerson} style={btnStyle}>
      Toggle Person
    </button>
    {showPerson &&
      persons.map((person) => (
        <Person
          key={person.id}
          name={person.name}
          click={changePerson}
          change={changeNameHandle}
        >
          {statement}
        </Person>
      ))}
  </div>
);
```

Lúc này, `changePerson()` và `changeNameHandle()` chưa hoạt động, chúng ta cần update hai function này. Để thay đổi name của một object person trong một mảng chúng ta sẽ cần truyền vào id của person đó khi gọi hàm:

```jsx
<Person
  key={person.id}
  name={person.name}
  click={() => deletePerson(person.id)}
  change={(event) => changeNameHandle(event, person.id)}
>
  {statement}
</Person>
```

Cuối cùng là update các function:

```jsx
const deletePerson = (id) => {
  const personIndex = persons.findIndex((p) => p.id === id);
  const newPersons = [...persons]; // Copy object, try to avoid reference and mutate it directly
  newPersons.splice(personIndex, 1); // Delete array element
  setPersons(newPersons);
};

const changeNameHandle = (event, id) => {
  const personIndex = persons.findIndex((p) => p.id === id);
  const person = { ...persons[personIndex] }; // Copy object, try to avoid reference and mutate it directly
  person.name = event.target.value;
  const newPersons = [...persons];
  newPersons[personIndex] = person;
  setPersons(newPersons);
};
```

Vì chúng ta không được change state một cách trực tiếp nên sẽ phải đi lòng vòng một chút nha.

Dưới đây là toàn bộ file App.js, tham khảo nếu bạn gặp lỗi nha:

```jsx
import React, { useState } from 'react';
import './App.css';
import Person from './components/person/Person';

const App = () => {
  // State Declaration
  const [persons, setPersons] = useState([
    { id: 1, name: 'Iron man' },
    { id: 2, name: 'Doctor Strange' },
    { id: 3, name: 'Captain America' },
  ]);

  const [showPerson, setShowPerson] = useState(false);
  const [statement] = useState('Love you 3000!');

  const deletePerson = (id) => {
    const personIndex = persons.findIndex((p) => p.id === id);
    const newPersons = [...persons]; // Copy object, try to avoid reference and mutate it directly
    newPersons.splice(personIndex, 1);
    setPersons(newPersons);
  };

  const changeNameHandle = (event, id) => {
    const personIndex = persons.findIndex((p) => p.id === id);
    const person = { ...persons[personIndex] }; // Copy object, try to avoid reference and mutate it directly
    person.name = event.target.value;
    const newPersons = [...persons];
    newPersons[personIndex] = person;
    setPersons(newPersons);
  };

  const togglePerson = () => {
    setShowPerson(!showPerson);
  };

  const btnStyle = {
    backgroundColor: '#20232a',
    color: '#61dafb',
    padding: '8px 16px',
    border: 'none',
    borderRadius: '4px',
    cursor: 'pointer',
  };

  return (
    <div className='App'>
      <h1>React Sample App</h1>
      <button onClick={togglePerson} style={btnStyle}>
        Toggle Person
      </button>
      {showPerson &&
        persons.map((person) => (
          <Person
            key={person.id}
            name={person.name}
            click={() => deletePerson(person.id)}
            change={(event) => changeNameHandle(event, person.id)}
          >
            {statement}
          </Person>
        ))}
    </div>
  );
};

export default App;
```

## Summary

Tổng kết day-02 có những topic sau quan trọng cần ghi nhớ nha:

1. [Conditional Rendering](#1-conditional-rendering)
2. [List Rendering](#2-list-rendering)

## Time to Practice

Các bạn hãy cố gắng tự thực hành những kiến thức trên để quen tay và hiểu hơn nha!
