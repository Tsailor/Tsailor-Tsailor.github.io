---
    title: 前端ArrayBuffer和String的相互转换
    date: 2021-09-08
    tags: 前端基础
---

在之前写公司的业务的时候有这样的一个需求，将ArrayBuffer中的数据暂存在JSON中，所以需要涉及到ArrayBuffer和String的相互转换。
记录一下

```typescript
/**
 * @description 将ArrayBuffer对象转换成string
 * @param {ArrayBuffer} buf - ArrayBuffer对象
 */
function bufferToString(buf): Promise<string> {
  return new Promise((resolve) => {
    var blob = new Blob([buf], { type: "application/octet-stream" });
    var reader = new FileReader();
    reader.onload = function (event) {
      const result = event.target.result as string;
      // console.log("result",result);
      resolve(result);
    };

    reader.readAsText(blob, "utf-8");
  });
}
```
<!--more-->

```typescript
/**
 * @description 将string转换成ArrayBuffer
 * @param {string} str - 参数string
 */
function stringToBuffer(str): Promise<ArrayBuffer> {
  return new Promise((resolve) => {
    var blob = new Blob([str], { type: "text/plain" });
    var reader = new FileReader();
    reader.onload = function (event) {
      const result = event.target.result as ArrayBuffer;
      // console.log("result",result);
      resolve(result);
    };

    reader.readAsArrayBuffer(blob);
  });
}
```

