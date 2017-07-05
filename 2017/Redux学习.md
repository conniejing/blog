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