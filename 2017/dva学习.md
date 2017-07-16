# dva学习笔记

### 概述
> 基于redux、redux-saga和react-router@2.0的轻量级前端框架

### 出现的原因
> * 编辑成本高，需要编写reducer，saga，action文件，来回切换
> * 不便于组织业务模型
> * saga书写较为复杂

### 核心实现
> 提供了app.model方法，把reducer，initialState，action，saga封装到一起

```
app.model({
  namespace: 'products',  // reducer在combine到rootReducer时的key值
  state: {  // reducer的initialState
    list: [],
    loading: false,
  },
  subscriptions: [  // 在dom ready后执行 TODO：详细理解
    function(dispatch) {
      dispatch({type: 'products/query'});
    },
  ],
  effects: {  // 简化saga
    ['products/query']: function*() { // 类似take({action.type})
      yield call(delay(800));
      yield put({
        type: 'products/query/success',
        payload: ['ant-tool', 'roof'],
      });
    },
  },
  reducers: {  // 简化reducers
    ['products/query'](state) { // 类似switch…case({action.type})
      return { ...state, loading: true, };
    },
    ['products/query/success'](state, { payload }) {
      return { ...state, loading: false, list: payload };
    },
  },
});
```


