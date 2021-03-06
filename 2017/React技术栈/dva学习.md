# dva

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
  /** 
   * reducer在combine到rootReducer时的key值
   * 全局state上的属性，只能用字符串，不支持.创建多层命名空间
   * pages中mapStateToProps的参数值必须一致
   */
  namespace: 'products',  
  /**
   * reducer的initialState
   * 优先级低于传给dva()的opts.initialState
   */
  state: {  // reducer的initialState
    list: [],
    loading: false,
  },
  /**
   * key/value格式定义subscription
   * 用于订阅一个数据源，然后根据需要 dispatch 相应的 action
   * app.start()时被执行
   */
  subscriptions: [
    function(dispatch) {
      dispatch({type: 'products/query'});
    },
  ],
  /**
   * key/value格式定义effect
   * 用于处理异步操作和业务逻辑
   * 不直接修改state
   */
  effects: {
    ['products/query']: function*() {
      yield call(delay(800));
      yield put({
        type: 'products/query/success',
        payload: ['ant-tool', 'roof'],
      });
    },
  },
  /**
   * key/value格式定义reducer
   * 用于处理同步操作
   * 唯一可以修改state的地方
   */
  reducers: {
    ['products/query'](state) { // 类似switch…case({action.type})
      return { ...state, loading: true, };
    },
    ['products/query/success'](state, { payload }) {
      return { ...state, loading: false, list: payload };
    },
  },
});
```
### dva 2.0
> 2.0 最主要的变化是提取了 dva-core，是仅封装了 redux 和 redux-saga 的纯数据流方案。这使得 dva 可以应用在除 react 之外的其他领域，比如 RN、小程序、游戏、vue 等领域；同时也可满足同一领域的多种实现，比如为 react 应用不同的路由方案的 dva-react-router-3 和 dva-no-router

### 开发参考步骤
1. 创建Model：在models文件夹下新建文件夹（UserManage），新建index.js并创建model（此时主要是namespace和state数据准备）   
&nbsp;&nbsp;a) subscriptions中添加history.listen   
&nbsp;&nbsp;b) 添加fetch相关effects   
&nbsp;&nbsp;c) 添加reducers   
2. 创建Component：在components文件夹下新建文件夹（UserManage），再新建index.js并创建页面组件（UserManage，页面展现样式）
3. 创建Page Component：在pages文件夹下新建UserManage.js，connect(mapStateToProps, mapDispatchToProps)(UserManage)把props传入组件。在pages/index.js添加UserManage。
4. 在入口文件（index.js）中注册model app.model(require('./models/userManage')); 
5. 在路由文件（router.js）中配置Route <Route path="/userManage" component={pages.UserManage} breadcrumbName="用户管理"/>
6. Ant Design中的Form表单受控组件，一定要注意，在getFieldDecorator方法后的组件里，直接写表单组件（input，RadioGroup等）不要用div包裹起来，会出现不能传值错误和无法传值的问题
