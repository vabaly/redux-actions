# API Reference for createAction(s)

- [API Reference for createAction(s)](#api-reference-for-createactions)
  - [Methods](#methods)
    - [createAction](#createaction)
      - [`createAction(type)` {#createactiontype}](#createactiontype-createactiontype)
          - [EXAMPLE](#example)
          - [EXAMPLE](#example-1)
          - [EXAMPLE](#example-2)
          - [EXAMPLE](#example-3)
      - [`createAction(type, payloadCreator)` {#createactiontype-payloadcreator}](#createactiontype-payloadcreator-createactiontype-payloadcreator)
          - [EXAMPLE](#example-4)
      - [`createAction(type, payloadCreator, metaCreator)` {#createactiontype-payloadcreator-metacreator}](#createactiontype-payloadcreator-metacreator-createactiontype-payloadcreator-metacreator)
          - [EXAMPLE](#example-5)
    - [createActions](#createactions)
      - [`createActions(actionMap[, options])` {#createactionsactionmap}](#createactionsactionmap-options-createactionsactionmap)
          - [EXAMPLE](#example-6)
          - [EXAMPLE](#example-7)
      - [`createActions(actionMap, ...identityActions[, options])`{#createactionsactionmap-identityactions}](#createactionsactionmap-identityactions-optionscreateactionsactionmap-identityactions)
      - [`createActions(actionMap[, ...identityActions], options)`](#createactionsactionmap-identityactions-options)
          - [EXAMPLE](#example-8)

## Methods

方法

### createAction

```js
createAction(
  type,
  payloadCreator = Identity,
  ?metaCreator
)
```

Wraps an action creator so that its return value is the payload of a Flux Standard Action.

构建一个 `action` 创建函数并将其返回，创建函数的返回值是 Flux 标准 `action`。

```js
import { createAction } from 'redux-actions';
```

**NOTE:** The more correct name for this function is probably `createActionCreator()`, but that seems a bit redundant.

**备注：**这个函数更准确的名字应该是 `createActionCreator()`，但是这样就显得有点冗长了。

#### `createAction(type)` {#createactiontype}

Calling `createAction` with a `type` will return an action creator for dispatching actions. `type` must implement `toString` and is the only required parameter for `createAction`.

调用 `createAction` 时传入一个 `type` 字符串将返回一个 `action` 创建函数，用于 `dispatch` 时执行。 `type` 是个字符串，并且是 `createAction` 函数唯一必须的参数。

###### EXAMPLE

例子

```js
export const increment = createAction('INCREMENT');
export const decrement = createAction('DECREMENT');

// `action` 创建函数的参数是 payload 的值
increment(); // { type: 'INCREMENT' }
decrement(); // { type: 'DECREMENT' }
increment(10); // { type: 'INCREMENT', payload: 10 }
decrement([1, 42]); // { type: 'DECREMENT', payload: [1, 42] }
```

If the payload is an instance of an [Error object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error),
`redux-actions` will automatically set `action.error` to true.

如果 `payload` 是 [Error object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) 的实例，那么 `redux-actions` 将自动将 `action.error` 设为 `true`。

###### EXAMPLE

示例

```js
const noop = createAction('NOOP');

const error = new TypeError('not a number');
expect(noop(error)).to.deep.equal({
  type: 'NOOP',
  payload: error,
  error: true
});
```

`createAction` also returns its `type` when used as type in `handleAction` or `handleActions`.

当在 `handleAction` 或 `handleActions`中作为参数时，`createAction` 还将在内部返回它的 `type` 值。

###### EXAMPLE

例子

```js
const noop = createAction('INCREMENT');

// As parameter in handleAction:
// noop 作为参数，在内部是用 `INCREMENT` 进行辨别的
handleAction(noop, {
  next(state, action) {...},
  throw(state, action) {...}
});

// As object key in handleActions:
const reducer = handleActions({
  [noop]: (state, action) => ({
    counter: state.counter + action.payload
  })
}, { counter: 0 });
```

Use the identity form to create one-off actions.

创建 `action` 创建函数并立即调用，这种称为一次性的 `action`。

###### EXAMPLE

例子

```js
// 没有变量来承载 `action` 创建函数，就没法复用了
createAction('ADD_TODO')('Use Redux');
```

#### `createAction(type, payloadCreator)` {#createactiontype-payloadcreator}

`payloadCreator` must be a function, `undefined`, or `null`. If `payloadCreator` is `undefined` or `null`, the identity function is used.

`payloadCreator` 必须是 `function`、`undefined` 或 `null` 这几种类型。如果是后两者，则使用内置的函数，即透传 `payload`。

**NOTE:** If payload is an instance of an [Error object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error),
`payloadCreator` will not be called.

**备注:** 如果 `payload` 是一个 [Error object](https://developer.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/Error) 实例，
`payloadCreator` 将不会被调用。

###### EXAMPLE

例子

```js
let noop = createAction('NOOP', amount => amount);
// same as
noop = createAction('NOOP');

expect(noop(42)).to.deep.equal({
  type: 'NOOP',
  payload: 42
});
```

#### `createAction(type, payloadCreator, metaCreator)` {#createactiontype-payloadcreator-metacreator}

`metaCreator` is an optional function that creates metadata for the payload. It receives the same arguments as the payload creator, but its result becomes the meta field of the resulting action. If `metaCreator` is undefined or not a function, the meta field is omitted.

`metaCreator` 是一个可选参数，可为 `payload` 创建元数据。它收到与 `payload` 创建函数相同的参数，但其返回值将作为最终 `action` 的 `meta` 字段（FSA 标准中的 `meta` 字段）。如果`metaCreator` 是 `undefined` 或者不是 `function`，则忽略 `meta` 字段。

###### EXAMPLE

例子

```js
const updateAdminUser = createAction(
  'UPDATE_ADMIN_USER',
  updates => updates,
  () => ({ admin: true })
);

updateAdminUser({ name: 'Foo' });
// {
//   type: 'UPDATE_ADMIN_USER',
//   payload: { name: 'Foo' },
//   // 元数据 
//   meta: { admin: true },
// }
```

### createActions

```js
createActions(
  actionMap,
  ?...identityActions,
  ?options
)
```

Returns an object mapping action types to action creators. The keys of this object are camel-cased from the keys in `actionMap` and the string literals of `identityActions`; the values are the action creators.

返回一个键是 `action` 类型，值是 `action` 创建函数的对象。该对象的键由 `actionMap` 对象中的键所对应的的字符串的值的**驼峰式封装**。

```js
import { createActions } from 'redux-actions';
```

#### `createActions(actionMap[, options])` {#createactionsactionmap}

`actionMap` is an object which can optionally have a recursive data structure, with action types as keys, and whose values **must** be either

`actionMap` 是一个对象，这个对象可以内嵌 `actionMap` 对象，形成递归结构。此对象以 `action` 类型为键，其值**必须**为以下一种：

- a function, which is the payload creator for that action
- an array with `payload` and `meta` functions in that order, as in [`createAction`](#createaction)
  - `meta` is **required** in this case \(otherwise use the function form above\)
- an `actionMap`

- 一个 `payload` 创建函数
- 一个由 `payload` 创建函数和 `meta` 创建函数的数组
  - 当使用数组时，`meta` 创建函数必须存在，否则请用第一种函数形式
- 一个 `actionMap` 对象，是的，`actionMap` 对象可以嵌套一个 `actionMap` 对象

###### EXAMPLE

```js
createActions({
  // 注意哦，属性本身就是字符串
  ADD_TODO: todo => ({ todo }), // payload creator
  REMOVE_TODO: [
    todo => ({ todo }), // payload creator
    (todo, warn) => ({ todo, warn }) // meta
  ]
});
```

If `actionMap` has a recursive structure, its leaves are used as payload and meta creators, and the action type for each leaf is the combined path to that leaf:

如果 `actionMap` 具有递归结构，则将其叶子节点用作 `payload` 和 `meta` 的创建函数，每个叶子的 `action` 类型是该叶子路径的结合：

###### EXAMPLE

例子

```js
// 此时返回的对象，结构与 actionMap 一致
// 只是每个属性被成了驼峰式命名
const actionCreators = createActions({
  APP: {
    COUNTER: {
      INCREMENT: [amount => ({ amount }), amount => ({ key: 'value', amount })],
      DECREMENT: amount => ({ amount: -amount }),
      SET: undefined // given undefined, the identity function will be used
    },
    NOTIFY: [
      (username, message) => ({ message: `${username}: ${message}` }),
      (username, message) => ({ username, message })
    ]
  }
});

// 创建函数用 . 符号来访问
expect(actionCreators.app.counter.increment(1)).to.deep.equal({
  // type 变成了访问路径用 `/` `join` 起来的字符串
  type: 'APP/COUNTER/INCREMENT',
  payload: { amount: 1 },
  meta: { key: 'value', amount: 1 }
});
expect(actionCreators.app.counter.decrement(1)).to.deep.equal({
  type: 'APP/COUNTER/DECREMENT',
  payload: { amount: -1 }
});
expect(actionCreators.app.counter.set(100)).to.deep.equal({
  type: 'APP/COUNTER/SET',
  payload: 100
});
expect(
  actionCreators.app.notify('yangmillstheory', 'Hello World')
).to.deep.equal({
  type: 'APP/NOTIFY',
  payload: { message: 'yangmillstheory: Hello World' },
  meta: { username: 'yangmillstheory', message: 'Hello World' }
});
```

#### `createActions(actionMap, ...identityActions[, options])`{#createactionsactionmap-identityactions}

`identityActions` is an optional list of positional string arguments that are action type strings; these action types will use the identity payload creator.

`identityActions` 是后面一大串字符串参数的数组形式，这些位置字符串参数是 `action` 类型。这些 `action` 类型将使用内置的 `payload` 透传的创建函数。

```js
const { actionOne, actionTwo, actionThree } = createActions(
  {
    // function form; payload creator defined inline
    ACTION_ONE: (key, value) => ({ [key]: value }),

    // array form
    ACTION_TWO: [
      first => [first], // payload
      (first, second) => ({ second }) // meta
    ]

    // trailing action type string form; payload creator is the identity
  },
  // 这个会透传 payload，相当于
  // createActions({}) 和 createActions('') 的结合
  'ACTION_THREE'
);

expect(actionOne('key', 1)).to.deep.equal({
  type: 'ACTION_ONE',
  payload: { key: 1 }
});

expect(actionTwo('first', 'second')).to.deep.equal({
  type: 'ACTION_TWO',
  payload: ['first'],
  meta: { second: 'second' }
});

expect(actionThree(3)).to.deep.equal({
  type: 'ACTION_THREE',
  payload: 3
});
```

#### `createActions(actionMap[, ...identityActions], options)`

You can prefix each action type by passing a configuration object as the last argument of `createActions`.

您可以通过将配置对象作为 `createActions` 的最后一个参数来传递每个 `action` 类型的前缀。

注意哦，除了第一个参数和最后一个不是 `string` 类型参数，其他中间的参数都是 `identityActions` 的一员。

###### EXAMPLE

例子

```js
createActions({ ... }, 'INCREMENT', {
  prefix: 'counter', // String used to prefix each type
  namespace: '--' // Separator between prefix and type.  Default: `/`
})
```

`'INCREMENT'` in this example will be prefixed as `counter--INCREMENT`.

在这个例子中，`INCREMENT` 的 `action` 类型变成了 `counter--INCREMENT`。
