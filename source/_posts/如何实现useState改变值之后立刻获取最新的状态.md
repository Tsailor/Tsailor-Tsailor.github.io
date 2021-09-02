---
    title: 如何实现useState改变值之后立刻获取最新的状态
    tags: React
    date: 2021-09-02
---

### 如何实现 useState 改变值之后立刻获取最新的状态

之前在写项目的时候,遇到一个问题,如何实现 useState 改变值之后立刻获取最新的状态?
然后刚刚好在思否上看到一个大神操作方法
记录一下
[原文连接](https://segmentfault.com/a/1190000039365818)

<!--more-->

**因为在 react 合成事件中改变状态是异步的，出于减少 render 次数，react 会收集所有状态变更，然后比对优化，最后做一次变更**

```javascript
/*
 * @lastTime: 2021-03-05 15:29:11
 * @Description: 同步hooks
 */

import { useEffect, useState, useCallback } from "react";

const useSyncCallback = (callback) => {
  const [proxyState, setProxyState] = useState({ current: false });

  const Func = useCallback(() => {
    setProxyState({ current: true });
  }, [proxyState]);

  useEffect(() => {
    if (proxyState.current === true) setProxyState({ current: false });
  }, [proxyState]);

  useEffect(() => {
    proxyState.current && callback();
  });

  return Func;
};

export default useSyncCallback;
```

使用示例

```javascript
function App() {
  const [state, setstate] = useState(0);

  const setT = () => {
    setstate(2);
    func();
  };
  /** 将func的方法传递给useSyncCallback然后返回一个新的函数 */
  const func = useSyncCallback(() => {
    console.log(state);
  });

  return (
    <div className="App">
      <header className="App-header">
        <button onClick={setT}>set 2</button>
      </header>
    </div>
  );
}

export default App;
```
