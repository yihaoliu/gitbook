# MobX入坑

MobX 是一个简单、方便扩展、久经考验的状态管理解决方案。这个教程旨在十分钟内向你介绍 MobX 的一些重要概念。MobX 是一个独立的苦，不过大多数人都把它和 React 一起使用，所以本教程也就着眼于这个组合展开。

###核心概念

State 是每一个应用程序的核心部分，而使用一个不合规范的 State 则是让你的应用充满 bug 和失控的不二法门，或者就是局部变量环绕，让你的 state 失去了同步。有很多框架试图解决这个问题，比如使用不可变的 state，但是这样以来又带来了新的问题，比如数据必须规格化，完整性约束失效等等。

MobX 让整个事情又变简单了：它不允许产生失控的 state。它的理念也很简单：所有可以从 state 中派生的事物，都会自动的派生。

把 MobX 想象成 Excel 表格。

首先，有一个 state，它可以是一个object，array，primitives等等任何组成你程序的部分。你可以把这个想象成你应用程序的“单元格”。
然后就是 derivations，一般它是指可以从 state 中直接计算的来的结果。比如未完成的任务的数量，这个比较简单，也可以稍复杂一些比如渲染你的任务显示的html。它类似于你的应用程序中的“公式和图表”。
Reactions 和 derivations 很像，主要的区别在于 reactions 并不产生数据结果，而是自动完成一些任务，一般是和 I/O 相关的。他们保证了 DOM 和 网络请求会自动适时地出发。
最后是 actions。Actions 指的是所有会改变 state 的事情，MobX 保证所有 actions 都会有对应的 derivations 和 reactions 相伴，保证同步。
一个简单的 todo 的 state

理论说的够多的了，看一个例子也许会更明白一些。我们从一个简单的 todo 程序开始。

下面是一个简单直接的 TodoStore，没有鱼丸，没有粗面，没有 MobX ……

JavaScript
```
class TodoStore {
	todos = [];

	get completedTodosCount() {
    	return this.todos.filter(
			todo => todo.completed === true
		).length;
    }

	report() {
		if (this.todos.length === 0)
			return "<none>";
		return `Next todo: "${this.todos[0].task}". ` +
			`Progress: ${this.completedTodosCount}/${this.todos.length}`;
	}

    addTodo(task) {
		this.todos.push({
			task: task,
			completed: false,
            assignee: null
		});
	}
}

const todoStore = new TodoStore();



```
我们创建了一个 todoStore，它拥有一个 todos 集合。现在我们往这个 todoStore 里添加一些东西，为了明显起见，我们每修改一个地方，就调用todoStore.report。

```
todoStore.addTodo("read MobX tutorial");
console.log(todoStore.report());
​
todoStore.addTodo("try MobX");
console.log(todoStore.report());
​
todoStore.todos[0].completed = true;
console.log(todoStore.report());
​
todoStore.todos[1].task = "try MobX in own project";
console.log(todoStore.report());
​
todoStore.todos[0].task = "grok MobX tutorial";
console.log(todoStore.report());
```
到现在为止，没有什么特别的。不过如果我们可以不再手动调用 report 方法，事情会不会更美好一些？我们只需要在想要的地方修改这个 state，所有的汇报都自动来做。

太巧了，这就是 MobX 能为你做的事情。自动执行只在 state 改变的时候触发，就好像 Excel 中的图表只在单元格数据改变时更新一样。为了达到这个目标，TodoStore 必须成为可观测的（observable）才行，让我们来改一些代码。

同时，completedTodosCount 属性应该被自动派生，使用 @observable 和 @computed 装饰器来做这些事情：

JavaScript
```
class ObservableTodoStore {
    @observable todos = [];
    @observable pendingRequests = 0;

    constructor() {
        mobx.autorun(() => console.log(this.report));
    }

    @computed get completedTodosCount() {
    	return this.todos.filter(
			todo => todo.completed === true
		).length;
    }

    @computed get report() {
        if (this.todos.length === 0)
            return "<none>";
	return `Next todo: "${this.todos[0].task}". ` +
	    `Progress: ${this.completedTodosCount}/${this.todos.length}`;
	}

    addTodo(task) {
	this.todos.push({
	    task: task,
	    completed: false,
	    assignee: null
	});
    }
}

const observableTodoStore = new ObservableTodoStore();

class ObservableTodoStore {
    @observable todos = [];
    @observable pendingRequests = 0;

    constructor() {
        mobx.autorun(() => console.log(this.report));
    }

    @computed get completedTodosCount() {
    	return this.todos.filter(
			todo => todo.completed === true
		).length;
    }

    @computed get report() {
        if (this.todos.length === 0)
            return "<none>";
	return `Next todo: "${this.todos[0].task}". ` +
	    `Progress: ${this.completedTodosCount}/${this.todos.length}`;
	}

    addTodo(task) {
	this.todos.push({
	    task: task,
	    completed: false,
	    assignee: null
	});
    }
}

const observableTodoStore = new ObservableTodoStore();
```
运行它，太棒了，我们每次赋值都能获得输出结果了。

有个 pendingRequests 暂时没用到，我们后面会用。另外这个教程都用了 ES6 的写法，不过 MobX 也支持 ES5 的写法。

在这个构造器中，我们使用autorun包裹了一个打出report的小函数。Autorun里的东西首先会运行一次，然后当其中的函数有observable的数据发生变化时，会再次运行。 这里我们使用了todos属性，每次todos变化了我们就打印出新的东西。

JavaScript
```
observableTodoStore.addTodo("read MobX tutorial");
observableTodoStore.addTodo("try MobX");
observableTodoStore.todos[0].completed = true;
observableTodoStore.todos[1].task = "try MobX in own project";
observableTodoStore.todos[0].task = "grok MobX tutorial";
```
（可以自己试试结果）

非常有趣是吧，report确实自己执行了，而且同步又精准。如果你仔细查看运行结果的话，你挥发性我们的第四句语句没有产生输出，因为我们修改了todos[1]的数据，而我们在report中指明的数据，并没有todos[1]的变化而发生变化。而第五句话修改了todos[0]的数据则输出了。这个例子很好的说明了，autorun不是简单的监视了todos，而是精确到了具体的一项。

让React更美好

好了，到目前未知，我们使report自动化了，是实话把react拉出来遛遛了。为了是的react 的组件可以识别mobx，我们需要使用mobx-react包来完成，使用autorun，自动的让组件和state同步，这个简直就和上面的让report自动输出一样简单。

下面是一个react 组件，唯一MobX出场的地方就是一个@observer修饰符，这已经足够了，你再也不用使用setState了，你也不需要指明这个组件需要关注state的哪个部分，也不许手动写什么高阶组件。一般来说，所有的部件都变成人工智能了，即使他被定义成一个木偶（纯展示）组件。

JavaScript
```
@observer
class TodoList extends React.Component {
  render() {
    const store = this.props.store;
    return (
      <div>
        { store.report }
        <ul>
          { store.todos.map(
            (todo, idx) => <TodoView todo={ todo } key={ idx } />
          ) }
        </ul>
        { store.pendingRequests > 0 ? <marquee>Loading...</marquee> : null }
        <button onClick={ this.onNewTodo }>New Todo</button>
        <small> (double-click a todo to edit)</small>
      <RenderCounter />
    </div>
    );
  }
​
  onNewTodo = () => {
    this.props.store.addTodo(prompt('Enter a new todo:','coffee plz'));
  }
}
​
@observer
class TodoView extends React.Component {
  render() {
    const todo = this.props.todo;
    return (
      <li onDoubleClick={ this.onRename }>
        <input
          type='checkbox'
          checked={ todo.completed }
          onChange={ this.onToggleCompleted }
        />
        { todo.task }
        { todo.assignee
          ? <small>{ todo.assignee.name }</small>
          : null
        }
      <RenderCounter />
    </li>
    );
  }
​
  onToggleCompleted = () => {
    const todo = this.props.todo;
    todo.completed = !todo.completed;
  }
​
  onRename = () => {
    const todo = this.props.todo;
    todo.task = prompt('Task name', todo.task) || todo.task;
  }
}
​
ReactDOM.render(
  <TodoList store={ observableTodoStore } />,
  document.getElementById('reactjs-app')
);
```

执行下面的语句，我们会发现MobX帮我们把数据的更改反应到界面上去了。

JavaScript
```
store.todos[0].completed = !store.todos[0].completed;
store.todos[1].task = "Random todo " + Math.random();
store.todos.push({ task: "Find a fine cheese", completed: true });
// etc etc.. add your own statements here...
```
使用引用（References)

到现在位置，我们已经使用 observable 创建了个数据类型了。也许你会想，MobX 能不能应付引用呢？在之前的例子里，你可能主意到了又一个 assignee 的属性，我们就在这里放另外的一个 store，然后把它赋值给 tasks。

JavaScript
```
var peopleStore = mobx.observable([
  { name: "Michel" },
  { name: "Me" }
]);
observableTodoStore.todos[0].assignee = peopleStore[0];
observableTodoStore.todos[1].assignee = peopleStore[1];
peopleStore[0].name = "Michel Weststrate";
```
毫无疑问的，MobX 把着一切打理的井井有条。使用 MobX ，不需要规格话数据，不需要指明控件，事实上你的数据在哪里都无所谓。只要 observale 了，什么都好了。





总结

好了，仅仅依靠一些简单的修饰器，我们就让 react 程序如此生动有趣。最后总结一些：

@observale 修饰器或者 observable 函数让对象可以被追踪；
@computed 修饰器创造了自动运算的表达式；
autorun 函数让依靠 observable 的函数自动执行，这个用来写 log，发请求很不错；
@observer 修饰器让 React 组建自动起来，它会自动更新，即便是在一个很大的程序里也会工作的很好；

最后，MobX 不是一个状态容器

很多人把 MobX 当作另外一个 Redux，但是它仅仅是一个库，不是一个什么架构。上面的例子还是需要程序员自己去组织逻辑和store或者控制器什么的。
