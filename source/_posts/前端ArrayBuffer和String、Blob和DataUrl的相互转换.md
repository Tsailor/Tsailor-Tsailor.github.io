---
    title: 前端ArrayBuffer和String、Blob和DataUrl的相互转换
    date: 2021-09-08
    tags: 前端基础
---

在之前写公司的业务的时候有这样的一个需求，通过XMLHttpRequest拿到图片的二进制内容。然后将ArrayBuffer中的数据暂存在JSON中，所以需要涉及到ArrayBuffer和String的相互转换。
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

但是问题并没有完全解决，原因大概是，图片二进制内容ArrayBuffer转换成string类型，通过utf-8编码生成的乱码，再转换回去时候，二进制数据内容和长度发生了变化。
于是，我结合另一个情景，前端图片经常使用DataUrl表示(DataUlr使用的也就是Base64编码)，所以我把图片转成成DataUrl，保存的时候存下这个DataUrl,使用的时候再转换成二进制内容。
于是， 设置 `XMLHttpRequest.responseType = 'blob';`
然后问题就变成了 Blob 与 DataUrl的转换

```typescript

/**
 * @description 将blob转换成DataUrl
 * @param {Blob} blob - blob
 */
function blobToDataUrl(blob: Blob): Promise<string> {
  return new Promise((resolve) => {
    var reader = new FileReader();
    reader.onload = function (event) {
      const result = event.target.result as string;
      // console.log("result",result);
      resolve(result);
    };

    reader.readAsDataURL(blob);
  });
}

/**
 * @description 将DataUrl转换成Blob
 * @param {string} dataurl - 参数dataurl （base64编码）
 */
function dataUrlToBlob(dataurl: string): Promise<Blob> {
  return new Promise((resolve) => {
    var arr = dataurl.split(","),
      mime = arr[0].match(/:(.*?);/)[1],
      bstr = atob(arr[1]),
      n = bstr.length,
      u8arr = new Uint8Array(n);
    while (n--) {
      u8arr[n] = bstr.charCodeAt(n);
    }
    let blob = new Blob([u8arr], { type: mime });
    resolve(blob);
  });
}
```