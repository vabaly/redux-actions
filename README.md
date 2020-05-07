# Redux Actions 学习笔记

名词：

- `action creator`，也称 `action` 创建函数
- 

## Redux Actions 解决什么问题

根本是解决写 `Redux` 代码非常啰嗦的问题。

1. 创建 `Action` 变得简单
2. `Action` 本身符合 `FSA` 规范
3. 处理 `Action` 的代码也变得简单
4. 不用总写 `type`、`switch...case` 等重复的代码。
5. 不用再写一堆常量了，直接字符串一次性传给 `createAction` 就可以了

## Redux Actions 使用方式

### 创建 `Action`

首先，我们要明白，一个 `Action` 的本质是一个对象，类似于 `{ type, payload }` 这样一个对象。以前我们创建它会先定义一个创建函数，然后在 `dispatch` 时，执行这个函数，并把函数返回值作为 `dispatch` 的参数。同理，`creatAction` 也是返回一个创建函数，所以在 `dispatch` 时，也要先执行这个返回的函数，这一点完全一样。另一个所有约束但也基本相同的点是，创建函数的参数是 `payload` 的值。

这里仿佛没有减少多少代码，不过还是多多少少减少了些，而且遵循 `FSA` 规范：

1. 不用写 `type` 了，直接在 `createAction` 中传个参数就是 `type`
2. 不用写参数，然后再 `return` 一个对象了，直接约定俗成返回一个对象，参数在调用创建函数时传递也会默认被接收。

使用 `createActions` 时，返回的对象的键是类型的驼峰式命名。

仅当使用 `createActions` 时，可以配置每个 `action` `type` 的前缀，以及类型和前缀的连接符。

### `Action` 对应的对 `state` 的处理

与以前的写法不同，`handleAction` 或者 `handleActions` 不再根据 `type` 来识别 `action`，而是直接根据 `action` 创建函数来识别，所以你不再对 `reducer` 所在的地方传一个常量，又对 `dispatch` 所在的地方传一个 `action` 创建函数，而是统一传 `action` 创建函数，这是第一个优点。

第二个优点是，`handleAction` 使用位置参数解耦 `action` 的辨别和逻辑， `handleActions` 使用 `key` `value` 来接耦这两者，逻辑部分都抽象成一个独立的函数，可以自由的在任何地方定义这段逻辑，而不是一堆 `switch...case`。

多个 `action` 如果具有相同修改 `state` 的逻辑，可以用 `combineActions(action1, action2, ...)` 作为 `key`，类似于多行在一起的 `case`。

## 从 Redux Actions 设计总结的规范

- 调用 `action` 创建函数时，传入数据（`payload`）的处理在创建函数内部进行，即 `payloadCreator` 中进行，而不是到 `reducer` 后再处理，`reducer` 主要负责对 `state` 的修改，仅仅是一个数据的赋值过程。

## Redux Actions 和 Redux Saga 的区别

- `Redux Actions` 处理的（也可以理解为监听的） `action` 都是要同步修改 `state` 的 `action`，而 `Redux Saga` 监听的 `action` 都仅仅是触发异步任务而已，不直接修改 `state`。所以二者角色不一样。例如 `Redux Saga` 监听到了 `Action` 拉取数据后，一定会触发另一个 `Action 被 `Redux Actions` 的 `handleAction(s)` 给处理。
- `Redux Saga` 监听的 `Action` `type` 仍然需要用常量定义，而 `Action` 创建函数需要用 `Redux Action` 来定义，并且要把前面定义的常量传给他，最终使用 `dispatch` 来触发 `Redux Saga` 的监听。