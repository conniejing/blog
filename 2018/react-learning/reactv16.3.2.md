# React v16.3.2

## Context
> 提供了一种不需要经过组件树的每一层级的props传递数据的方法

> 有一些数据（例如: 国际化、主题）每一个组件都需要，但是通过一层一层的props会显得十分繁琐，context提供了共享这些数据的一种方法。

#### 什么场景下使用？
> context被设计来共享组件树中的"全局"数据，比如 username, theme, language。
```javascript
const ThemeContext = React.createContext('light');

class App extends React.Component {
  render() {
    // Use a Provider to pass the current theme to the tree below.
    return (
      <ThemeContext.Provider value="dark">
        <Toolbar />
      </ThemeContext.Provider>
    );
  }
}

// A component in the middle doesn't have to
// pass the theme down explicitly anymore.
function Toolbar(props) {
  return (
    <div>
      <ThemedButton />
    </div>
  );
}

function ThemedButton(props) {
  // React will find the closest theme Provider above and use its value.
  return (
    <ThemeContext.Consumer>
      {theme => <Button {...props} theme={theme} />}
    </ThemeContext.Consumer>
  );
}
```
#### 应用实例
* 动态context：通过更改component的state来动态控制Provider提供的value值
```javascript
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      theme: themes.light,
    };

    this.toggleTheme = () => {
      this.setState(state => ({
        theme:
          state.theme === themes.dark
            ? themes.light
            : themes.dark,
      }));
    };
  }

  render() {
    return (
      <Page>
        <ThemeContext.Provider value={this.state.theme}>
          <Toolbar changeTheme={this.toggleTheme} />
        </ThemeContext.Provider>
        <Section>
          <ThemedButton />
        </Section>
      </Page>
    );
  }
}

function ThemedButton(props) {
  return (
    <ThemeContext.Consumer>
      {theme => (
        <button
          {...props}
          style={{backgroundColor: theme.background}}
        />
      )}
    </ThemeContext.Consumer>
  );
}
```
* 通过嵌套组件更新context：通过context传递更新context的方法
```javascript
export const ThemeContext = React.createContext({
  theme: themes.dark,
  toggleTheme: () => {},
});

class App extends React.Component {
  constructor(props) {
    super(props);

    this.toggleTheme = () => {
      this.setState(state => ({
        theme:
          state.theme === themes.dark
            ? themes.light
            : themes.dark,
      }));
    };

    // State also contains the updater function so it will
    // be passed down into the context provider
    this.state = {
      theme: themes.light,
      toggleTheme: this.toggleTheme,
    };
  }

  render() {
    return (
      <ThemeContext.Provider value={this.state}>
        <Content />
      </ThemeContext.Provider>
    );
  }
}
```
* 消费多个context：可以有多个Provider嵌套，多个Consumer消费
* 在组件生命周期的钩子函数中获得Context：跟普通props一致，this.props.theme
* 高阶组件中消费context
> 当许多组件都需要消费context(例如 theme)时，如果每个组件都通过<Context.Consumer>封装会比较麻烦。

```javascript
// 较少组件消费context的写法
const ThemeContext = React.createContext('light');

function ThemedButton(props) {
  return (
    <ThemeContext.Consumer>
      {theme => <button className={theme} {...props} />}
    </ThemeContext.Consumer>
  );
}
```
```javascript
// 较多组件消费context的高阶组件用法
const ThemeContext = React.createContext('light');

export function withTheme(Component) {
  return function ThemedComponent(props) {
    // pass through any additional props as well
    return (
      <ThemeContext.Consumer>
        {theme => <Component {...props} theme={theme} />}
      </ThemeContext.Consumer>
    );
  };
}

// 需要消费context的组件
function Button({theme, ...rest}) {
  return <button className={theme} {...rest} />;
}

const ThemedButton = withTheme(Button);
```
* 传递refs给context的消费者
> render prop API 不会自动传递refs给wrapped元素，可以使用React.forwardRef
```javascript
class FancyButton extends React.Component {}

// Use context to pass the current "theme" to FancyButton.
// Use forwardRef to pass refs to FancyButton as well.
export default React.forwardRef((props, ref) => (
  <ThemeContext.Consumer>
    {theme => (
      <FancyButton {...props} theme={theme} ref={ref} />
    )}
  </ThemeContext.Consumer>
));

// app.js
import FancyButton from './fancy-button';

const ref = React.createRef();

// Our ref will point to the FancyButton component,
// And not the ThemeContext.Consumer that wraps it.
// We can call FancyButton methods like ref.current.focus()
<FancyButton ref={ref} onClick={handleClick}>
  Click me!
</FancyButton>;
```
#### 注意事项
错误使用：当Provider re-render时，每一个Consumer都会re-render，因为每次都会创建新的value值
```javascript
class App extends React.Component {
  render() {
    return (
      <Provider value={{something: 'something'}}>
        <Toolbar />
      </Provider>
    );
  }
}
```
正确使用：Provider re-render时，只要this.state.value值不变就不会re-render Consumer
```javascript
class App extends React.Component {
  constructor(props) {
    super(props);
    this.state = {
      value: {something: 'something'},
    };
  }

  render() {
    return (
      <Provider value={this.state.value}>
        <Toolbar />
      </Provider>
    );
  }
}
```

test
