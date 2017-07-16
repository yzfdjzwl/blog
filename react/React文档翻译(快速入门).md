> 翻译自react的大部分文档，方便自己查阅。

## 目录 

* [生命周期](#生命周期)
    * [实例化](#实例化)
    * [存在期](#存在期)
    * [销毁期](#销毁期)
* [state](#state)
    * [Do Not Modify State Directly](#do-not-modify-state-directly)
    * [State Updates May Be Asynchronous](#state-updates-may-be-asynchronous)
    * [State Updates are Merged](#state-updates-are-merged)
    * [The Data Flows Down](#the-data-flows-down)
* [条件渲染(Conditional Rendering)](#条件渲染conditional-rendering)
    * [使用&&操作符替换if](#使用操作符替换if)
    * [使用?:替换if else](#使用替换if-else)
    * [Preventing Component from Rendering](#preventing-component-from-rendering)
* [Lists and Keys](#lists-and-keys)
    * [Keys](#keys)
* [表单](#表单)
    * [受控组件](#受控组件)
    * [The textarea tag](#the-textarea-tag)
    * [Select](#select)
* [Handling Multiple Inputs](#handling-multiple-inputs)
* [Lifting State Up](#lifting-state-up)
* [组合与继承](#组合与继承)
    * [Containment](#containment)
* [特殊化(Specialization)](#特殊化specialization)
    * [So What About Inheritance?](#so-what-about-inheritance)
* [思考React](#思考react)
    * [Step1: Break The UI Into A Component Hierarchy](#step1-break-the-ui-into-a-component-hierarchy)
    * [Step2: Build A Static Version In React](#step2-build-a-static-version-in-react)
    * [Step3: Identify The Minimal (but complete) Representation Of UI State](#step3-identify-the-minimal-but-complete-representation-of-ui-state)
    * [Step4: Identify Where You State Live](#step4-identify-where-you-state-live)
    * [Step5: Add Inverse Data Flow](#step5-add-inverse-data-flow)
* [参考](#参考)

## 生命周期

> 仅仅关于客户端的React

### 实例化

1. getDefaultProps()
2. getInitialState()
3. componentWillMount()
4. render()
5. componentDidwMount()

### 存在期

1. componentWillReceiveProps()
2. shouldComponentUpdate()
3. componentWillUpdate()
4. componentDidUpdate()

### 销毁期

1. componentWillUnmount

## state

### Do Not Modify State Directly

不能直接设置`state`, 这不会再次渲染一个component(this will not re-render a component), 只有一个地方可以直接设置state。
```
// wrong, this
this.state = { comments: 'Hello world'};
```
应该使用`setState()`方法。
```
// true
this.setState({
  comments: 'Hello world',
});
```

### State Updates May Be Asynchronous

React可以将多个`setState()`调用分批到单个更新中以实现性能。

因为`this.props`与`this.state`都可能是异步更新，因此不可以依靠它们的值来计算下一次的state。举例来说，下面就是错的:

```
// wrong
this.setState({
  counter: this.state.counter + this.props.increment,
});
```

为了解决这个问题，使用`setState()`的第二种形式，它接受一个函数，函数的第一个参数是之前的state, 第二个参数是props。

```
// correct
this.setState((prevState, props) => ({
  counter: prevState.counter + props.increment,
}));
```

### State Updates are Merged

当你调用`setState()`的时候，React合并你提供的对象到当前的state当中。(When you call setState(), React merges the object you provide into the current state.)

### The Data Flows Down

## 条件渲染(Conditional Rendering)

### 使用&&操作符替换if

```
function MailBox(props) {
  return (
    <div>
      <h1>Hello!</h1>
      {unreadMessages.length > 0 &&
        <h2>
          You have {unreadMessages.length} unread messages.
        </h2>
      }
    </div>
  );
}
```

但是这种方法，值得注意的是，return后面跟的最外层必须是html元素。而不能是下面这样:
```
function MailBox(props) {
  return (
    {unreadMessages.length > 0 &&
      <h2>
        You have {unreadMessages.length} unread messages.
      </h2>
    }
  );
}
```

### 使用?:替换if else

```
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return(
    <div>
      The user is <b>{isLogginIn ? 'currently' : 'not'}</b> logged in.
    </div>
  );
}
```

还可以用于更大的表达式。

```
render() {
  const isLoggedIn = this.state.isLoggedIn;
  return (
    <div>
      {isLoggedIn ? (
        <LogoutButton onClick={this.handleLogoutClick} />
      ) : (
        <LoginButton onClick={this.handleLoginClick} />
      )}
    </div>
  );
}
```

### Preventing Component from Rendering

极少的情况下，要让组件隐藏起来，那么可以通过`return null`来实现。

## Lists and Keys

在react中使用list，必须要为每个list的元素指定key(每个item的key是唯一的)。

### Keys

keys帮助React确定哪些item已经改变了，被添加了或者被移除了。

不推荐设置key的值是索引，如果这个数组要被排序的话(We don't recommand using indexes for keys if the items can reorder, as that would be slow.)。因为这将会被慢。

key只在周围数组的上下文中有意义。

## 表单

在react中，HTML表单元素与其他DOM元素有所不一样。因为表单元素自然保持着一些内部状态。举例来说，这个HTML格式的表单接受一个单个的name:

```
<form>
  <label>
    Name:
    <input type="text" name="name" />
  </label>
  <input type="submit" value="Submit" />
</form>
```

这个表单拥有HTML表单的默认行为，当提交表单的时候，浏览器会打开一个新的页面。如果你在react中这么使用的话，它依然起作用。但是在大多数情况下，拥有一个可以处理表单并可访问用户输入表单的数据的JavaScript函数是很方便的。实现这一点的标准方法是使用一种叫作'受控组件'的技术。

### 受控组件

在HTML中，类似于`input textarea select`这样的表单元素通常维持着它们自己的状态，并且基于用户的输入更新。在React中，可变状态通常保存在组件的`state`属性中，并且只能通过`setState()`来更新。

我们可以通过使react成为'真正的唯一来源', 将两者结合起来。然后，呈现表单的React组件也控制后续用户输入时该表单中发生的情况。 一个输入表单元素的值被React控制，被称为受控组件。

一个简单的受控组件如下：

```
class NameForm extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: '',};
    this.handleChange = this.handleChange.bind(this);
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(e) {
    this.setState({
      value: e.target.value,
    });
  }

  handleSubmit(e) {
    console.log(this.state.value);
    e.preventDefault();
  }

  render() {
    return (
      <form onSubmit={this.handleSubmit}>
        <label>
          name:
          <input type="text" value={this.state.value} onChange={this.handleChange} />
        </labeL>
        <input type="submit" value="Submit" />
      </form>
    );
  }
}
```

使用受控组件，每个状态的突变都具有相关联的处理函数。这使得修改或验证用户的输入很直接。举例来说，如果我们想要用户强制性这些名称用全部大写字母写，我们可以把handleChange写为:

```
handleChange(e) {
  this.setState({
    value: e.target.value.toUpperCase(),
  });
}
```

### The textarea tag

在HTML中，一个`textarea`元素定义它的文本通过children来定义。

而在React中，一个`textarea`元素是使用value属性来定义的。其使用方法和`input`差不多的。

### Select

在HTML中，select创建一个下拉列表，比如像下面这样的:

```
<select>
  <option value="baoma">baoma</option>
  <option selected value="benchi">benchi</option>
  <option value="aodi">aodi</option>
</select>
```

而在React中，是在根select组件里使用value属性来选中的。在受控组件中，这更加方便，因为你只需要在一个地方更新它，比如下面这样:

```
class FormItem extends React.Component {
  constructor(props) {
    super(props);
    this.state = {value: 'benchi'};
    this.handleChange = this.handleChange.bind(this); 
    this.handleSubmit = this.handleSubmit.bind(this);
  }

  handleChange(e) {
    this.setState({
      value: e.target.value,
    });
  }

  handleSubmit(e) {
    console.log(e.state.value);
    e.preventDefault();
  }

  render(
    return (
      <form onSubmit={this.handleSubmit}
        <label>
          Pick your favorite car:
          <select value={this.state.value} onChange={this.handleChange}>
            <option value="baoma">baoma</option>
            <option value="benchi">benchi</option>
            <option value="aodi">aodi</option>
          </select>
        </label>
        <input type="submit" value="Submit" />
      />
    );
  );
}
```

`<input type="text" /> <textarea> <select>`它们都工作的差不多，它们都接受一个`value`属性，这个属性可以用来实现一个受控组件。

## Handling Multiple Inputs

当你需要处理多个受控组件`input`元素的时候，你可以给每个元素添加`name`属性，并且让处理函数根据该值(event.target.name)选择要进行的操作。

举个例子:

```
class Reservation extends React.Component {
  constructor(props) {
    super(props);
    this.setState = {
      isGoing: true,
      numberOfGuests: 2,
    };

    this.handleChange = this.handleChange.bind(this);
  }

  handleChange(e) {
    const target = e.target;
    const value = target.type === 'checkbox' ? target.checked : target.value;
    const name = target.name;

    // es6 computed property name
    this.setState({
      [name]: value,
    });
  }

  render() {
    return (
      <form>
        <label>
          Is going:
          <Input
            name="isGoing"
            type="checkbox"
            checked={this.state.isGoing}
            onChange={this.handleChange} />
        </label>
        </br>
        <label>
          Number of guests:
          <input
            name="numberOfGuests"
            type="number"
            value=={this.state.numberOfGuests}
            onChange={this.handleChange} />
        </label>
      </form>
    );
  }
}
```

可能你觉得太麻烦，你需要[非受控组件](https://facebook.github.io/react/docs/uncontrolled-components.html)

## Lifting State Up

在react中，共享state是通过将它移动到需要它的组件的最接近的共同祖先来实现的。这被称为提升状态。如果祖先拥有了共享状态，那么就有了`source of true`。

官方文档给的那个例子，简单来说，两个组件的值相互依赖的时候，就将state提升到它们最近的共同祖先，通过在父组件里进行相应的值转换，然后通过props将值传递给子组件。然后两个子组件的值改变时，调用的函数是父组件传递下来的。

**因此, 可以通过子组件调父组件传递过来的函数,将子组件的值传递给父组件， 来改变父组件的state, 然后父组件计算值后，通过props将计算后的值分发到各个子组件，这样就保证了唯一的来源，而子组件的值也是相互依赖(有关联)的。** 如下:

```
// 子组件
handleChange(e) {
  this.props.onTemperatureChange(e.target.value);
}
<input onChange={this.handleChange} type="text" />

// 父组件
handleChange(value) {
  this.setState({
    temperature: value,
  }); 
}
<TemperatureInput
  onTemperatureChage={this.handleChange} />
```

## 组合与继承

在React中，我们推荐使用组合来复用代码而不是继承。

### Containment

一些容器不能提前知道它们的children是谁。在`Sidebar`和`Dialog`里尤其的正常。

我们推荐这类的组件使用特殊的`children` prop来将children直接传递到它们的输出中:

```
function FancyBorder(props) {
  return (
    <div className={'FancyBorder FancyBorder-' + props.color}>
      {this.props.children}
   </div>
  );
}
```

然后让其他组件通过嵌入JSX传递任意个child给`FancyBorder`:

```
function WelcomeDialog() {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        Welcome
      </h1>
      <p className="Dialog-message">
        Thank you for visiting our spacecraft!
      </p>
    </FancyBorder>
  );
}
```

`<FancyBorder>`JSX标签中的任何内容都会作为子菜单传入FancyBorder组件。由于`FancyBorder`将`{props.children}`呈现在<div>中，所以传递的元素将显示在最终输出中。一般在开发中，还可以在路由中控制`this.props.children`是谁。比如下面这样:

```
// News.js
render() {
  return (
    <div className={style.container}>
      {this.props.children}
    </div>
  );
}

// routers.js
export function createRoutes() {
  return {
    path: '/',
    component: App,
    indexRoute: { component: Main },
    childRoutes: [
      {
        path: ':channel',
        component: NewsList,
      }
    ],
  };
}
```

这里News组件的子组件就是NewsList。

虽然不常见，但是有时候你可能在组件里需要多个'hole', 在这种情况下，你可能想出自己的习惯，而不是使用`children`:

```
function SplitPane(props) {
  return (
    <div className="SplitPane">
      <div className="SplitPane-left">
        {props.left}
      </div>
      <div className="SplitPane-right">
        {props.right}
      </div>
    </div>
  );
}

function App() {
  return (
    <SplitPane
      left={
        <Contacts />
      }
      right={
        <Chat />
      }
    />
  );
}
```

## 特殊化(Specialization)

有时我们将组件视为其他组件的"特殊情况"。举例来说，我们可以说`WelcomeDialog`是`Dialog`的一种特殊情况。

在React中，这通常由组合来实现，其中一个更"特定"的组件呈现出更"通用的组件", 并且使用`props`来配置它。

```
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-title">
        {props.message}
      </p>
    </FancyBorder>
  );
}

function WelcomeDialog() {
  return (
    <Dialog
      title="Welcome"
      message="Thank you for visiting our spacecraft!" />
  );
}
```

组合对于定义为类的组件同样适用。

```
function Dialog(props) {
  return (
    <FancyBorder color="blue">
      <h1 className="Dialog-title">
        {props.title}
      </h1>
      <p className="Dialog-message">
        {props.messgae}
      </p>
      {props.children}
    </FancyBorder>
  ); 
}

class SignUpDialog extends React.Component {
  constructor(props) {
    super(props);
    this.handleChange = this.handleChange.bind(this);
    this.handleSignUp = this.handleSignUp.bind(this);
    this.state = {login: ''};
  }

  handleChange(e) {
    this.setState({
      login: e.target.value,
    });
  }

  handleSignUp() {
    alert(`Welcome aboard, ${this.state.login}!`);
  }

  render() {
    return (
      <Dialog
        title="Mars Exploration Program"
        message="How should we refer to you?">
        <input
          value={this.state.login}
          onChange={this.handleChange} />
        <button onClick={this.handleSignUp}>
          Sign Me Up!
        </button>
      </Dialog>
    );
  }
}
```

### So What About Inheritance?

在Facebook, 我们在数千计的组件中，还没有发现任何一种情况值得我们推荐使用继承。使用Props和composition就已经很完美了。

## 思考React

### Step1: Break The UI Into A Component Hierarchy

使用单一任务原则，也就是说，一个组件理想上只做一件事情。

### Step2: Build A Static Version In React

使用mock数据构架一个没有交互的静态版本。因为构建一个静态的版本，需要大量的`typing`和不需要思考太多，而构建一个交互性的版本需要大量的思考，而不需要大量的`typing`。
如果你很熟悉`state`的概念，那么就不要使用`state`来构建静态的版本, `state`只是为了交互而保留的。
你既可以自下向上进行构建，也可以自上向下构建。在简单的例子中，通常更容易自上而下；而在较大的例子中，自下而上通常更容易一些，并且也更容易写测试。

### Step3: Identify The Minimal (but complete) Representation Of UI State

要使你的应用可以交互，你需要能够触发底层数据模型的更改, `State`让这一切变得很容易。
为了正确的构建你的app，首先你需要思考你的app需要的最小的可变的state集。这里很关键的是DRY: Don't repeat yourself。找出你的app需要的state集的最小表示，并计算出你所需要的其他需求。举例来说，如果你要构建一个`Todo List`, 只需要维持一个`todo items`的数组，不需要为数量单独保持一个state的变量。当你想要渲染todo的数量时，只需要取todos数组的长度就可以了。

如何区分是数据是state还是props，只需要问下面这三个问题:

1. 它是从父组件从props传递过来的吗？如果是，那么它不是state。
2. 它会随着时间的推移保持不变吗？如果是，那么它不是state。
3. 你可以根据组件中的其他state或props计算出它来吗？如果可以，那么它不是state。 

### Step4: Identify Where You State Live

当我们确定app的最小的state集后。下一步，我们需要确定是哪一个组件拥有state。
记住: React是单向数据流，可能不能立即清楚哪个组件拥有哪个state。这对于新手来说也是最大的挑战，因此根据下面这些步骤来解决这个问题:

对于你的应用中的每个state:

* 识别出基于那个state渲染的所有组件。
* 找一个通用的组件`find a common owner component`(单个组件，它是所有需要那个state的组件的父级组件)。
* 公共所有者(The common owner)或层次结构中较高的其他组件应该拥有state。
* 如果你找不到拥有state的组件，就创建一个新的组件，仅用于保存state，并将其添加到所有者组件上方的层次结构中。 

### Step5: Add Inverse Data Flow

子组件通过props调用父级组件 传递过来的方法(回调)来改变父级的state。

## 参考

[react官方文档](https://facebook.github.io/react/docs/state-and-lifecycle.html)
[react生命周期](https://segmentfault.com/a/1190000004168886)