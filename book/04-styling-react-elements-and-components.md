# Day 04: Styling React Elements and Components

Table of contents

1. [Adding Styling with Stylesheets](#1-adding-styling-with-stylesheets)
2. [Setting Class Name Dynamically](#2-setting-class-name-dynamically)
3. [Working with CSS Modules](#3-working-with-css-modules)
4. [CSS Modules and Media Queries](#4-css-modules-and-media-queries)

5. [Inline Styles](#5-inline-styles)
6. [Setting Styles Dynamically](#5-setting-styles-dynamically)
7. [Additional: Using Radium for Pseudo and Media Queries](#7-additional-using-radium-for-pseudo-and-media-queries)
8. [Additional: Using Styled Components and Dynamic Styles](#8-additional-using-styled-components-and-dynamic-styles)

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
import './Person.css';

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

## 3. Working with CSS Modules

## 4. CSS Modules and Media Queries

## 5. Inline Styles

Ngoài ra chúng ta có thể sử dụng inline style như sau:

```jsx
// App.js
// ...
const btnStyle = {
  backgroundColor: '#20232a',
  color: '#61dafb',
  padding: '8px 16px',
  border: 'none',
  borderRadius: '4px',
  cursor: 'pointer',
};
// ...
<button onClick={() => changePerson()} style={btnStyle}>
  Change person
</button>;
```

**Lưu ý** là style attribute nhận vào một Javascript object, các properties follow camelCased nha.

## 6. Setting Styles Dynamically

## 7. Additional: Using Radium for Pseudo and Media Queries

## 8. Additional: Using Styled Components and Dynamic Styles
