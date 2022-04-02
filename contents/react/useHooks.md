## 问题

我不能理解为什么需要 `useImperativeHandle`、`useLayoutEffect` 和 `useDebugValue` 这样的 `Hooks`，你能举出可以使用它们的例子吗，但请不要举出文档中的例子。

## 答案

请允许我在回答前说明，这些 `Hooks` 确实很少使用。

99% 的情况下，你不会需要这些。它们只是为了涵盖一些罕见的极端场景。

---

### useImperativeHandle

通常，当你使用 `Ref` 时，你会得到 `Ref` 所连接的组件的实例元素。这让你可以直接与 `DOM` 元素进行交互。

`useImperativeHandle` 和它非常相似，但是它可以让你做两件事:

- 你可以对返回的值进行控制。而不是返回实例元素，你明确说明返回值是什么。
- 它允许你用自己的函数来代替原生函数（如 `blur`, `focus` 等），从而允许对正常行为产生副作用，或完全是不同的行为。

当你想完成上述任何一项的功能时，你可能出于一些原因，不想向父类暴露本地属性，或者你想改变本地函数的行为。

> `useImperativeHandle` 可以让你在使用 `ref` 时自定义暴露给父组件的实例值。

#### Example

在这个例子中，我们将从 `ref` 中得到的值将只包含我们在 `useImperativeHandle` 中声明的函数 `blur`。

它不包含其他的属性。该函数被 "定制" 为与你通常所期望的不同的行为。

这里，它设置了`document.title`，并同时调用 `blur`。

```js
const MyInput = React.forwardRef((props, ref) => {
  const [val, setVal] = React.useState('');
  const inputRef = React.useRef();

  React.useImperativeHandle(ref, () => ({
    blur: () => {
      document.title = val;
      inputRef.current.blur();
    }
  }));

  return (
    <input
      ref={inputRef}
      val={val}
      onChange={e => setVal(e.target.value)}
      {...props}
    />
  );
});

const App = () => {
  const ref = React.useRef(null);
  const onBlur = () => {
    console.log(ref.current); // 只包含一个属性：blur
    ref.current.blur();
  };

  return <MyInput ref={ref} onBlur={onBlur} />;
};

ReactDOM.render(<App />, document.getElementById("app"));
```

### useLayoutEffect

在某种程度上与 `useEffect` 相似，但它的不同之处在于，它将在 `React` 提交 `DOM` 更新后运行。

在少数情况下，当你需要在更新后计算元素之间的距离或做其他更新后的计算/副作用时使用。

    其函数签名与 useEffect 相同，但它会在所有的 DOM 变更之后同步调用 effect。可以使用它来读取 DOM 布局并同步触发重渲染。在浏览器执行绘制之前，useLayoutEffect 内部的更新计划将被同步刷新。

#### Example

假如你有一个绝对定位的元素，其高度可能会变化，你想在它下面定位另一个 `div`。

你可以使用 `getBoundingCLientRect()` 来计算父元素的高度和顶部属性，然后将其应用于子元素的顶部属性。

使用useEffect。(会有一种闪烁的行为)

```ts
const Message = ({boxRef, children}) => {
  const msgRef = React.useRef(null);
  React.useLayoutEffect(() => {
    const rect = boxRef.current.getBoundingClientRect();
    msgRef.current.style.top = `${rect.height + rect.top}px`;
  }, []);

  return <span ref={msgRef} className="msg">{children}</span>;
};

const App = () => {
  const [show, setShow] = React.useState(false);
  const boxRef = React.useRef(null);

  return (
    <div>
      <div ref={boxRef} className="box" onClick={() => setShow(prev => !prev)}>Click me</div>
      {show && <Message boxRef={boxRef}>Foo bar baz</Message>}
    </div>
  );
};

ReactDOM.render(<App />, document.getElementById("app"));
```

### useDebugValue

有时你可能想调试某些值或属性，但这样做可能需要昂贵的操作，可能影响性能。

`useDebugValue` 只有在 `React DevTools` 打开并检查相关 `hook` 时才会被调用，以防止对性能产生任何影响。

    useDebugValue 可用于在 React 开发者工具中显示自定义 hook 的标签。

不过我个人从未使用过这个钩子。也许评论中的人可以用一个好的例子给出一些见解。

> 问题来源：[https://stackoverflow.com/questions/57005663/when-to-use-useimperativehandle-uselayouteffect-and-usedebugvalue](https://stackoverflow.com/questions/57005663/when-to-use-useimperativehandle-uselayouteffect-and-usedebugvalue)