> [原文地址](https://github.com/airbnb/javascript)

以下是学习总结：

### 变量
- 用const定义变量，避免使用var
- 如果需要重新定义变量，用let
- const和let都是块作用域变量
### 对象
- 字面量创建对象 const item = {}
- 使用计算属性名创建具有动态属性名称的对象

```
function getKey(k) {
  return `a key named ${k}`;
}

// good
const obj = {
  id: 5,
  name: 'San Francisco',
  [getKey('enabled')]: true,
};
```
- 使用对象方法的简写方式
- 使用对象属性的简写方式
- 简写属性统一放在顶部
```
const lukeSkywalker = 'Luke Skywalker';
const anakinSkywalker = 'Anakin Skywalker';
const atom = {
  lukeSkywalker,
  anakinSkywalker,
  age: 12,
  addValue(value) {
    return atom.value + value;
  },
};
```
- 只在有无效符号的属性时使用引号
```
const good = {
  foo: 3,
  bar: 4,
  'data-blah': 5,
};
```
- 不要直接使用Object.prototype相关方法

```
// bad
console.log(object.hasOwnProperty(key));

// good
console.log(Object.prototype.hasOwnProperty.call(object, key));

// best
const has = Object.prototype.hasOwnProperty; // cache the lookup once, in module scope.
/* or */
import has from 'has';
// ...
console.log(has.call(object, key));
```
- 用spread operator(...)复制对象而不用Object.assign，用rest operator(...)获取新对象省略的属性

```
// bad
const original = { a: 1, b: 2 };
const copy = Object.assign({}, original, { c: 3 }); // copy => { a: 1, b: 2, c: 3 }

// good
const original = { a: 1, b: 2 };
const copy = { ...original, c: 3 }; // copy => { a: 1, b: 2, c: 3 }

const { a, ...noA } = copy; // noA => { b: 2, c: 3 }
```
### Arrays
- 字面量创建数组 const item = []
- 用push向数组添加数据
- 用spreads(...)复制数组
```
const itemsCopy = [...item]
```
- 用Array.from转换类Array的Object为array
```
const foo = document.querySelectorAll('.foo');
const nodes = Array.from(foo);
```
- 
