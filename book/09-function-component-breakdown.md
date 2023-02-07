# 09.01: Khi nào nên tách function ra riêng

# 09.02: Khi nào nên tách component

## Table of contents

- [08: Http request in React (bonus: using Axios)](<#08-Httprequest-in-React-(bonus-using-Axios)>)
  - [1. API là gì?](#1-API-là-gì)
  - [2. Call API by axios](#2-Call-API-by-axios)
    - [2.1 Get request](#21-Get-request)
    - [2.2 Post Requests](#22-Post-request)
    - [2.3 Delete Requests](#33-Delete-request)
  - [3. Tổ chức API module như thế nào?](#3-Tổ-chức-API-module-như-thế-nào?)

---

## 1. Mục đích của việc tách component

Khi dự án mở rộng hơn hoặc đơn giản chỉ là hiện tại đang có nhiều component tương tự nhau bị lặp lại. Thì người ta sẽ tìm cách tách các component ra để có thể tái sử dụng chúng.

Để dễ trình bày mình sẽ đặt tên chung cho các component được tái sử dụng là component A

Ưu điểm:

- Đảm bảo tính đồng bộ cho code vì các component A được tái sử dụng, dẫn đến hạn chế các sai sót
- Khi có sự thay đổi về giao diện hoặc chức năng mà có liên quan đến việc sử dụng component A thì sẽ chỉ phải sửa lại component A hoặc nếu có thể thì chỉ là mở rộng trong nội bộ component A
  Nhược điểm:
- Việc tách component A ra riêng là con dao hai lưỡi. Vì phải hoạch định được chính xác các component nào có thể tái sử dụng để đưa ra riêng cũng như thiết lấp logic bên trong component đủ tốt để có thể dễ dàng trong việc sử dụng.

## 2. Các ví dụ điển hình khi nào nên tách component để tái sử dụng

### 2.1 Wrap Component, Layout Component hoặc Template Component

#### 2.1a Component chỉ nhận props children

Đây là các component cực kỳ đơn giản và dễ sử dụng, chúng chỉ nhận vào props children là các element

```jsx
import React from "react";
import { Box, Typography } from "@material-ui/core";

const cssContainer = {
  width: "100%",
  borderRadius: "50px"
  margin: "15px 25px",
}

// Component Header sẽ mặc định có các thuộc tính css của cssContainer
const HeaderTemplate = ({ children }) => {
  return (
    <Box {...cssContainer}>
      {children}
    </Box>
  )
}

// Sử dụng thực tế
const App = () => {
  return (
    <HeaderTemplate>
      <Typography variant="h2" color="primary">
        Đây là Header
      </Typography>
    </HeaderTemplate>
  )
}
```

#### 2.1b Component nhận props và có sãn các component khác

Đây là các component không có hoặc có sẵn các component gốc từ trước và nhận thêm props children là các element

```jsx
import React from "react";
import { Box, Button } from "@material-ui/core";
import { useHistory } from "react-router-dom";

const cssContainer = {
  width: "100%",
  boxSizing: "border-box",
  margin: "15px 25px",
  display: "flex",
  justifyContent: "space-between",
};

// Component Header sẽ mặc định có các thuộc tính css của cssContainer
const HeaderTemplate = ({ children, link }) => {
  const history = useHistory();
  function routerPrevPage(link) {
    history.push(link);
  }
  return (
    <Box {...cssContainer}>
      {children}
      {/* Button dùng để router về trang trước luôn có sẵn*/}
      <Button onClick={() => routerPrevPage(link)}>Exit</Button>
    </Box>
  );
};

// Sử dụng thực tế
const App = () => {
  return (
    <HeaderTemplate link="some link">
      <Typography variant="h2" color="primary">
        Đây là Header có button router
      </Typography>
    </HeaderTemplate>
  );
};
```

#### 2.1c Component nhận props nhưng props có liên quan đến logic bên trong và có sãn các component khác

```jsx
import React from "react";
import { Box, Button } from "@material-ui/core";
import { useHistory } from "react-router-dom";

const cssContainer = {
  width: "100%",
  boxSizing: "border-box",
  margin: "15px 25px",
  display: "flex",
  justifyContent: "space-between",
};

// Component Header sẽ mặc định có các thuộc tính css của cssContainer
const HeaderTemplate = ({ children, link, search }) => {
  const history = useHistory();
  function routerPrevPage(link) {
    history.push(link);
  }
  return (
    <Box {...cssContainer}>
      {children}
      {/* Button dùng để router về trang trước luôn có sẵn*/}
      {search && <TextField variant="outlined" label="search" />}
      {/* Button dùng để router về trang trước luôn có sẵn*/} 
      {link && <Button onClick={() => routerPrevPage(link)}>Exit</Button>}
    </Box>
  );
};

// Sử dụng thực tế
const App = () => {
  return (
    <HeaderTemplate link="some link" seacrh>
      <Typography variant="h2" color="primary">
        Đây là Header có button router, search input
      </Typography>
    </HeaderTemplate>
  );
};
```
### 2.2 Render props
```jsx
import React from "react";
import { Box, Button } from "@material-ui/core";
import { useHistory } from "react-router-dom";
import AddIcon from '@material-ui/icons/Add';
import ClearIcon from '@material-ui/icons/Clear';

const cssContainer = {
  width: "100%",
  boxSizing: "border-box",
  margin: "15px 0",
  display: "flex",
  justifyContent: "space-between",
};

const passedUsers = [
  "Thai", "Huy", "Phong", "Hieu"
]
const rejectedUsers = [
  "Hien", "Long"
]

const List = ({ data, render }) => data.map((user, index) => render(user))
const WrapListItem = ({children}) => <Box {...cssContainer}>{children}</Box> 
// Sử dụng thực tế
const App = () => {
  return (
    <Box>
      {/* List chỉ có button clear*/}
      <List 
        data={rejectedUsers} 
        render={user => 
          <WrapListItem>
            <Button variant="outlined" startIcon={<ClearIcon />}/>
            <Typography variant="h6" color="secondary">
              {user.name}
            </Typography>
          </WrapListItem>
        }
      />
      {/* List có button add và textfield*/}
      <List 
        data={passedUsers} 
        render={user => 
          <WrapListItem>
            <Button variant="outlined" startIcon={<AddIcon />}/>
            <Typography variant="h6" color="primary">
              {user.name}
            </Typography>
            <TextField variant="outlined" label="Type-User">
              {
                ["intern", "fresher", "middle", "senior"].map((option,index) => 
                  <MenuItem key={index} value={option}>
                    {option}
                  </MenuItem>
                )
              }
            </TextField>
          </WrapListItem>
        }
      />
    <Box>
  );
};
``` 