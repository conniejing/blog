# Redux学习笔记

### 概述
> Redux 是 JavaScript 状态容器，提供可预测化的状态管理。

### 出现的原因
> 单页面应用中，Javascript需要管理越来越多的状态（state），管理state变得越来越复杂，越来越困难。跟踪state状态变化变得很困难，想重现问题步骤和添加新功能也举步维艰。

### 原则
- 单一数据源
> 唯一一颗object tree，保证唯一性

```
{
  visibilityFilter: 'SHOW_ALL',
  todos: [
    {
      text: 'Consider using Redux',
      completed: true,
    },
    {
      text: 'Keep all state in a single tree',
      completed: false
    }
  ]
}
```

- State只读
> 唯一改变State的方法就是触发action，action是描述已发生事件的普通对象，表达想修改的意图，可以被序列化、存储、日志打印、测试回放。

```
store.dispatch({
  type: 'COMPLETE_TODO',
  index: 1
});

store.dispatch({
  type: 'SET_VISIBILITY_FILTER',
  filter: 'SHOW_COMPLETED'
});
```

- 纯函数来执行修改
> 描述action如何改变state的函数，可以控制它们被调用的顺序，传入附加数据，编写可复用的reducer来处理通用任务。

```
function visibilityFilter(state = 'SHOW_ALL', action) {
  switch (action.type) {
    case 'SET_VISIBILITY_FILTER':
      return action.filter
    default:
      return state
  }
}

function todos(state = [], action) {
  switch (action.type) {
    case 'ADD_TODO':
      return [
        ...state,
        {
          text: action.text,
          completed: false
        }
      ]
    case 'COMPLETE_TODO':
      return state.map((todo, index) => {
        if (index === action.index) {
          return Object.assign({}, todo, {
            completed: true
          })
        }
        return todo
      })
    default:
      return state
  }
}

import { combineReducers, createStore } from 'redux'
let reducer = combineReducers({ visibilityFilter, todos })
let store = createStore(reducer)
```

### 基础
#### state的结构设计
> 建议尽可能把state范式化，不存在嵌套。把state想象成数据库，用ID作为数据的主键，不同实体之间通过ID相互引用数据。
#### reducer函数
> 保持函数纯净，不要做以下这些操作：
> - 修改传入参数
> - 执行有副作用的操作，如API请求和路由跳转
> - 调用非纯函数，如Date.now() Math.random()   

> 函数中不要修改state，而使用Object.assign({}, state, {...})新建对象。这样不会修改上一次的state值，可以能进行状态跟踪、回放等。   
default情况下返回上一次的state。   

#### Store
> Redux只有单一的store   
   
> 其职责：
> - 维持应用的state
> - 提供getState() 获取state
> - 提供dispatch(action) 触发reducer更新state
> - 通过subscribe(listener) 注册监听器
> - 通过subscribe(listener) 返回的函数注销监听器

### 数据流
> 严格的单向数据流   

Redux应用中数据的生命周期：
1. 调用store.dispatch(action)
> 描述“发生了什么”的对象
2. Redux store调用传入的reducer函数  
> 把当前state和action传入reducer函数，返回计算后新的state。   
纯函数reducer是可预测，多次传入相同的输入必须有相同的输出。  
3. 根reducer合并多个子reducer组成单一的state树   
> Redux原生提供combineReducers()辅助函数   
4. Redux store保存根reducer返回的完成的state树   
> 所有订阅store.subscribe(listener)的监听器都将被调用

---

### 遇到的问题
1. 相同的action.type会覆盖还是都执行？有执行顺序么？   
答：不同的reduce（不同的state属性）中有相同的action.type都会被触发执行。按照创建的顺序执行，但是顺序并没有什么影响