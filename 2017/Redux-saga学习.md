# Redux-saga学习笔记

### 概述
> redux-saga 是一个用于管理 Redux 应用异步操作的库，（例如异步获取服务器数据、获取浏览器缓存）使React/Redux应用更简单明了。

### 出现的原因
> redux根据action进行Store的修改，但是无法处理异步操作。redux-saga通过创建Sagas将异步操作逻辑集中处理，可以用来代替redux-thunk。

### 一般实现（自己的理解）
> sagaMiddleware监听异步action，做异步处理（fetch），处理返回结果后调用同步action

//TODO: 无阻塞调用代码整理
```
function* authorise(user, password) {
    try{
        // 异步登陆请求
        const token = yield call(Api.fetch, user, password)
        // 请求成功更改state
        yield put({type: 'LOGIN_SUCCES'}, token)
        // 保存token信息
        yield call(Api.storeItem, token)
        return token
    } catch(error){
        // 请求失败更改state
        yield put({type: 'LOGIN_ERROR'}, token)
    } finally{
        // 请求主动取消
        if(yield canceled()){
            //清空等待登陆标记
        }
    }
}

function* loginFlow() {
    while(true){
        // 监听登陆请求
        const { user, password } = yield take('LOGIN_REQUEST')
        // fork登陆请求，并返回task对象
        const task = yield fork(authorise, user, password)
        // 监听登出、登陆出错请求
        const action = yield take(['LOGOUT', 'LOGIN_ERROR'])
        // 如果捕获到登出请求，主动取消登录请求
        if(action.type === 'LOGOUT')
            yield cancel(task)
        // 请出token信息
        yield call(Api.clearItem, token)
    }
}

```
