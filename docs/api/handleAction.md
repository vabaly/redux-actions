# API Reference for handleAction(s)

- [API Reference for handleAction(s)](#api-reference-for-handleactions)
  - [Methods](#methods)
    - [handleAction](#handleaction)
      - [`handleAction(type, reducer, defaultState)`{#handleactiontype-reducer-defaultstate}](#handleactiontype-reducer-defaultstatehandleactiontype-reducer-defaultstate)
          - [EXAMPLE](#example)
      - [`handleAction(type, reducerMap, defaultState)`{#handleactiontype-reducermap-defaultstate}](#handleactiontype-reducermap-defaultstatehandleactiontype-reducermap-defaultstate)
          - [EXAMPLE](#example-1)
    - [handleActions](#handleactions)
      - [`handleActions(reducerMap, defaultState[, options])` {#handleactionsreducermap-defaultstate}](#handleactionsreducermap-defaultstate-options-handleactionsreducermap-defaultstate)
          - [EXAMPLE](#example-2)
      - [`handleActions(actionMap[, defaultState], options)`](#handleactionsactionmap-defaultstate-options)
          - [EXAMPLE](#example-3)

## Methods

### handleAction

```js
handleAction(
  type,
  reducer | reducerMap = Identity,
  defaultState,
)
```

Wraps a reducer so that it only handles [Flux Standard Actions](https://github.com/redux-utilities/flux-standard-action#flux-standard-action) of a certain type.

返回一个 `reducer`，其仅能处理指定类型的符合 [Flux Standard Actions](https://github.com/redux-utilities/flux-standard-action#flux-standard-action) 的 `action`。

```js
import { handleAction } from 'redux-actions';
```

#### `handleAction(type, reducer, defaultState)`{#handleactiontype-reducer-defaultstate}

If a `reducer` function is passed, it is used to handle both normal actions and failed actions. (A failed action is analogous to a rejected promise.) You can use this form if you know a certain type of action will never fail, like the increment example above.

如果传递了一个 `reducer` 函数（这个 `reducer` 指的是作为第二个参数的那个函数），它将用于处理正常的 `action` 和失败的 `action`（失败的 `action` 类似于被拒绝的 `promise`）。如果您知道某种类型的 `action` 将永远不会失败，则可以使用 `this form`，例如之前的增量示例。

If the reducer argument (`reducer`) is `undefined`, then the identity function is used.

如果 `reducer` 参数是 `undefined`，则使用内置函数，即透传 `state`，不做任何改变。

The third parameter `defaultState` is required, and is used when `undefined` is passed to the reducer.

第三个参数 `defaultState` 是必需的，特别是在 `reducer` 是 `undefined` 的时候也会使用这个值。

###### EXAMPLE

例子

```js
handleAction(
  'APP/COUNTER/INCREMENT',
  (state, action) => ({
    counter: state.counter + action.payload.amount
  }),
  defaultState
);
```

#### `handleAction(type, reducerMap, defaultState)`{#handleactiontype-reducermap-defaultstate}

Otherwise, you can specify separate reducers for `next()` and `throw()` using the `reducerMap` form. This API is inspired by the ES6 generator interface.

除此之外，您还可以使用 `reducerMap` 的形式分别为 `next()` 和 `throw()` 指定 `reducer` 函数。 该 API 受 ES6 生成器的启发。

If the reducer argument (`reducerMap`) is `undefined`, then the identity function is used.

如果 `reducerMap` 参数是 `undefined`，则使用默认函数，即透传老的状态。

###### EXAMPLE

```js
handleAction('FETCH_DATA', {
  next(state, action) {...},
  throw(state, action) {...},
}, defaultState);
```

If either `next()` or `throw()` are `undefined` or `null`, then the identity function is used for that reducer.

如果 `next()` 或 `throw()` 是 `undefined` 或 `null`，则使用默认函数，即透传老的状态。

### handleActions

```js
handleActions(reducerMap, defaultState);
```

Creates multiple reducers using `handleAction()` and combines them into a single reducer that handles multiple actions. Accepts a map where the keys are passed as the first parameter to `handleAction()` (the action type), and the values are passed as the second parameter (either a reducer or reducer map). The map must not be empty.

这个函数本质上是使用 `handleAction()` 创建多个 `reducer`，并将它们合并为一个处理多个 `action` 的 `reducer`。这个函数接受一个对象，在该对象中，键作为第一个参数传递给`handleAction()`（`action` `type` 或 `action` 创建函数），而值作为 `handleAction` 的第二个参数（所以值可以是 `reducer`，也可以是 `reducerMap`）。`handleActions()` 的参数 `reducerMap` 不能为空。

If `reducerMap` has a recursive structure, its leaves are used as reducers, and the action type for each leaf is the path to that leaf. If a node's only children are `next()` and `throw()`, the node will be treated as a reducer. If the leaf is `undefined` or `null`, the identity function is used as the reducer. Otherwise, the leaf should be the reducer function.

如果 `reducerMap` 具有递归结构，即内嵌 `reducerMap`，则将其叶子节点的值用作 `reducer`，每个叶子的 `action` 类型是该叶子的路径拼接。如果一个节点的子节点仅仅是 `next()` 和 `throw()`，则该节点将被视为 `reducerMap`。如果叶子节点是 `undefined` 或 `null`，则将默认函数作为 `reducer`，即透传原来的 `state`。除此之外，叶子应该是 `reducer` 函数。

```js
import { handleActions } from 'redux-actions';
```

#### `handleActions(reducerMap, defaultState[, options])` {#handleactionsreducermap-defaultstate}

The second parameter `defaultState` is required, and is used when `undefined` is passed to the reducer.

第二个参数 `defaultState` 是必须的，如果 `reducer` 函数是 `undefined`，则使用默认状态，即透传老状态。

(Internally, `handleActions()` works by applying multiple reducers in sequence using [reduce-reducers](https://github.com/redux-utilities/reduce-reducers).)

（在内部，`handleActions()` 通过使用[reduce-reducers](https://github.com/redux-utilities/reduce-reducers) 依次应用多个 `reducer` 来工作。

###### EXAMPLE

例子

```js
const reducer = handleActions(
  {
    INCREMENT: (state, action) => ({
      counter: state.counter + action.payload
    }),

    DECREMENT: (state, action) => ({
      counter: state.counter - action.payload
    })
  },
  { counter: 0 }
);
```

Or using a JavaScript `Map` type:

或者使用 `JavaScript` 的 `Map` 类型：

```js
const reducer = handleActions(
  new Map([
    [
      INCREMENT,
      (state, action) => ({
        counter: state.counter + action.payload
      })
    ],

    [
      DECREMENT,
      (state, action) => ({
        counter: state.counter - action.payload
      })
    ]
  ]),
  { counter: 0 }
);
```

You can also use an action function as the key to a reduce function instead of using a string const:

你还可以使用 `action` 函数作为 `reducer` 函数的键，而不是使用字符串常量：

```js
const increment = createAction(INCREMENT);
const decrement = createAction(DECREMENT);

const reducer = handleActions(
  new Map([
    [
      increment,
      (state, action) => ({
        counter: state.counter + action.payload
      })
    ],

    [
      decrement,
      (state, action) => ({
        counter: state.counter - action.payload
      })
    ]
  ]),
  { counter: 0 }
);
```

#### `handleActions(actionMap[, defaultState], options)`

You can prefix each action type by passing a configuration object as the last argument of `handleActions`.

你可以通过将配置对象作为 `handleActions` 的最后一个参数来传递每个 `action` 类型的前缀。正好对应上 `createActions` 的这个功能。

###### EXAMPLE

```js
const options = {
  prefix: 'counter', // String used to prefix each type
  namespace: '--' // Separator between prefix and type.  Default: `/`
}

createActions({ ... }, 'INCREMENT', options)

handleActions({ ... }, defaultState, options)
```
