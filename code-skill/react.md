## 目录

* [react与react-router的一次交互](#react与react-router的一次交互)
* [对于渲染组件](#对于渲染组件)
* [基于antd的Modal](#基于antd的modal)
* [基于react, node, antd的crud](#基于react-node-antd的crud)

### react与react-router的一次交互

1. redux的两个map函数。
2. 添加点击事件。
3. 点击事件调用class里的函数。
4. class里的函数调用actions。
5. 写actionTypes。
6. 写reducer。

```
// index.js
onClick={(item) => this.newRole(item)};
onCancel={() => this.newRole(null)};

newRole(item) {
    this.props.newRole(item);
}

mapStateToProps(state) { // 来源state树
    return {
        state: state.xxx,
    };
}

const { editRole } = this.props.state;
{ editRole ?
 <div>1</div>
: null }

// actions.js
new Roel(data) {
    dispatch({
        type: NEW_ROLE,
        data,
    });
}

// actionTypes.js
const NEW_ROLE = 'new_role';

// reducers.js
const initialState = {
    editRole: null,
};
case NEW_ROLE:
    return Object.assing({}, state, action.data);
```

### 对于渲染组件

1. 多用&&。
2. 多用三目运算符。

### 基于antd的Modal

1. 可以在state里面存放一个对象，用于表示弹出层的信息。
2. 如果对象的值为null, 渲染的时候，弹出层隐藏。
3. 如果对象的值是{}, 渲染的时候，弹出层显示，表示新增信息。
4. 如果对象的值是{ a, b}, 渲染的时候，弹出层显示，表示更新信息。
5. 当点击弹出层的onSubmit时，将对象传递到父级组件，父级组件判断是否存在_id, 如果不存在，则是create，如果存在，则是update。

### 基于react, node, antd的crud
