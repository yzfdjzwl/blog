<!-- vim-markdown-toc GFM -->
* [TodoList](#todolist)
  * [1. 编写actions.js](#1-编写actionsjs)
  * [2. 分析state](#2-分析state)
    * [试着拆分成多个reducer](#试着拆分成多个reducer)
  * [3. 了解store](#3-了解store)
  * [4. 了解redux数据流生命周期](#4-了解redux数据流生命周期)
  * [5. 分析容器组件和展示组件](#5-分析容器组件和展示组件)
    * [搞清楚，数据到底是如何流动的？](#搞清楚数据到底是如何流动的)
  * [6. 编写展示组件的代码](#6-编写展示组件的代码)
  * [7. 编写容器组件](#7-编写容器组件)
  * [8. 传入store](#8-传入store)
  * [9. 总结](#9-总结)
  * [10. 参考](#10-参考)

<!-- vim-markdown-toc -->
# TodoList

[脚手架Github地址](https://github.com/knownsec/generator-modation)

## 1. 编写actions.js

## 2. 分析state

### 试着拆分成多个reducer

## 3. 了解store

1. `store.getState()`
2. `store.dispatch(action)`
3. `store.subscribe(listener)注册监听器`
4. `调用store.subscribe(listener)返回的函数来注销监听器`

## 4. 了解redux数据流生命周期

1. `store.dispatch(action);`
2. store调用传入的reducer。
3. 根reducer将多个子reducer输出的state合成一个单一的state树。
4. store保存了根reducer返回的state树。

## 5. 分析容器组件和展示组件

在这里，我遇到了很多问题。展示组件就没有什么好说的了，主要是容器组件。

### 搞清楚，数据到底是如何流动的？

下面举例：

1. React组件上有一个点击事件。
2. 当点击之后，点击事件对应一个dispatch(actionCreator())。
3. store会将actionCreator()返回的action以及当前的state传递给reducer。
4. reducer收到action，然后根据action.type更新state，并且返回新的state。
5. store保存新的state。
6. state更新后，组件调用render()方法。

那么问题来了：

由于展示组件，没有数据，那么数据该是如何来的？

回答：数据是从存放在state里的，如何将state里的数据，传递给展示组件呢？使用connect()函数，它接受两个参数，两个参数分别是函数。

```
function mapStateToProps(state) {
  return {
    todos: selectTodos(state.main.todos, state.main.visibilityFilter),
    visibilityFilter: state.main.visibilityFilter,
  };
}

function mapDispatchToProps(dispatch) {
  return {
    onAddClick: text => dispatch(addTodo(text)),
    onFilterChange: nextFilter => dispatch(setVisibilityFilter(nextFilter)),
  };
}

class Main extends Component {
  render() {
    return (
      <AddTodo
        onAddClick={this.props.onAddClick}
      />
      <VisibleTodoList />
      <Footer
        onFilterChange={this.props.onFilterChange}
        visibilityFilter={this.props.visibilityFilter}
      />
    );
  }
}

Main.propTypes = {
  onAddClick: PropTypes.func.isRequired,
  onFilterChange: PropTypes.func.isRequired,
  visibilityFilter: PropTypes.oneOf([
    'SHOW_ALL',
    'SHOW_ACTIVE',
    'SHOW_COMPLETED',
  ]);
};

export default connect(mapStateToProps, mapDispatchToProps)(Main);
```

* 上面的demo，是将Main作为一个展示组件。
* 将`mapStateToProps`和`mapDispatchToProps`注入到`Main`里。
* 所以在`Main`组件里，就可以使用注入的方法和属性了。
* 值得注意的地方，在Main里使用的props都要进行检测，也就是下面的`Main.propTypes`。
* 除了将注入和组件写在一个js文件里，还可以将它们分开写。
* 就比如VisibleTodoList和TodoList分别写。然后将2个函数注入到TodoList里去。
* 对了，初始化的state是写在reducer里的，因为无论如何都会去调reducer。


在做的过程中，我还遇到了一个问题，它在几个地方三番五次的阻挠我。

```
// 代码里，我是这么获取state的数据的。
state.todos:
state.visibleFilter;

// 但是事实上，使用的脚手架里，它对app包了一层路由，它在最外层的reducer里的代码是这样的。
import main from 'containers/Main/reducer';

export default function createReducer(asyncReducers) {
  return combineReducers(
    main,
    routing: routerReducer,
    ...asyncReducers,
  );
}

// 所以我本身拿到的state是包含了main和routing这两个对象的。因此我要拿我组件里的state的数据，应该下面这样：
state.main.todos;
state.visibleFilter;
```

## 6. 编写展示组件的代码

1. class类
2. 每个组件都要对propTypes进行验证
3. 要export

## 7. 编写容器组件

1. 定义`mapStateToProps()`将当前的state映射到组件的props, 读取state操作。
2. 定义`mapDispatcherToProps()`, 分发action操作。
  * 传入dispatch方法。
  * 返回期望注入到展示组件的props中的回调方法。
  * 回调方法里，可以分发action。
3. 使用connect()方法。
```
export default const VisibleTodoList = connect(
  mapStateToProps,
  mapDispatcherToProps,
)(TodoList);
// TodoList为要被注入的展示组件
```

## 8. 传入store

使用React Redux里的Provider组件，将store注入到Provider组件，它可以让所有**容器组件**都可以访问到store。

## 9. 总结

当我遇到问题：

1. 要沉着冷静。
2. 要管理好时间。
3. 别被bug或error搞的不高兴，要高兴，又有煅炼思维的机会了。
4. 要思考这是为什么？
5. 要搞清楚问题的本质。
6. 要探究问题，探究数据的流动。

## 10. 参考
[React中文文档](http://cn.redux.js.org/index.html)
[Github地址](https://github.com/yzfdjzwl/react-redux-todolist-demo)