# API Reference for combineActions

- [API Reference for combineActions](#api-reference-for-combineactions)
  - [Methods](#methods)
    - [combineActions](#combineactions)
      - [`combineActions(...types)` {#combineactionstypes}](#combineactionstypes-combineactionstypes)
          - [EXAMPLE](#example)

## Methods

### combineActions

```js
combineActions(...types);
```

Combine any number of action types or action creators. `types` is a list of positional arguments which can be action type strings, symbols, or action creators.

组合任意数量的 `action` 类型或 `action` 创建函数。 `types` 是位置参数的列表， `action` 类型可以是字符串、symbol 或函数。

```js
import { combineActions } from 'redux-actions';
```

#### `combineActions(...types)` {#combineactionstypes}

This allows you to reduce multiple distinct actions with the same reducer.

这使你可以使用同一 `reducer` 函数处理多个不同的 `action`。

```js
const { increment, decrement } = createActions({
  INCREMENT: amount => ({ amount }),
  DECREMENT: amount => ({ amount: -amount }),
})

const reducer = handleAction(combineActions(increment, decrement), {
  next: (state, { payload: { amount } }) => ({ ...state, counter: state.counter + amount }),
  throw: state => ({ ...state, counter: 0 }),
}, { counter: 10 })

expect(reducer(undefined, increment(1)).to.deep.equal({ counter: 11 })
expect(reducer(undefined, decrement(1)).to.deep.equal({ counter: 9 })
// 当 action.error 为 true 时，触发 throw 函数
expect(reducer(undefined, increment(new Error)).to.deep.equal({ counter: 0 })
expect(reducer(undefined, decrement(new Error)).to.deep.equal({ counter: 0 })
```

Below is how you would use `combineActions` and `handleActions` together

以下是如何一起使用 `combineActions` 和 `handleActions` 的例子

###### EXAMPLE

例子

```js
const { increment, decrement } = createActions({
  INCREMENT: amount => ({ amount }),
  DECREMENT: amount => ({ amount: -amount })
});

const reducer = handleActions(
  {
    [combineActions(increment, decrement)]: (
      state,
      { payload: { amount } }
    ) => {
      return { ...state, counter: state.counter + amount };
    }
  },
  { counter: 10 }
);

expect(reducer({ counter: 5 }, increment(5))).to.deep.equal({ counter: 10 });
expect(reducer({ counter: 5 }, decrement(5))).to.deep.equal({ counter: 0 });
expect(reducer({ counter: 5 }, { type: 'NOT_TYPE', payload: 1000 })).to.equal({
  counter: 5
});
expect(reducer(undefined, increment(5))).to.deep.equal({ counter: 15 });
```
