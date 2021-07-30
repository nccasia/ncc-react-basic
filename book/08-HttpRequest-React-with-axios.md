# 08:Http request in React (bonus: using Axios)

## Table of contents

- [08: Http request in React (bonus: using Axios)](<#08-Httprequest-in-React-(bonus-using-Axios)>)
  - [1. API là gì?](#1-API-là-gì)
  - [2. Call API by axios](#2-Call-API-by-axios)
    - [2.1 Get request](#21-Get-request)
    - [2.2 Post Requests](#22-Post-request)
    - [2.3 Delete Requests](#33-Delete-request)
  - [3. Tổ chức API module như thế nào?](#3-Tổ-chức-API-module-như-thế-nào?)

---

## 1.API là gì?

Đơn giản, nó là chuẩn giao tiếp giữa client và server.
Client dùng API do server định nghĩa sẵn để nói cho server biết client mong gì.

Một vài ví dụ:

Client muốn lấy 10 products đầu tiên

> GET https://js-post-api.herokuapp.com/api/products?_limit=10&_page=1

Client muốn xoá product có ID 12345

> DELETE https://js-post-api.herokuapp.com/api/products/12345

## 2.Call API by axios(recommend)

Đặc điểm của axios:

- Sử dụng được cả trên browser và node.js
- Inteceptors(Chặn request và respone)
- Cancel request
- Auto transform JSON data

Cách cài đặt:
Sử dụng npm

> $ npm install --save axios

Sử dụng yarn

> $ yarn add axios

Bây giờ chúng ta sẽ tìm hiểu về một số phương thức HTTP mà chúng ta hay sử dụng với axios( Ví dụ: GET/POST/DELETE)

### 2.1 Get request

Ban đầu chúng ta tạo một function component có tên PersonList như sau(mình lấy ví dụ sử dụng function component nhé):

```jsx
import React, { useEffect, useState } from "react";
import axios from "axios";
export default function PersonList() {
  const [persons, setPersons] = useState([]);
  useEffect(() => {
    axios
      .get(`https://jsonplaceholder.typicode.com/users`)
      .then((res) => {
        const persons = res.data;
        setPersons(persons);
      })
      .catch((error) => console.log(error));
  }, []);

  return (
    <ul>
      {persons.map((person) => (
        <li>{person.name}</li>
      ))}
    </ul>
  );
}
```

Như các bạn đã thấy, đoạn mã này gửi một request với phương thức GET đến URL https://jsonplaceholder.typicode.com/users, nếu thành công kết quả sẽ được render ra danh sách person.name bằng method map của function trong js. À lưu ý là nhớ call API trong useEffect nhé. Đây là đoạn code render ra list person:

```jsx
<ul>
  {persons.map((person) => (
    <li>{person.name}</li>
  ))}
</ul>
```

Và nếu lỗi thì in lỗi ra console trong .catch(). Bạn có thể tưởng tượng nó giống như việc bạn mở trình duyệt ra, gõ vào đường dẫn cần đến và chờ, nếu thông tin được hiển thị bạn sẽ đọc được, nếu không một thông báo lỗi hiển thị lên trên trình duyệt.

```jsx
axios
  .get(`https://jsonplaceholder.typicode.com/users`)
  .then((res) => {
    const persons = res.data;
    setPersons({ persons });
  })
  .catch((error) => console.log(error));
```

### 2.2 Post request

Với việc cho phép người dùng nhập dữ liệu vào form sau đó submit gửi nội dung lên API thì ta có thể sử dụng phương thức POST.

```jsx
import React, { useEffect, useState } from "react";
import axios from "axios";

export default function PersonList() {
  const [name, setName] = useState("");
  const handleChange = (event) => {
    setName({ name: event.target.value });
  };

  const handleSubmit = (event) => {
    event.preventDefault();

    const user = {
      name: name,
    };

    axios
      .post(`https://jsonplaceholder.typicode.com/users`, { user })
      .then((res) => {
        console.log(res);
        console.log(res.data);
      });
  };

  return (
    <div>
      <form onSubmit={handleSubmit}>
        <label>
          Person Name:
          <input type="text" name="name" onChange={handleChange} />
        </label>
        <button type="submit">Add</button>
      </form>
    </div>
  );
}
```

Trong ví dụ trên sau khi nhập thông tin vào form xong rồi click “Add” để thêm user vào CSDL thì ta sử dụng phương thức: axois,post(‘https://jsonplaceholder.typicode.com/users’,{user})
Trong đó:
https://jsonplaceholder.typicode.com/users chính là url API mà chúng ta post lên, còn {user} là user mà chúng ta muống add vào CSDL.
Lưu ý: User phải là 1 object và các thuộc tính của user phải trùng với trường mà bên server yêu cầu.

### 2.3 Delete request

Chúng ta có thể xóa một hoặc nhiều person khỏi API bằng cách sử dụng axios.delete và truyền vào URL dưới dạng tham số id.

```jsx
import React, { useState } from "react";
import axios from "axios";

export default function PersonList() {
  const [id, setId] = useState("");
  const handleChange = (event) => {
    setId({ id: event.target.value });
  };

  const handleSubmit = (event) => {
    event.preventDefault();

    axios
      .delete(`https://jsonplaceholder.typicode.com/users/${id}`)
      .then((res) => {
        console.log(res);
        console.log(res.data);
      });
  };

  return (
    <div>
      <form onSubmit={handleSubmit}>
        <label>
          Person ID:
          <input type="text" name="id" onChange={handleChange} />
        </label>
        <button type="submit">Delete</button>
      </form>
    </div>
  );
}
```

Giống như phương thức post là khi gửi request lên server, cùng với url API ta cần truyền thêm tham số id để server biết rằng client muống xóa đối tượng nào thông qua id của đối tượng đó.

## 3.3. Tổ chức API module như thế nào?

Đầu tiên chúng ta sẽ setup biến môi trường trong file .env

> REACT_APP_API_URL=https://js-post-api.herokuapp.com/api

Vì tất cả các API đều có giống nhau đoạn ban đầu và khác ở đuôi đằng sau lên ta sẽ tạo 1 file baseAPI.

```jsx
import axios from "axios";
import queryString from "query-string";
import { getTokenAcess } from "../utils/localstorage";

const baseURL = axios.create({
  baseURL: process.env.REACT_APP_API_URL,
  timeout: 5000,
  headers: {
    "Content-Type": "application/json",
  },

  paramsSerializer: (params) => queryString.stringify(params),
});

baseURL.interceptors.request.use((request) => {
  const accessToken = getTokenAcess();
  const accessHeader = `Bearer ${accessToken}`;
  request.headers["Authorization"] = accessHeader;
  return request;
});
export default baseURL;
```

Lưu ý: Sau khi đăng nhập mỗi user sẽ có 1 token riêng và mỗi lần khi ta call API để làm gì đó thì ta phải kèm theo cả token. Các bạn tìm hiểu thêm về authentication nhé!
