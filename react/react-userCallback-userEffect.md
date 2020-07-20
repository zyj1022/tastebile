# userCallback & userEffect 使用注意事项

随着 v16.8 Hooks 问世后，人们开始大量使用函数组件，当使用函数进行编写组件时，如果需要在内部执行 API 接口的调用，需要用到 useEffect 生命周期钩子。

> useEffect 用于处理组件中的 effect，通常用于请求数据，事件处理，订阅等相关操作。

在最初版本的文档指出，防止 useEffect 出现无限循环，需要提供空数组 [] 作为 useEffect 依赖项，将使钩子只能在组件的挂载和卸载阶段运行。因此，我们会看到在很多使用 useEffect 的地方将 [] 作为依赖项传入。

> 使用 useEffect 出现无限循环的原因是，useEffect 在组件 mount 时执行，但也会在组件更新时执行。因为我们在每次
> 请求数据之后基本上都会设置本地的状态，所以组件会更新，因此 useEffect 会再次执行，因此出现了无限循环的情况。
> 然而，这种处理方式就会出现 react-hooks/exhaustive-deps 规则的警告，因此代码中常常会通过注释忽略此警告。

然而，这种处理方式就会出现 `react-hooks/exhaustive-deps` 规则的警告，因此代码中常常会通过注释忽略此警告。

```
// eslint-disable-next-line react-hooks/exhaustive-deps
import React, { useState, useEffect } from 'react'

import { fetchUserAction } from '../api/actions.js'

const UserContainer = () => {
  const [user, setUser] = useState(null);

  const handleUserFetch = async () => {
    const result = await fetchUserAction();
    setUser(result);
  };

  useEffect(() => {
    handleUserFetch();
    // 忽略警告
    // eslint-disable-next-line react-hooks/exhaustive-deps 
  }, []);

  if (!user) return <p>No data available.</p>

  return <UserCard data={user} />
};
```

最初，很多人认为这个警告毫无意义，从而选择进行忽略，而不去试图探索它是如何产生的。

其实，有些人没有意识到，`handleUserFetch()` 方法在组件每次渲染的时候都会重新创建（组件有多少次更新就会创建多少次）。

关于 `react-hooks/exhaustive-deps` 详细的讨论，可以看下这个 [issue](https://github.com/facebook/react/issues/15924)。

> useCallback 的作用在于利用 memoize 减少无效的 re-render，来达到性能优化的作用。

这就是为什么我们需要在 `useEffect` 中调用的方法上使用 `useCallback` 的原因。通过这种方式，我们可以防止 `handleUserFetch()` 方法重新创建（除非其依赖项发生变化) ，因此这个方法可以用作 `useEffect` 钩子的依赖项，而不会导致无限循环。

上边的例子应该这样重写:

```
import React, { useState, useEffect, useCalllback } from 'react'

import { fetchUserAction } from '../api/actions.js'

const UserContainer = () => {
  const [user, setUser] = useState(null);

  // 使用 useCallback 包裹
  const handleUserFetch = useCalllback(async () => {
    const result = await fetchUserAction();
    setUser(result);
  }, []);

  useEffect(() => {
    handleUserFetch();
  }, [handleUserFetch]); /* 将 handleUserFetch 作为依赖项传入 */

  if (!user) return <p>No data available.</p>

  return <UserCard data={user} />
};
```
我们将 `handleUserFetch` 作为 `useEffect` 的依赖项，并将它包裹在 `useCallback` 中。如果此方法使用外部参数，例如 `userId` (在实际开发中，可能希望获取特定的用户) ，则此参数可以作为 `useCallback` 的依赖项传入。只有 `userId` 发生变化时，依赖它的 `handleUserFetch` 才重写改变。