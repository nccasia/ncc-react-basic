# 09.01: Khi nào nên tách function ra riêng

## Table of contents

- [09: Function-Breakdown ](#09-Function,Component-Breakdown)
  - [1. Mục đích của việc tách component ](#1-Mục-đích-của-việc-tách-component)
  - [2. Ví dụ của việc tách function, custom-hook ](#1-Ví-dụ-của-việc-tách-function-custom-hook)
    - [2.1 useDialog](#21-useDialog)
    - [2.2 usePropsForm](#22-usePropsForm)

---

## 1. Mục đích của việc tách function

Khi dự án có nhiều function bị lặp lại quá nhiều thì đây là lúc thích hợp để tách function đó ra riêng để tái sử dụng. Custom hook cũng là một trong số đó.

## 2. Ví dụ của việc tách function, custom-hook

### 2.1 useDialog

Dialog được sử dụng rất nhiều trong các dự án từ nhỏ tới lớn thay vì việc mỗi khi ta muốn dùng một dialog thì phải khai báo một state, function để quản lý trạng thái của dialog. Do đó ta có thể tạo một custom hook để gói gọn những thứ trên vào trong một function duy nhất

```jsx
import { Box, Button, Dialog } from "@material-ui/core";
import React from "react";
import ReactDOM from "react-dom";

const Modal = ({ isOpen, onClose }) => isOpen ? ReactDOM.createPortal(
  <Dialog open={isOpen}>
    <Box padding="25px">
      <Button onClick={onClose}>
        close
      </Button>
      This is Dialog
    </Box>
  </Dialog>, document.body
) : null
export default Modal

import { Button, Box} from '@material-ui/core'
function App() {
  const { isOpen, close, toggle } = useDialog()
  return (
    <div className="App">
      <Button onClick={() => toggle()} variant="outlined">
        open
      </Button>
      <Modal isOpen={isOpen} onClose={close} />
    </div>
  )
}
export default App
```

Như trên ta có thể thấy hook useDialog cung cấp cho component các state, function để tương tác với dialog thay vì việc phải khởi tạo những thứ đó từ đầu như trước đây.

Ngoài ra có thể truyền các props children, function vào Modal để tăng tính linh
hoạt cho Modal.

### 2.2 usePropsForm

Khi sử dụng các thư viện như formik, react-hook-form sẽ có những đoạn hardcode bị lặp lại liên tục như sau:

Với formik :

```jsx
import React from "react";
import { useFormik } from "formik";

const App = () => {
  const formik = useFormik({
    initialValues: {
      firstName: "",
      lastName: "",
      email: "",
    },
    onSubmit: (values) => {
      alert(JSON.stringify(values, null, 2));
    },
  });
  return (
    <>
      <form onSubmit={formik.handleSubmit}>
        <label htmlFor="firstName">First Name</label>
        <input
          id="firstName"
          name="firstName"
          type="text"
          onChange={formik.handleChange}
          value={formik.values.firstName}
        />
        <label htmlFor="lastName">Last Name</label>
        <input
          id="lastName"
          name="lastName"
          type="text"
          onChange={formik.handleChange}
          value={formik.values.lastName}
        />
        <label htmlFor="email">Email Address</label>
        <input
          id="email"
          name="email"
          type="email"
          onChange={formik.handleChange}
          value={formik.values.email}
        />
        <button type="submit">Submit</button>
      </form>
    </>
  );
};
```

Ta có thể thấy ở trên có rất nhiều props bị lặp lại dẫn tới việc phải hardcode quá nhiều và tăng số lượng dòng code lên. Đây là lúc ta có thể sử dụng một function để trả về các props như trên

```jsx
import React from "react";
import { useFormik } from 'formik';

const usePropsForm = (name) => {
  return {
    id: name,
    name: name,
    type: "text",
    onChange: {formik.handleChange},
    value={formik.values.name}
  }
}
const App = () => {
  const formik = useFormik({
     initialValues: {
       firstName: '',
       lastName: '',
       email: '',
     },
     onSubmit: values => {
       alert(JSON.stringify(values, null, 2));
     },
   });
  return (
    <>
      <form onSubmit={formik.handleSubmit}>
        <label htmlFor="firstName">First Name</label>
        <input
          {...usePropsForm("firstName")}
        />
        <label htmlFor="lastName">Last Name</label>
        <input
          {...usePropsForm("lastName")}
        />
        <label htmlFor="email">Email Address</label>
        <input
          {...usePropsForm("email")}
        />
        <button type="submit">Submit</button>
      </form>
    </>
  )
}
```

Code đã ngắn gọn hơn rất nhiều

# 09.02: Khi nào nên tách component

## Table of contents

- [09: Function,Component-Breakdown ](#09-Function,Component-Breakdown)
  - [1. Mục đích của việc tách component ](#1-Mục-đích-của-việc-tách-component)
  - [2. Các ví dụ điển hình khi nào nên tách component để tái sử dụng](#2-Các-ví-dụ-điển-hình-tách-component)
    - [2.1 Wrap Component, Layout Component hoặc Template Component](#21-Wrap-Component-Layout-Component-Template-Component) -[2.1a Component chỉ nhận props children](#21a-Component-chỉ-nhận-props-children) -[2.1b Component nhận props và có sãn các component khác](#21b-Component-nhận-props-và-có-sẵn-các-component-khác) -[2.1c Component nhận props nhưng props có liên quan đến logic bên trong và có sãn các component khác](#21b-Component-nhận-props-nhưng-props-liên-quan-đến-logic-bên-trong-và-có-sẵn-các-component-khác)

---

## 1. Mục đích của việc tách component

Khi dự án mở rộng hơn hoặc đơn giản chỉ là hiện tại đang có nhiều component tương tự nhau bị lặp lại. Người ta sẽ tìm cách tách các component ra để có thể tái sử dụng chúng.
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

Đây là các component có sẵn các component gốc từ trước và nhận thêm props children là các element

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

Đây là các component không có hoặc có sẵn các component gốc từ trước và nhận thêm props để xử lý logic bên trong component

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
      {search && <TextField variant="outlined" label="search" />}
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

Đây là một phương pháp có mục đích tương tự với phương pháp sử dụng Higher Order Component, giúp chúng ta sử dụng lại logic trên nhiều component

```jsx
import React from "react";
import { Box, Button, Typography, MenuItem } from "@material-ui/core";
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

const avaiableUsers = [
  "Thai", "Huy", "Phong", "Hieu"
]
const selectedUsers = [
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
        data={selectedUsers}
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
        data={avaiableUsers}
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
# 09.03: useRef trong React
## Table of contents

- [09: useRef ](#09-useRef)
  - [1. useRef là gì? Đặc điểm? Cú pháp? Khi nào nên sử dụng refs ](#1-useRef-là-gì)
  - [2. Ví dụ về việc sử dụng useRef ](#2-Ví-dụ-về-việc-sử-dụng-useRef)
    - [2.1 Lưu giữ giá trị ban đầu](#21-Lưu-giữ-giá-trị-ban-đầu)
  - [3. useRef mở rộng ](#3-useRef-mở-rộng)
---
## 1 useRef là gì? Khi nào nên sử dụng refs

useRef là một hook được tích hợp sẵn trong React từ phiên bản React 16.8.

Đặc điểm của useRef:
- Có khả năng tham chiếu đến DOM ELEMENT thay vì phải sử dụng JS DOM như trước đấy
- Khá giống với useState là useRef có thể lưu trữ nhưng khác biệt lớn nhất là khi giá trị của useRef thay đổi thì Component không re-render lại ( thư viện react-hook-form cũng sử dụng useRef kết hợp với các hook đi cặp với useRef để lưu giá trị của input cho tới khi thực hiện submit ) 

Khi nào nên sử dùng useRef: 
- Control focus, get text của DOM element, get state value(play/pause/timeline) của media playback,... nói chung là dùng trong những trường hợp các data này không cần đưa vào state của component.

- Trigger các transitions, animations: sử dụng refs khi một element cần trigger animation/transition của một element khác

- Tích hợp với các Third-party DOM libraries

Về cú pháp của useRef:
- Trong Class Component ( nay đã ít sử dụng do sự phổ biến của Functional Componnent )
```jsx
import React from "react";
export default class MyComponent extends React.Component {
  constructor(props) {
    super(props);
    //Khởi tạo một ref
    this.myRef = React.createRef();
  }
  render() {
    return (
      <input
        name="email"
        onChange={this.onChange}
        ref={this.myRef}
        type="text"
      />
    );
  }
}
```
- Trong Functional Componnent
```jsx
import React from "react";
function TextInputWithFocusButton() {
  const inputEl = useRef(null);
  const onButtonClick = () => {
    // `current` là một key trong "inputEl" trả về Element được gán ref
    inputEl.current.focus();
  };
  return (
    <>
      <input ref={inputEl} type="text" />
      <button onClick={onButtonClick}>Focus the input</button>
    </>
  );
}
```
## 2 Ví dụ về việc sử dụng useRef
### 2.1 Lưu giữ giá trị ban đầu
```jsx
import React from "react";
import { Box, Dialog, Button } from "@material-ui/core";
const selectedList = [
  {
    name: "male", isChecked: true 
  },
  {
    name: "female", isChecked: false 
  },
  {
    name: "ageOver18", isChecked: false 
  },
]

const DialogFilter = ({open, onDialog, onSubmitList}) => {
  const [ newSelectedListm setNewSelectedList ] = useState([])
  const prevSelected = useRef(selectedList)
  const handleSelected = (e) => {
    const index = selectedList.indexOf((item,index) => return item.name === e.target.name )
    setNewSelectedList(prevState => [
      ...prevState,
      {
        ...prevState[index],
        isChecked: !prevState[index].isChecked
      }
    ])
  }
  functon resetDefaultFilter() {
    setNewSelectedList(prevSelected)
    onDialog()
  }
  useEffect(() => {
    setNewSelectedList(selectedList)
    // clean up ref
    return () => prevSelected.current = {}
  },[])
  return (
    <Dialog open={open}>
      <Box>
      {
        selectedList.map((item,index) => 
          <Box display="flex" justifyContent="space-between">
            <Typography variant="h6" color="primary">{item.name}</Typography>
            <Checkbox onChange={handleSelected} checked={item.isChecked} name={item.name} />
          </Box>
        )
      }
      </Box>
      <Button onCick={() => onSubmitList}>
        Save
      </Button>
      <Button onCick={() => resetDefaultFilter}>
        Cancel
      </Button>
    </Dialog>
  )
}
const App = () => {
  const [ selectedValue, setSelectedValue ] = useState(selectedList)
  const [ isOpen, setOpen ] = useState(false)
  function handleDialog() {
    setOpen(!isOpen)
  }
  fucntion submitFilter(list) {
    setSelectedValue(list)
    handleDialog()
  }
  return (
    <>
      <Button onClick={() => handleDialog()}>
      </Button>
      <DialogFilter 
        open={isOpen} 
        selectedList={selectedList}
        onDialog={handleDialog}
        onSubmitList={onSubmitList}
      />
    </>
  );
}
```

Docs vẫn đang trong quá trình hoàn thiện !!!
