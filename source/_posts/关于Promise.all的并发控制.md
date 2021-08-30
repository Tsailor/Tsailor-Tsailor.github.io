---
    title: 关于Promise.all的并发控制
    date: 2021-08-30
    tags: 前端基础
---

在前端同时处理多个Http请求时，为了减少请求与响应时延，往往会使用**Promise.all()**实现请求并行发送。但是浏览器对此有一定的限制，
**在 Chrome 浏览器中允许的最大并发请求数目为 6，这个限制还有一个前提是针对同一域名的，超过这一限制的后续请求将会被阻塞。**

于是，可以通过一定方式实现并发控制，一次发送一组Http请求，待执行完成一个Http请求则将下一个Http请求加入分组
<!--more-->
```javascript
/**
 * @description Promsie.all()并发控制
 * @param {Array<() => Promise<any>>} request - 异步事件组成的数组，每个元素执行后会返回一个Promise对象
 * @param {number} max - 最大并发控制数
 * @param {() => Promise<any>} callback - 批量执行完成回调函数
*/
export const handleBatchRequest = (request:Array<() => Promise<any>>, max = 6, callback: () => Promise<any>) => {
  let i = 0; // 数组下标 已经执行过或者正在执行 的个数
  let okCount = 0; // 执行完成的个数
  let fetchArr: Promise<any>[] = []; // 正在执行的请求
  let len = request.length;
  let toFetch = (): Promise<any> => {
    // 如果异步任务都已开始执行，剩最后一组，则结束并发控制
    if (i === len) {
      return Promise.resolve();
    }

    // 执行异步任务
    let it = request[i]();
    i++;
    // 添加异步事件的完成处理
    it.then(() => {
      okCount++;
      fetchArr.splice(fetchArr.indexOf(it), 1);
    }).catch((err) => {
      // 添加异步事件的错误处理
      console.log('error', err);
      // 结束 toFetch
      return Promise.reject(err);
    });
    fetchArr.push(it);

    let p = Promise.resolve();
    // 如果并发数达到最大数，则等其中一个异步任务完成再添加
    if (fetchArr.length >= max) {
      p = Promise.race(fetchArr);
    }

    // 执行递归
    return p.then(() => toFetch());
  };

  toFetch()
    .then(() =>
      // 最后一组全部执行完再执行回调函数
      Promise.all(fetchArr).then(() => {
        // 执行全部成功的回调函数
        callback();
      }),
    )
    .catch(() => {
      // todo： 错误处理
    });
};
```
