![image](http://wx2.sinaimg.cn/large/88ecd367gy1fglouzy1sij21kw1nrjy6.jpg)

### **组件在初始化时会触发的钩子函数：**

  **1、constructor()**
> 设置默认的props，也可以用dufaultProps设置组件的默认属性。  
初始化this.state


 **2、componentWillMount()**
> 组件初始化时调用，整个生命周期只调用一次，此时可以修改state，但不会re-render。


 **3、 render()**
>  react最重要的步骤，实例化，创建虚拟dom，进行diff算法，更新dom树。


 **4、componentDidMount()**
> 组件渲染之后调用，可以通过this.getDOMNode()获取和操作dom节点，只调用一次，一般load远端data放在此钩子函数里。


### **在更新时触发的钩子函数：**

  **5、componentWillReceivePorps(nextProps)**
> 组件初始化时不调用，组件接受新的props时调用。   
父组件的render事件会触发子组件的此事件。


  **6、shouldComponentUpdate(nextProps, nextState)**
> react性能优化非常重要的一环。组件接受新的state或者props时调用，在此对比前后两个props和state是否相同，如果相同则返回false阻止更新，因为相同的属性状态一定会生成相同的dom树，这样就不需要创造新的dom树和旧的dom树进行diff算法对比，节省大量性能，尤其是在dom结构复杂的时候。  
initial render和forceUpdate不触发。


  **7、componentWillUpdate(nextProps, nextState)**
> 组件初始化时不调用，只有在组件将要更新时才调用，此时可以修改state


  **8、render()**


  **9、componentDidUpdate()**
> 组件初始化时不调用，组件更新完成后调用，此时可以获取dom节点。


### **还有一个卸载钩子函数：**

  **10、componentWillUnmount()**
> 组件将要卸载时调用，一些事件监听和定时器需要在此时清除。


### 遇到的问题
>问题：一个子组件(B)，通过父组件(A)传递数据(A.radioFields)渲染。
子组件里某个按钮点击后会触发父组件的方法(A.change)然后更改(A.radioFields)
我理解的这个时候B组件会触发自身的componentWillReceiveProps并重新渲染，但是我写的代码并不会。  
  
> 解答：  
1. 数据来源不对。（A.radioFields数据是定义在render函数中的对象，既不是state也不是props。）  
2. 没有触发state更改（A.change没有dispatch也没有setState） 
3. 要么让A重新渲染（这样 A的render函数重新执行。然后B才会重新渲染。） 要么B里调用setState（B重新渲染）

> 问题：怎么创建同时受控与非受控的组件   

> 出现原因：类似input、select跟用户交互的组件   
*如果自身维护自己的的state，则外部无法修改（非受控）   
如果通过props设置value，则input只能通过外部组件来更新（受控）   
我们希望外部能修改value且内部又能够触发更新数据*   

> 解答：同时维护props.value和state.value的值。props.value在展示上拥有更高的优先级，state.value代表着组件真正的值。  
原则1. props.value比内部state.value优先级更高   
原则2. 组件中所有变化都同步到内部的state.value，并通过执行props.change来触发更新   
原则3. 当组件接受到新的props时，将props.value反应给state.value   
原则4. 当props.value变化时更新组件