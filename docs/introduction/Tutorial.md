## Tutorial

### Installation

For NPM users

```bash
$ npm install --save redux-actions
```

For Yarn users

```bash
$ yarn add redux-actions
```

For UMD users

The [UMD](https://unpkg.com/redux-actions@latest/dist) build exports a global called `window.ReduxActions` if you add it to your page via a `<script>` tag. We _don’t_ recommend UMD builds for any serious application, as most of the libraries complementary to Redux are only available on [npm](https://www.npmjs.com/search?q=redux).

### Vanilla Counter

We are going to be building a simple counter, I recommend using something like [jsfiddle](https://jsfiddle.net/) or [codepen](https://codepen.io/pen/) or [codesandbox](https://codesandbox.io) if you would like to follow along, that way you do not need a complicated setup to grasp the basics of `redux-actions`.

我们将要建立一个简单的计数器，我建议使用类似[jsfiddle](https://jsfiddle.net/) 或[codepen](https://codepen.io/pen/) 或[codesandbox](https://codesandbox.io) ，这样一来，您就无需复杂的设置即可掌握`redux-actions'的基础知识。

To begin we are going to need some scaffolding so here is some HTML to get started with. You may need to create a new file called main.js depending on where you are trying to set this tutorial up.

首先，我们需要一些样板代码，包括下面这段 HTML 代码，以及一个 `main.js` 文件。

```html
<!doctype html>
<html>
  <head>
    <meta charset="utf-8"/>
    <script src="https://unpkg.com/redux@latest/dist/redux.js"></script>
    <script src="https://unpkg.com/redux-actions@latest/dist/redux-actions.js"></script>
  </head>
  <body>
    <button id="increment">INCREMENT</button>
    <button id="decrement">DECREMENT</button>
    <div id="content" />
    <script src="main.js"></script>
  </body>
</html>
```

Now that we are ready to write some JS let us create our default state for our counter.

现在我们准备编写一些 `JS` 代码，首先得为计数器的状态创建默认值。

```js
// 默认计数为 0
const defaultState = { counter: 0 };
```

Now if we want to see our default state we need to render it.
Lets get a reference to our main content and render our default state into the html.

现在，如果要显示默认状态，则需要渲染它。
让我们获取 `content` DOM，并将默认状态赋值给它的 `innerHTML`。

```js
const content = document.getElementById('content');

const render = () => {
  content.innerHTML = defaultState.counter;
};
render();
```

With our default state and renderer in place we can start to use our libraries. `redux` and `redux-actions` can be found via the globals `window.Redux` and `window.ReduxActions`. Okay enough setup lets start to make something with `redux`!

有了默认状态和渲染器后，我们就可以开始使用我们的库了。可以通过全局变量 `window.Redux` 和`window.ReduxActions` 来使用 `redux` 和 `redux-actions`。好的设置足以让我们开始使用`redux` 做点什么！

We are going to want a store for our defaultState. We can create one from `redux` using `createStore`.

我们可以使用 `redux` 的 `createStore` 创建一个 “数据库” 来存储状态。

```js
const { createStore } = window.Redux;
```

We are going to want to create our first action and handle that action.

我们将要创建我们的第一个 `action` 并处理它。

```js
const { createAction, handleAction } = window.ReduxActions;
```

Next lets create our first action, 'increment', using `createAction`.

接下来，使用 `createAction` 创建第一个 `action` `increment`。

```js
const increment = createAction('INCREMENT');
```

Next we are going to handle that action with `handleAction`. We can provide it our `increment` action to let it know which action to handle, a method to handle our state transformation, and the default state.

接下来，我们将使用 `handleAction` 处理这个 `action`。我们可以向其提供“增量”操作，以使其知道要处理的操作，处理状态转换的方法以及默认状态。

```js
// action 对应的 reducer，本质上也就是处理 action 的具体操作，所以对我来说 handleAction 比 reducer 更加富有语义一些，reducer 本质上是个函数
const reducer = handleAction(
  // 要处理的 action，也相当于监听一个 action
  increment,
  // 此 action 触发的同步修改
  (state, action) => ({
    ...state,
    counter: state.counter + 1
  }),
  // 默认的 state
  defaultState
);
```

`handleAction` produced a reducer for our `redux` store. Now that we have a reducer we can create a store.

`handleAction` 为 `redux store` 提供了一个 `reducer`。于是就可以创建一个 `store` 了。

```js
const store = createStore(reducer, defaultState);
```

Now that we have a `store`, we can rewrite our `render` method to use it instead of the `defaultState`. We also want to `subscribe` our `render` to any changes the `store` might have for us.

现在我们有了一个 `store`，我们可以重写我们的 `render` 方法来使用它而不是只渲染个 `defaultState`。我们还想 `subscribe` `render` 函数，以便 `store` 发生任何更改都调用它。

```js
const render = () => {
  content.innerHTML = store.getState().counter;
};

// store.subscribe 的参数是一个渲染函数，如果 `store` 的 `state` 发生任何更改，都会调用着个渲染函数。
store.subscribe(render);
```

We are ready to `dispatch` an action. Lets create an event listener for our increment button that will dispatch our `increment` action creator when clicked.

我们准备 `dispatch` 一个 `action`。让我们为增加按钮创建一个事件侦听器，该事件监听器在单击时将触发我们的 `INCREMENT` `action`。这个 `action` 由 `increment` 函数创建。

```js
document.getElementById('increment').addEventListener('click', () => {
  store.dispatch(increment());
});
```

If you try to click the increment button you should see the value is now going up by one on each click.

如果你尝试单击增量按钮，则应该看到该值现在每次点击都增加一。

We have one button working, so why don't we try to get the second one working by creating a new action for decrement.

我们有一个按钮在工作，所以为什么不尝试通过创建新的减量动作来使第二个按钮工作。

```js
const decrement = createAction('DECREMENT');
```

Instead of using `handleAction` like we did for `increment`, we can replace it with our other tool `handleActions` which will let us handle both `increment` and `decrement` actions.

不用像我们只对 `increment` 所做的那样使用 `handleAction` 来做处理，我们可以使用 `handleActions`，它可以让我们同时处理 `increment` 和 `decrement` 两个 `action`。

```js
const { createAction, handleActions } = window.ReduxActions;

const reducer = handleActions(
  // 这里传一个对象哦
  {
    // 用属性表明处理的 action，用
    [increment]: state => ({ ...state, counter: state.counter + 1 }),
    [decrement]: state => ({ ...state, counter: state.counter - 1 })
  },
  defaultState
);
```

Now when we add a handler for dispatching our `decrement` action we can see both `increment` and `decrement` buttons now function appropriately.

现在，当我们添加一个处理程序来分派我们的 `decrement` 动作时，我们可以看到 `increment` 和 `decrement` 按钮现在都可以正常工作了。

```js
document.getElementById('decrement').addEventListener('click', () => {
  store.dispatch(decrement());
});
```

You might be thinking at this point we are all done. We have both buttons hooked up, and we can call it a day. Yet we have much optimizing to do. `redux-actions` has other tools we have not yet taken advantage of. So lets investigate how we can change the code to use the remaining tools and make the code less verbose.

你可能会认为此时我们已经完成了。我们拥有两个按钮，我们可以玩一天。但是，我们还有很多优化可以做。 `redux-actions` 还有其他我们尚未利用的工具。因此，让我们研究一下如何更改代码以使用其余工具，并使代码不那么冗长。

We have declarations for both `increment` and `decrement` action creators. We can modify these lines from using `createAction` to using `createActions` like so.

我们可以同时声明 `increment` 和 `decrement` 两个 `action` 创建函数。我们可以将这些代码从使用 `createAction` 修改为使用 `createActions`。

```js
const { createActions, handleActions } = window.ReduxActions;

// 返回一个对象
const { increment, decrement } = 
// 接收多个常量参数
createActions('INCREMENT', 'DECREMENT');
```

We can still do better though. What if we want an action like `'INCREMENT_FIVE'`? We would want to be able to create variations of our existing actions easily. We can abstract our logic in the reducer to our actions, making new permutations of existing actions easy to create.

我们仍然可以做得更好。如果我们要执行类似 `INCREMENT_FIVE` 的 `action` 怎么办？我们希望能够轻松地创建我们现有 `action` 的变体。我们可以将 `reducer` 中的逻辑抽象为我们的动作，从而使现有动作的新组合易于创建。

```js
const { increment, decrement } = 
// createActions 可以传一个对象，键代表 `action` 类型，值代表接收参数和返回 payload 的逻辑处理
createActions({
  INCREMENT: (amount = 1) => ({ amount }),
  DECREMENT: (amount = 1) => ({ amount: -amount })
});

const reducer = handleActions(
  {
    [increment]: (state, { payload: { amount } }) => {
      return { ...state, counter: state.counter + amount };
    },
    [decrement]: (state, { payload: { amount } }) => {
      return { ...state, counter: state.counter + amount };
    }
  },
  defaultState
);
```

Now that we have moved our logic, our `reducers` are looking identical. If only we could combine them somehow. Well we can! `combineActions` can be used to reduce multiple distinct actions with the same reducer.

既然我们已经改变了逻辑，那么我们的 `reducer` 看起来就一样。如果我们能以某种方式将它们结合起来。好吧，我们可以！可以使用`combineActions` 来减少具有相同多个不同 `action` 的处理。

```js
const { createActions, handleActions, combineActions } = window.ReduxActions;

const reducer = handleActions(
  {
    [combineActions(increment, decrement)]: (
      state,
      { payload: { amount } }
    ) => {
      return { ...state, counter: state.counter + amount };
    }
  },
  defaultState
);
```

We have finally used all of the tools that `redux-actions` has to offer. Concluding our [vanilla tutorial](https://www.webpackbin.com/bins/-KntJIfbsxVzsD98UEWF). This doesn't mean you don't have more to learn though. Much more can be accomplished using these tools in many ways, just head on over to the [API Reference](../api) to begin exploring what else `redux-actions` can do for you.

我们终于使用了 `redux-actions` 必须提供的所有工具。结束我们的[初级教程](https://www.webpackbin.com/bins/-KntJIfbsxVzsD98UEWF)。但这并不意味着你没有更多的知识要学习。使用这些工具可以通过多种方式完成更多工作，只需转到[API参考](../api)，即可开始探索 `redux-actions` 还能为你做什么。
