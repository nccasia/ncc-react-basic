# 01: Javascript Necessary Knowledge Base

Table of contents

- [01: Javascript Necessary Knowledge Base](#01-javascript-necessary-knowledge-base)
  - [1. `var`, `let` and `const`](#1-var-let-and-const)
    - [1.1 `var`](#11-var)
      - [Scope: globally scoped or function/locally scoped](#scope-globally-scoped-or-functionlocally-scoped)
      - [Var variables: re-declared and updated](#var-variables-re-declared-and-updated)
      - [Hoisting of var](#hoisting-of-var)
      - [`Var` weakness](#var-weakness)
    - [1.2 `let`](#12-let)
      - [Scope: block scoped](#scope-block-scoped)
      - [Let can be updated but not re-declared](#let-can-be-updated-but-not-re-declared)
      - [Hoisting of `let`](#hoisting-of-let)
    - [1.3 `Const`](#13-const)
      - [Hoisting of `const`](#hoisting-of-const)
  - [2. Arrow function](#2-arrow-function)
    - [`this` trong arrow function](#this-trong-arrow-function)
  - [3. Import and Export](#3-import-and-export)
    - [Named exports:](#named-exports)
    - [Default exports:](#default-exports)
  - [4. Classes, Properties and Methods](#4-classes-properties-and-methods)
  - [5. Spread operators and Rest parameters](#5-spread-operators-and-rest-parameters)
    - [Spread operators](#spread-operators)
    - [Rest parameters](#rest-parameters)
  - [6. Destructuring](#6-destructuring)
    - [Array destructuring](#array-destructuring)
    - [Object destructuring](#object-destructuring)
  - [7. Reference and Primitive types](#7-reference-and-primitive-types)
    - [Primitive type](#primitive-type)
    - [Reference types](#reference-types)
  - [8. Array functions](#8-array-functions)
    - [8.1. `map()`](#81-map)
    - [8.2. `forEach()`](#82-foreach)
    - [8.3. `filter()`](#83-filter)
    - [8.4. `some()`](#84-some)
  - [9. Error handling with try/catch](#9-xử-lý-lỗi-với-trycatch)
  - [10. Callback, Promise, Async and Await](#10-callback-promise-async-and-await)
    - [10.1. Callback](#101-callback)
    - [10.2. Promise](#102-promise)
    - [10.3. Async and Await](#103-async-and-await)
---

## 1. `var`, `let` and `const`

### 1.1 `var`

#### Scope: globally scoped or function/locally scoped

`var` là câu lệnh dùng để khai báo biến có phạm vi là **function scoped** hoặc **globally scoped**.  
Scope của biến là Global khi chúng ta khai báo biến bên ngoài function block. Tương tự, scope của biến là function scoped khi biến được khai báo bên trong block của function.

```javascript
var name = 'thaibm'; // globally scoped

function newFunction() {
  var age = '25'; // function scoped
}

console.log(name); // output: thaibm
console.log(age); // Uncaught ReferenceError: age is not defined
```

#### Var variables: re-declared and updated

Biến được khai báo bởi `var` có thể được tái khai báo và update.

```javascript
var name = 'thaibm';
var name = 'quytm'; // re-declared
```

và

```javascript
var name = 'thaibm';
name = 'quytm'; // update
```

#### Hoisting of var

Hoisting là cơ chế của JavaScript cho phép các khai báo biến hoặc hàm được dời lên trên đầu phạm vi của chúng trước khi thực thi đoạn code. Trong trường hợp này phạm vi của biến được khai báo bằng var là **function scoped** hoặc **globally scoped**.

```javascript
console.log(name);
var name = 'Do Dinh Van';
```

Tương đương với:

```javascript
var name;
console.log(name); // name is undefined
name = 'Do Dinh Van';
```

Biến được khai báo bởi `var` được dời lên đầu phạm vi và được khởi tạo với giá trị là `undefined`.

#### `Var` weakness

Scope và Re-declared là một trong những điểm yếu của `var`, nó sẽ nảy sinh vấn đề nếu như chúng ta ko biết hoặc ko nhớ biến đó đã từng được khai báo trước đó.
Chúng ta thử tham khảo ví dụ dưới đây:

```javascript
var name = 'Anh Pham Tien';
var age = 24;

if (age > 20) {
  var name = 'Thai Bui Minh';
}

console.log(name); // "Thai Bui Minh"
```

Khi `age > 20`, name bị redefined thành `"Thai Bui Minh"`. Sẽ không có vấn đề gì nếu bạn biết và muốn redefined lại biến `name`, tuy nhiên giả sử bạn không hề biết name đã được define trước đó, điều này hoàn toàn thực tế nếu như chúng ta làm việc trong một dự án thực tế, nơi có rất nhiều dòng code. Thế rồi có quá nhiều nơi có thể thay đổi một biến của bạn... so messy! T_T Thật khó để debug phải không nào.
Và thật may mắn chung ta có `let` và `const`.

### 1.2 `let`

#### Scope: block scoped

Biến được khai báo bởi `let` có phạm vi trong block. Block là một đoạn code đc giới hạn bởi hai dấu ngoặc nhọn `{}` (curly braces).

```javascript
if (true) {
  let name = 'tutv';
  console.log(name); // output: tutv
}

console.log(name); // Uncaught ReferenceError: name is not defined
```

#### Let can be updated but not re-declared

Biến khai báo bới let có thể được update nhưng không thể được khái báo lại.

```javascript
let name = 'tutv';
name = 'thanhnv';
```

```javascript
let name = 'tutv';
let name = 'thanhnv'; // Uncaught SyntaxError: Identifier 'name' has already been declared
```

#### Hoisting of `let`

Biến được khai báo bằng `let` cũng được hoisted lên đầu phạm vi (Ở đây là block nha). Tuy nhiên không giống như `var` giá trị biến là `undefined`, khi dùng `let` biết đó sẽ là chưa được khởi tạo. Bởi vậy, khi bạn sử dụng một biến trước khi được khai báo bằng `let`, lỗi `Reference Error` thôi. =)))

### 1.3 `Const`

`const` giống `let` về scope tuy nhiên, biến được khai báo bởi `const` thì không thể được update, gán giá trị khác.

```javascript
const name = 'thaibm';
name = 'thanhnv'; // Uncaught TypeError: Assignment to constant variable.
```

_Một số lưu ý_  
Đối với biến có type là object hoặc array, chúng ta không thể gán lại giá trị cho biến nhưng có thể thay đổi giá trị của properties trong object hoặc thay đổi giá trị của phần tử trong mảng:

```javascript
const person = {
  name: 'thaibm',
  age: 25,
};

person.name = 'quytm'; // Thay đổi đc luôn, chẳng có lỗi gì cả =))
person = { name: 'minhnt', age: 25 }; // Uncaught TypeError: Assignment to constant variable.
```

```javascript
const array = ['thaibm', 'quytm'];

array[0] = 'thanhnv'; // ok, cũng chả lỗi luôn :v
array = ['minhnt', 'tutv']; // Uncaught TypeError: Assignment to constant variable.
```

#### Hoisting of `const`

Biến được khai báo bằng `const` cũng được đưa lên đầu phạm vi. Tuy nhiên khi khai báo biến với `const` chúng ta phải khởi tạo giá trị ban đầu cho biến đó.

## 2. Arrow function

Arrow function cho phép chúng ta khai báo function ngắn gọn hơn so với cách truyền thống:

```javascript
const hello = () => {
  return 'Hello';
};
// the same as
const helloShort = () => 'Hello';
```

Arrow function có params

```javascript
const hello = (name) => 'Hello ' + name;
// the same as
const helloShort = (name) => 'Hello ' + name;
```

Arrow function return an object

```javascript
const person = () => ({ name: 'thaibm', age: 17 }); // return an object
```

### `this` trong arrow function

`this` trong javascript là một cái gì đó khá lằng nhằng, lúc thế này lúc thế kia, trở mặt nhanh hơn tốc độ của người yêu cũ. Mình xin nhắc lại một vài ví dụ về `this` trong function truyền thống (ES5).

Nếu `this` nằm bên trong object's method:

```javascript
var person = {
  name: 'thaibm',
  showName: function () {
    console.log(this.name);
  },
};

person.showName(); // thaibm
```

Function `showName` thuộc object person, lúc này this sẽ refer đến object person. Ok mọi thứ vẫn bình thường, chưa có ny cũ nào trở mặt ở đây cả.

Bây giờ, giả sử `this` nằm bên trong function của method hoặc là callback. (Method là function của object)

```javascript
window.name = "window's name";

var person = {
  name: 'thaibm',
  tasks: ['eat', 'sleep', 'code'],
  showTasks: function () {
    this.tasks.forEach(function (task) {
      console.log(this.name + ' wants to ' + task);
    });
  },
};

person.showTasks();
```

Và kết quả

```
window's name wants to eat
window's name wants to sleep
window's name wants to code
```

What the hợi??? Thực ra, `this` sẽ refer đến **_the owner of the function it is in_**, tuy nhiên trong trường hợp này function của chúng ta lại thuộc về window/global object.  
Khi chúng ta call `this` bên trong function không thuộc object nào cả hoặc function bên trong một method, khi này this sẽ thuộc về window/global object.

```javascript
var standAloneFunc = function () {
  console.log(this);
};

standAloneFunc(); // [object Window]
```

Để khắc phục vấn đề trên, với ES5 chúng ta có 2 cách:

1. Tạo một variable bên ngoài function để lưu lại `this`

```javascript
var person = {
  name: 'thaibm',
  tasks: ['eat', 'sleep', 'code'],
  showTasks: function () {
    var _this = this;
    this.tasks.forEach(function (task) {
      console.log(_this.name + ' wants to ' + task);
    });
  },
};

person.showTasks();
// thaibm wants to eat
// thaibm wants to sleep
// thaibm wants to code
```

2. Sử dụng bind()

```javascript
var person = {
  name: 'thaibm',
  tasks: ['eat', 'sleep', 'code'],
  showTasks: function () {
    this.tasks.forEach(
      function (task) {
        console.log(this.name + ' wants to ' + task);
      }.bind(this)
    );
  },
};

person.showTasks();
// thaibm wants to eat
// thaibm wants to sleep
// thaibm wants to code
```

Tuy nhiên, với ES6 Arrow function, vấn đề trên sẽ bay màu luôn và ngay:

```javascript
var person = {
  name: 'thaibm',
  tasks: ['eat', 'sleep', 'code'],
  showTasks: function () {
    this.tasks.forEach((task) => {
      console.log(this.name + ' wants to ' + task);
    });
  },
};

person.showTasks();
// thaibm wants to eat
// thaibm wants to sleep
// thaibm wants to code
```

Trên [MDN web docs](https://developer.mozilla.org/vi/docs/Web/JavaScript/Reference/Functions/Arrow_functions) có định nghĩa `this` trong arrow function như sau:

> Arrow functions establish "this" based on the scope the Arrow function is defined within.

Có thể hiểu nôm na là giá trị của `this` sẽ phụ thuộc vào scope ở nơi mà arrow function đó được định nghĩa.

## 3. Import and Export

Javascript code của chúng ta có thể được chia thành nhiều file, với `export` và `import` chúng ta có thể khai báo những variables, function hoặc class rồi truy cập và sử dụng ở những file khác nhau.

Có 2 loại `export`:

1. Named Exports (Zero or more exports per module)
2. Default Exports (One per module)

### Named exports:

```javascript
// utility.js
// export individual features (can export var, let, const, function, class)
export const myVariable = Math.sqrt(2);
export function myFunction() { ... };
```

Sau đó chúng ta có thể import ở một nơi nào đó:

```javascript
import { myVariable } from './utility.js';
import { myFunction } from './utility.js';
// or import with alias
import { myVariable as variable } from './utility.js';
// or import the whole file
import * as util from './utility.js';
// Usage
util.myFunction();
```

### Default exports:

```javascript
// person.js
// export individual features (can export var, let, const, function, class)
const person = { name: 'thaibm' };
export default person;
```

```javascript
// myFunction.js
export default function () {}
```

```javascript
// myClass.js
export default class {}
```

Đối với default export chúng ta sẽ import như sau:

```javascript
import person from './person.js';
// or we can rename like this
import prs from './person.js';
```

```javascript
import myFunction from './myFunction.js';
```

```javascript
import MyClass from './myClass.js';
```

_Note_ Mình sẽ đi nhanh qua các khái niệm này cơ bản nhất có thể. Các bạn có thể xem và tham khảo thêm các cú pháp trên [MDN web docs](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/export) nha!

## 4. Classes, Properties and Methods

Khai báo và sử dụng `class` (ES6)

```javascript
class Person {
  constructor() {
    // declare a Property
    this.name = 'thaibm';
  }

  // declare a Method
  call() {
    console.log(this.name);
  }
}

// Usage
const person = new Person();
person.call(); // thaibm
console.log(person.name); // thaibm
```

Kế thừa (inheritance)

```javascript
class Human {
  constructor() {
    this.gender = 'male';
  }

  printGender() {
    console.log(this.gender);
  }
}

class Person extends Human {
  constructor() {
    super(); // Nhớ call super nha
    this.name = 'thaibm';
  }

  call() {
    console.log(this.name);
  }
}

// Usage
const person = new Person();
person.call(); // thaibm
person.printGender(); // male
```

Với ES7 trở đi chúng ta có thể khai báo class như sau:

```javascript
class Human {
  gender = 'male';

  printGender = () => {
    console.log(this.gender);
  };
}
```

Và đây cũng là cách sử dụng bạn sẽ gặp nhiều khi làm việc với React.

## 5. Spread operators and Rest parameters

### Spread operators

Spread operators có cú pháp là `...` được sử dụng để split up array elements hoặc object properties. (Có thể dịch nôm na cho dễ hiểu là cắt nhỏ, chuyển đổi array thành nhiều phần tử, object thành nhiều properties)  
Ví dụ với array:

```javascript
const array1 = [1, 2, 3];
// Concatenate array
const array2 = [...array1, 4, 5, 6]; // [1, 2, 3, 4, 5, 6]

// Copy array
const array3 = [...array1]; // [1, 2, 3]
```

Đối với object

```javascript
const obj1 = { name: 'thaibm', age: 17 };
// Copy object
const obj2 = { ...obj1 }; // {name: "thaibm", age: 17}

// Merge object
const obj3 = { ...obj1, gender: 'male' }; // {name: "thaibm", age: 17, gender: "male"}

// Or you can merege object like this:
const obj4 = { gender: 'male' };
const obj = { ...obj1, ...obj4 };
```

### Rest parameters

Rest parameters là khi chúng ta sử dụng `...` để biểu diễn một số lượng vô hạn arguments dưới dạng một mảng.  
Ví dụ khi ta viết function tính tổng nhiều số

```javascript
const sum = (...args) => {
  // args is an array here
  return args.reduce((previous, current) => {
    return previous + current;
  });
};

console.log(sum(1, 2, 3, 4)); // output: 10

// or
const numbers = [1, 2, 3, 4];
console.log(sum(...numbers)); // output: 10
```

Bạn có thể tìm hiểu thêm về `reduce()` ở [đây](https://developer.mozilla.org/vi/docs/Web/JavaScript/Reference/Global_Objects/Array/Reduce).

## 6. Destructuring

Destructuring cho phép chúng ta extract array elements hoặc object properties rồi lưu vào biến.

### Array destructuring

```javascript
const [a, b] = ['thaibm', 'tutv'];
console.log(a); // thaibm
console.log(b); // tutv
```

### Object destructuring

```javascript
const { name } = { name: 'thaibm', age: 18 };
console.log(name); // thaibm
console.log(age); // undefined
```

Khi làm việc với React chúng ta sẽ rất hay sử dụng cái này đó nha. :D

## 7. Reference and Primitive types

### Primitive type

Ví dụ mình có đoạn code sau:

```javascript
const number1 = 1;
const number2 = number1;

console.log(number2); // 1
```

Lúc này, `number2` sẽ đc _copy giá trị_ từ `number1` sang. Trong javascript những type như `number`, `string`, `boolean` là Primitive type.

### Reference types

Với Object hoặc Array thì lại không như vậy:

```javascript
const person1 = { name: 'thaibm', age: 18 };
const person2 = person1;

console.log(person2); // { name: 'thaibm', age: 18 };
```

Chưa có gì khác thường phải không nào. Tuy nhiên khi chúng ta thay đổi giá trị property của `person1`:

```javascript
person1.name = 'quytm';
console.log(person2); // { name: 'quytm', age: 18 };
```

Thay đổi thằng person1 nhưng person2 cũng đổi theo, vậy là sao? Trong javascript, Object và Array là reference types có nghĩa là khi bạn khai báo một object/array, nó sẽ được cấp 1 định danh (`person1`, `person2`) tham chiếu đến giá trị được lưu trong bộ nhớ.  
Ở ví dụ bên trên, `person1` và `person2` đều tham chiếu đến một giá trị trong bộ nhớ, vậy nên khi thay đổi giá trị đó thì ta có kết quả như vậy.

Để giải quyết vấn đề này chúng ta sẽ phải copy object. Có thể sử dụng Spread Operators hoặc cặp đôi JSON.stringify() và JSON.parse()

```javascript
const obj1 = { name: 'thaibm' };

// Shallow copies
const obj2 = { ...obj1 };

// Deep clone https://developer.mozilla.org/en-US/docs/Glossary/Deep_copy
const obj3 = JSON.parse(JSON.stringify(obj1));
const clone = structuredClone(original);
```
Hoặc sử dụng thư viện lodash: [cloneDeep](https://lodash.com/docs/#cloneDeep)
Hoặc sử dụng đệ quy function.

**_Chú ý_** 
Spread syntax, Array.prototype.concat(), Array.prototype.slice(), Array.from(), Object.assign(), and Object.create()) do not create deep copies (instead, they create shallow copies.

## 8. Array functions

Mình xin giới thiệu một số array functions sẽ được sử dụng khá thường xuyên khi làm việc với React.

### 8.1. `map()`

`map()` giúp tạo ra một mảng mới với các phần tử là kết quả từ việc thực thi một hàm lên từng phần tử của mảng được gọi. Lấy ví dụ cho dễ hình dung nha:

```javascript
const array1 = [1, 4, 9, 16];
// pass a function to map
const map1 = array1.map((num) => {
  return num * 2;
});
console.log(map1);
// expected output: Array [2, 8, 18, 32]
```

Ở đây chúng ta có thể thấy map được truyền vào một arrow function, trả về giá trị được nhân 2. Và ta có mảng mới như trên.

### 8.2. `forEach()`

`forEach()` sẽ thực thi một hàm khi duyệt qua từng phần tử của mảng.

```javascript
const array1 = ['a', 'b', 'c'];

array1.forEach((element) => console.log(element));

// expected output: "a"
// expected output: "b"
// expected output: "c"
```

### 8.3. `filter()`

`filter() `dùng để tạo một mảng mới với tất cả các phần tử thỏa điều kiện của một hàm test.

```javascript
const numbers = [1, 2, 3, 4, 5, 6];

const result = numbers.filter((num) => num > 3);

console.log(result);
// expected output: Array [4, 5, 6]
```

### 8.4. `some()`

`some()` kiểm tra xem có ít nhất một phần tử của mảng thoả điều kiện ở hàm được truyền vào hay không. Kết quả trả về có kiểu `Boolean`.

```javascript
const array = [1, 2, 3, 4, 5];

// checks whether an element is even
const even = (element) => element % 2 === 0;

console.log(array.some(even));
// expected output: true
```
Mình xin giới thiệu đến đây thôi, còn những function khác như `every()`, `find()`, `includes()`,... các bạn tìm hiểu thêm ở [đây](https://developer.mozilla.org/vi/docs/Web/JavaScript/Reference/Global_Objects/Array) nha.

## 9. Xử lý lỗi với "try...catch"

Dù chúng ta có cẩn thận thế nào, code của chúng ta vẫn sẽ có lỗi. Chúng xuất hiện có thể là bởi một input không lường trước từ người dùng, một thông báo lỗi từ server, hoặc từ một ngàn lẻ một lý do khác.

Thông thường, một chương trình "chết" (ngay lập tức dừng lại) trong trường hợp có lỗi, và in nó ra trong console.

Nhưng chúng ta có thể dùng cấu trúc `try...catch` để bắt lỗi, nhờ vậy chương trình của chúng ta có thể tiếp tục chạy và làm một điều gì đó hữu ích hơn là "chết" ngay như vậy.

Cấu trúc `try...catch` có 2 phần chính: `try`, và sau đó `catch`

```js
try {
  // code...
} catch (err) {
  // error handling
}
```

Nó hoạt động như sau:
1. Đầu tiên, câu lệnh trong `try {...}` được thực thi.
2. Nếu không có lỗi, `catch (err)` bị bỏ qua: câu lệnh chạy hết trong `try` và tiếp tục, bỏ qua câu lệnh trong `catch`.
3. Nếu có lỗi xảy ra thì việc thực thi câu lệnh trong `try` bị dùng lại ngay lập tức, và câu lệnh trong `catch(err)` được thực thi. Biến `err` (chúng ta có thể dùng bất kỳ tên nào cho nó) sẽ chứa một object chứa các thông tin về lỗi đã xảy ra.

Vì vậy, một lỗi xảy ra trong `try {...}` sẽ không dừng chương trình lại và chúng ta có cơ hội để xử lý nó trong `catch`.

Ví dụ không có lỗi: sẽ hiện `alert` `(1)` và `(2)`:

```js 
try {
  alert('Start of try runs');  // *!*(1) <--*/!*
  // ...không có lỗi ở đây
  alert('End of try runs');   // *!*(2) <--*/!*
} catch (err) {
  alert('Catch is ignored, because there are no errors'); // (3)
}
```
Ví dụ có lỗi: sẽ hiện `alert` `(1)` và `(3)`:

```js
try {
  alert('Start of try runs');  // *!*(1) <--*/!*
  *!*
  lalala; // lỗi, biến chưa khai báo!
  */!*
  alert('End of try (never reached)');  // (2)
} catch (err) {
  alert(`Error has occurred!`); // *!*(3) <--*/!*
}
```

## 10. Callback, Promise, Async and Await

### 10.1. Callback

Một callback function là function được gọi bởi function khác. Thông thường, function A (callback function) sẽ được truyền vào function B như một parameter,
function B sẽ gọi function A để thực hiện một chức năng nào đó.

```javascript
function A() {
  // ...........
}

function B(callbackFn) {
  // ..........
  callbackFn();
}

B(A);
```

Được sử dụng để xử lý bất đồng bộ hoặc xử lý sự kiện

```javascript
document.getElementById("demo").addEventListener("click", () => {
  alert('da click');
})
```

```javascript
function getRequest(url, callback) {
  $.ajax({
    type: "GET",
    url: url,
    success: function (msg) {
      // do something on success
      callback(msg);
    },
    error: function (msg) {
      // do something on error
    },
  });
}
```

```javascript
function alertFunc() {
  alert("Hello!");
}

setTimeout(alertFunc, 3000);
```

```javascript
const [title, setTitle] = useState('title');

//useEffect
useEffect(() => {
  setTitle('changed title');
});
```

### 10.2. Promise

Khi sử dụng callback, dễ gây ra Callback Hell (quá nhiều callbacks lồng nhau, đoạn code nhìn như hình mũi tên), gây mất kiểm soát trong quá trình code, thiếu bộ nhớ, ...\
=> ES6 giới thiệu **_Promise_** như một giải pháp thay thế để xử lý bất đồng bộ một cách hiệu quả hơn.

Các trạng thái của **_Promise_**:

- **_pending_**: trạng thái khởi tạo, chưa fullfilled hoặc rejected.
- **_fulfilled_**: trạng thái thực thi thành công.
- **_rejected_**: trạng thái thực thi thất bại.

Khởi tạo một **_Promise_**:

```javascript
var promise = new Promise((resolve, reject) => {});
```

`resolve` là một hàm callback xử lý cho hành động thành công.\
`reject` là một hàm callback xử lý cho hành động thất bại.

Thenable trong **_Promise_** => Nhận các giá trị kết quả (thành công hoặc thất bại) được trả về khi `resolve` hoặc `reject`.

```javascript
var promise = new Promise((resolve, reject) => {
  resolve("OK");
});

promise.then(
  (successParams) => {
    console.log("run resolve")
    console.log(successParams);
  },
  (failParams) => {
    console.log("run reject")
    console.log(failParams);
  }
);
```

```javascript
var promise = new Promise((resolve, reject) => {
  reject("wrong")
});

promise.then(
  (successParams) => {
    console.log("run resolve")
    console.log(successParams);
  },
  (failParams) => {
    console.log("run reject")
    console.log(failParams);
  }
);
```

Có thể sử dụng `catch` để bắt lỗi (nên sử dụng cách này cho rõ ràng)\
**Chú ý:** khi có `reject`, bắt buộc phải có `then` hoặc `catch`

```javascript
var promise = new Promise(function (resolve, reject) {
  reject("something wrong");
});

promise
  .then((successParams) => {
    console.log("yes OK");
    console.log(successParams);
  })
  .catch((errorParams) => {
    console.log("wrong");
    console.log(errorParams);
  });
```

Consucutive then (kết quả của then hiện tại sẽ quyết định trạng thái của then kế tiếp)

```javascript
var promise = new Promise((resolve, reject) => {
  resolve();
});

promise
  .then(() => {
    return new Promise((resolve, reject) => {
      reject();
    });
  })
  .then(() => {
    console.log("Success!");
  })
  .catch(() => {
    console.log("Error!");
  });
```

**Chú ý:** callback function của **_Promise_** sẽ được chạy ngay lập tức khi **_Promise_** được khởi tạo (xem ví dụ cuối mục 9)

### 10.3. Async and Await

Trong ES6, ta sử dụng **_Promise_** (.then()) để thay thế cho callback cũ. Tuy nhiên, vấn đề cố hữu của callback vẫn chưa được giải quyết triệt để (vẫn khá khó để đọc/mở rộng/bảo trì code do có thể có rất nhiều then).\
=> ES7 giới thiệu **_Async/Await_** để xử lý các tác vụ bất đồng bộ. **_Async/Await_** được xây dựng dựa trên **_Promise_** nên hoàn toàn tương thích với **_Promise_**

**_Async_** (khởi tạo một function bất đồng bộ)

- Tự động chuyển một function bình thường thành một **_Promise_**
- Cho phép sử dụng **_Await_**

**_Await_** (dừng việc thực thi của một async function cho đến khi **_Promise_** được thực thi (resolve/reject))

- Khi được đặt trước một **_Promise_**, đợi cho đến khi **_Promise_** kết thúc, trả về kết quả
- Chỉ hoạt động với **_Promise_**, không hoạt động với callback
- Chỉ có thể được sử dụng trong async functions

```javascript
var promise = new Promise((resolve, reject) => {
    setTimeout(() => {
      resolve("done 300ms")
    }, 300);
  })

var run = () => { //thêm async để chạy
  var result = await promise; //error
  console.log(result);
}

run()
```

```javascript
fetch("api1")
  .then((response) => {
    return fetch("api2");
  })
  .then((params) => {
    return fetch("api3");
  })
  .then((response) => {
    console.log("Success!");
  });
//tương đương với

async function getData() {
  var res1 = await fetch("api1");
  var res2 = await fetch("api2");
  var res3 = await fetch("api3");
}

getData();
```

Kết hợp cùng try/catch để bắt lỗi

```javascript

const fetch = () => {
  return new Promise((resolve, reject) => {
    reject(new Error('day la loi'))
  })
}

async function getData() {
  try {
    const response = await fetch() 
    console.log('response: ', response)
  } catch (error) {
    console.log("error: ", error)
  }
}

getData();
```

Lỗi thường gặp khi sử dụng **_Promise_** - **_Async/Await_**

```javascript
const logTime = () => {
  console.log(new Date());
};

const getPromise = (message) => {
  return new Promise((resolve, reject) => {
    setTimeout(() => {
      console.log(message);
      logTime();
      resolve();
    }, 2000);
  });
};

const function1 = async () => {
  logTime();

  var promise1 = getPromise("promise1 done");

  var promise2 = getPromise("promise2 done");

  await promise1;
  await promise2;
};

const function2 = async () => {
  logTime();

  await getPromise("promise1 done");

  await getPromise("promise2 done");
};

const function3 = async () => {
  logTime();

  var promise1 = getPromise("promise1 done");

  var promise2 = getPromise("promise2 done");

  await Promise.all([promise1, promise2]);
};

//chạy từng function 1, 2, 3 để so sánh kết quả

kết quả của function 3 (2 promises resolve cùng thời điểm)
2022-12-01T12:49:19.915Z

=========
promise1 done
2022-12-01T12:49:21.974Z
=========
promise2 done
2022-12-01T12:49:21.975Z
=========

kết quả của function 2
2022-12-01T12:50:49.749Z

=========
promise1 done
2022-12-01T12:50:51.757Z
=========
promise2 done
2022-12-01T12:50:53.758Z
=========

kết quả của function 1 (kết quả tương đồng với function 3)
2022-12-01T12:53:35.016Z

=========
promise1 done
2022-12-01T12:53:37.075Z
=========
promise2 done
2022-12-01T12:53:37.075Z
=========
```

Nhận xét:
- Kết quả của function 2: không có nhiều điều để nói, theo đúng quy tắc async/await (promise 2 resolves sau promise 1 một khoảng thời gian là 2s)
- Kết quả của function 3: 2 promises resolve cùng thời điểm do sử dụng Promise.all (đọc thêm tại https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Promise/all)
- Kết quả của function 1: nhìn sơ qua thì expect sẽ giống với function 2 (promise 2 resolves sau promise 1 một khoảng thời gian là 2s), nhưng thực chất thì kết quả lại giống function 3 (resolve cùng lúc), lý do là vì callback function trong Promise sẽ được execute ngay lập tức sau khi Promise được khởi tạo => 2 function setTimeout của 2 Promises chạy cùng lúc và sau thì 2 callback của 2 function setTimeout được chạy.