## 问题：React Hooks 中，如何在 useEffect 里比较 oldValues 和 newValues ？

我有 3 个根据输入并进行值变化的数据：`rate`、`sendAmount` 和 `receiveAmount`。

现在我把这 3 个数据放在了 3 个 `useEffect` 的不同参数上，有以下规则：

- 如果 `sendAmount` 改变了，计算 `receiveAmount = sendAmount * rate`
- 如果 `receiveAmount` 改变了，计算 `sendAmount = receiveAmount / rate`
- 如果 `rate` 改变了，当 `sendAmount > 0` 时，计算`receiveAmount = sendAmount * rate`，或者当 `receiveAmount > 0` 时，计算 `sendAmount = receiveAmount / rate`。

是否有办法像在 `componentDidUpdate` 上那样直接比较 `oldValues` 和 `newValues`，而不是现在这样为这种情况做 3 个规则判断？

## 答案

你可以写一个自定义 `hook`，使用 `useRef` 可以获得之前的数据 ([previous props](https://zh-hans.reactjs.org/docs/hooks-faq.html#how-to-get-the-previous-props-or-state))

```js
function usePrevious(value) {
  const ref = useRef();
  useEffect(() => {
    ref.current = value;
  });
  return ref.current;
}
```

然后在 `useEffect` 中使用:

```js
const Component = (props) => {
    const {receiveAmount, sendAmount } = props
    const prevAmount = usePrevious({receiveAmount, sendAmount});
    useEffect(() => {
        if(prevAmount.receiveAmount !== receiveAmount) {

         // process here
        }
        if(prevAmount.sendAmount !== sendAmount) {

         // process here
        }
    }, [receiveAmount, sendAmount])
}
```

如果你为每个变化的 `id` 分别使用不同的 `useEffect` 来进行处理，逻辑会更清楚，这样可能更好，也更容易阅读和理解。

> 问题来源：[https://stackoverflow.com/questions/53446020/how-to-compare-oldvalues-and-newvalues-on-react-hooks-useeffect](https://stackoverflow.com/questions/53446020/how-to-compare-oldvalues-and-newvalues-on-react-hooks-useeffect)
