# 07: React Router

Table of contents

- [07: React Router](#03-React-router)
  - [1.React-router là gì?](#1-React-router-là-gì?)
  - [2. Cách cài đặt](#2-Cách-cài-đặt)
  - [3. API](#3-API)
    - [3.1 BrowserRouter và HashRouter](#31-BrowserRouter-và-HashRouter)
    - [3.2 Route](#32-Route)
    - [3.3 Switch](#33-Switch)
    - [3.4 Link](#34-Link)
    - [3.5 Redirect](#35-Redirect)

---

## 1. React Router là gì?

React-Router là một thư viện định tuyến (routing) tiêu chuẩn trong React. Nó giữ cho giao diện của ứng dụng đồng bộ với URL trên trình duyệt. React-Router cho phép bạn định tuyến "luồng dữ liệu" (data flow) trong ứng dụng của bạn một cách rõ ràng. Các bạn có thể hiểu đơn giản là nếu bạn có URL này, nó sẽ tương đương với Route này, và giao diện tương ứng.

## 2. Cách cài đặt

> npm install react-router

# or

> yarn add react-router

## 3. API

Chúng ta sẽ đi tỉm hiểu một số API phổ biến của thằng react-router.
Cách import:

> import { BrowserRouter as Switch,Router, Route, Link, NavLink } from "react-router-dom";

### 3.1 BrowserRouter và HashRouter

React-Router cung cấp cho chúng 2 thành phần hay sử dụng đó là BrowserRouter & HashRouter. Hai thành phần này khác nhau ở kiểu URL mà chúng sẽ tạo ra và đồng bộ.
BrowserRouter: Được sử dụng phổ biến hơn, nó sử dụng History API có trong HTML5 để theo dõi lịch sử bộ định tuyến của bạn.
HashRouter: Sử dụng hash của URL (window.location.hash) để ghi nhớ mọi thứ.

Thông thường chúng ta sẽ bọc App của chúng ta trong BrowerRouter để sử dụng được các API khác của react-router

### 3.2 Route

Route: Thằng này sẽ kết nối giữa một URL và một Component. Điều đó có nghĩa là khi người dùng truy cập theo một URL trên trình duyệt, một Component tương ứng sẽ được render trên giao diện.

```jsx
<Router>
  <div className="App">
    <Route path="/" exact component={Home} />
    <Route path="/about" component={About} />
    <Route path="/contact" component={Contact} />
    <Route component={NotFound} />
  </div>
</Router>
```

Trong đó:

- **path**: Là đường dẫn trên URL.
- **exact**: Liúp cho route này này chỉ hoạt động nếu URL trên trình duyệt phù hợp tuyệt đối với giá trị của thuộc tính path của nó.
- **component**: Là component sẽ đươc load ra tương ứng với Route đó.
- **exact**: khi nào url chỉ có / hoặc ko có gì thì nó mới render thằng Home.

### 3.3 Switch

Switch: Chạy lần lượt qua từng Router và nhình thấy thằng url của Router nào thì chỉ render thằng component của Router đấy thôi vào không xét thằng khác bên dưới nữa để tránh trường hợp render linh tinh. Bình thường ta sẽ bọc các Route trong thằng Switch này.

```jsx
<Switch>
  <Route exact path="/">
    <Home />
  </Route>
  <Route path="/about">
    <About />
  </Route>
  <Route path="/:user">
    <User />
  </Route>
  <Route>
    <NoMatch />
  </Route>
</Switch>
```

### 3.4 Link

Trong HTML thì cặp thẻ để chuyển hướng đó là thẻ <a></a> thì trong react chúng ta sẽ dử dụng cặp thẻ <Link></Link> được import từ React-Router.

```jsx
<Link to="/about">About</Link>
```

trong đó:

- to: Giống như thuộc tính href trong thẻ a.

Bình thường khi dùng thẻ <a></a> để tạo đường link, khi ta bấm vào nó thì sẽ đến đường link đó và reload lại trang, nhưng khi ta dùng <Link> của react-router thì điều đó sẽ không sảy ra mà nó sẽ đến luôn đường link đó mà không reload lại trang( kì diệu chưa =)) ).

Ngoài ra ta cũng có thể dùng Navlink. Nó rất giống với Link về cách sử dụng, nhưng NavLink tốt hơn vì nó hỗ trợ thêm một số thuộc tính nhưlà activeClassName và activeStyle 2 thuộc tính này giúp cho khi mà nó trùng khớp thì nó sẽ được active lên và chúng ta có thể style cho nó.

```jsx
<NavLink
  exact
  activeStyle={{
    backgroundColor: "white",
    color: "red",
  }}
  to="/"
  className="my-link"
>
  Trang Chu
</NavLink>
```

### 3.5 Redirect

<Redirect> sẽ điều hướng trang đến một đường dẫn mới. Đường dẫn mới sẽ ghi đè đường dẫn cũ trong history stack.

```jsx
<Route exact path="/">
  {loggedIn ? <Redirect to="/dashboard" /> : <PublicHomePage />}
</Route>
```

```jsx
<Redirect to="/somewhere/else" />
```

Trong đó:

- to: URL chuyển hướng đến

Trên đây là một chút kiến thức cơ bản về react-router, ngoài ra còn có các hookAPI của react-router các bạn có thể tìm hiểu thêm ở:
[https://reactrouter.com](https://reactrouter.com)
