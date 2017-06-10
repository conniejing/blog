# Javascript常用设计模式
### 模块模式（Module Pattern）
javascript中的封装实现用到了该模式，类似于面向对象语言中的Class，保护私有变量和私有方法。封装不会污染外部作用域。
模块利用立即执行方法表达式（IIFE）来提供私有作用域，也就是保护私有变量和方法的闭包。利用这种方法返回的是一个对象而不是方法。

```
(function() {

    // 声明私有变量和方法

    return {
      // 声明公有变量和方法
    }

})();
```
更具体的实现：

```
var HTMLChanger = (function() {
  var contents = 'contents'

  var changeHTML = function() {
    var element = document.getElementById('attribute-to-change');
    element.innerHTML = contents;
  }

  return {
    callChangeHTML: function() {
      changeHTML();
      console.log(contents);
    }
  };

})();

HTMLChanger.callChangeHTML();       // 'contents'
console.log(HTMLChanger.contents);  // undefined
```
#### 显示模块模式（Revealing Module pattern）

```
var Exposer = (function() {
  var privateVariable = 10;

  var privateMethod = function() {
    console.log('Inside a private method!');
    privateVariable++;
  }

  var methodToExpose = function() {
    console.log('This is a method I want to expose!');
  }

  var otherMethodIWantToExpose = function() {
    privateMethod();
  }

  return {
      first: methodToExpose,
      second: otherMethodIWantToExpose
  };
})();

Exposer.first();        // This is a method I want to expose!
Exposer.second();       // Inside a private method!
Exposer.methodToExpose; // undefined
```

### 原型模式（Prototype Pattern）
原型模式主要用于性能密集情况下创建对象，把方法定义在原型对象上，创建的所有新实例公用方法，提高性能。

```
var TeslaModelS = function() {
  this.numWheels    = 4;
  this.manufacturer = 'Tesla';
  this.make         = 'Model S';  // 实例属性
  this.say = function(){          // 实例方法
      console.log('Hi');
  }
  function privateFun(){          // 私有方法
      console.log('private');
  }
}

// 原型方法
TeslaModelS.prototype.go = function() {
  // Rotate wheels
}

TeslaModelS.prototype.stop = function() {
  // Apply brake pads
}
```
OR

```
var TeslaModelS = function() {
  this.numWheels    = 4;
  this.manufacturer = 'Tesla';
  this.make         = 'Model S';
}

TeslaModelS.prototype = {
  go: function() {
    // Rotate wheels
  },
  stop: function() {
    // Apply brake pads
  }
}
```
#### 显示原型模式（Revealing Prototype Pattern）

```
var TeslaModelS = function() {
  this.numWheels    = 4;
  this.manufacturer = 'Tesla';
  this.make         = 'Model S';
}

TeslaModelS.prototype = function() {

  var go = function() {
    // Rotate wheels
  };

  var stop = function() {
    // Apply brake pads
  };

  return {
    pressBrakePedal: stop,
    pressGasPedal: go
  }

}();
```
### 观察者模式（Observer Pattern）
> If an object is modified it broadcasts to dependent objects that a change has occurred.
One benefit is decoupling the view from the model to reduce dependencies.
The subject contains references to the concrete observers to notify for any changes. The Observer object is an abstract class that allows for the concrete observers to implements the notify method.
![image](https://upload.wikimedia.org/wikipedia/commons/thumb/8/8d/Observer.svg/1000px-Observer.svg.png)
  
自实现简单观察者模式

```
class Subject{
    constructor(props){
        this.observers = [];
    }
    addObserver(observer){
        this.observers.push(observer);
    }
    change(){
        this.observers.map((observer) => {
            observer.notify();
        });
    }
}

class Observer{
    constructor(props){
        this.name = props.name;
        this.update = function(){
            console.log('This name is: ' + this.name);
        }
    }
    notify(){
        this.update();
    }
}

class ConcreteA extends Observer{
    constructor(props){
        super(props);
        this.update = function(){
            console.log(`ConcreteA's name is: ` + this.name);
        }
    }
}

let a = new ConcreteA({'name': 'Connie'});
let b = new Observer({'name': 'Alex'});
let subj = new Subject();
subj.addObserver(a);
subj.addObserver(b);
subj.change();
```
网上实例

```
var Subject = function() {
  this.observers = [];

  return {
    subscribeObserver: function(observer) {
      this.observers.push(observer);
    },
    unsubscribeObserver: function(observer) {
      var index = this.observers.indexOf(observer);
      if(index > -1) {
        this.observers.splice(index, 1);
      }
    },
    notifyObserver: function(observer) {
      var index = this.observers.indexOf(observer);
      if(index > -1) {
        this.observers[index].notify(index);
      }
    },
    notifyAllObservers: function() {
      for(var i = 0; i < this.observers.length; i++){
        this.observers[i].notify(i);
      };
    }
  };
};

var Observer = function() {
  return {
    notify: function(index) {
      console.log("Observer " + index + " is notified!");
    }
  }
}

var subject = new Subject();

var observer1 = new Observer();
var observer2 = new Observer();
var observer3 = new Observer();
var observer4 = new Observer();

subject.subscribeObserver(observer1);
subject.subscribeObserver(observer2);
subject.subscribeObserver(observer3);
subject.subscribeObserver(observer4);

subject.notifyObserver(observer2); // Observer 2 is notified!

subject.notifyAllObservers();
// Observer 1 is notified!
// Observer 2 is notified!
// Observer 3 is notified!
// Observer 4 is notified!
```
// TODO: Publish/Subscribe 的区别
### 单例模式（Singleton Pattern）
所有实例共享同一个对象，单例模式限制创建多个对象，当创建了一个对象后，将会返回这个已创建的对象。

```
var printer = (function () {

  var printerInstance;

  function create () {

    this.name = 'connie';
	
    function print(str) {
	  this.str = str || 'print';
      console.log(this.str);
    }

    return {
      // public + private states and behaviors
      name: this.name,
      print: print
    };
  }

  return {
    getInstance: function() {
      //当创建了一个对象后，将会返回这个已创建的对象
      if(!printerInstance) {
        printerInstance = create();
      }
      return printerInstance;
    }
  };

})();

var officePrinter1 = printer.getInstance();
var officePrinter2 = printer.getInstance();
```
> Therefore, developers must be privy to synchronization when implementing singletons in multithreaded applications.