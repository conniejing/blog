![image](http://wx2.sinaimg.cn/large/88ecd367gy1fglouzy1sij21kw1nrjy6.jpg)
**组件在初始化时会触发的钩子函数：**

  **1、constructor()**
> 设置默认的props，也可以用dufaultProps设置组件的默认属性。  
初始化this.state


 **2、componentWillMount()**
> 组件初始化时调用，整个生命周期只调用一次，此时可以修改state，但不会re-render。


 **3、 render()**
>  react最重要的步骤，实例化，创建虚拟dom，进行diff算法，更新dom树。


 **4、componentDidMount()**
> 组件渲染之后调用，可以通过this.getDOMNode()获取和操作dom节点，只调用一次，一般load远端data放在此钩子函数里。


**在更新时触发德钩子函数：**

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


还有一个卸载钩子函数

  **10、componentWillUnmount()**
> 组件将要卸载时调用，一些事件监听和定时器需要在此时清除。
