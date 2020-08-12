---
    title: DOM操作-获取标签数目
    date: 2020-07-23
    tags: JS基础
---
完成一个 getTags 函数，可以检测当前页面用到了哪些标签，函数返回包含标签的字符串的数组，比如页面如下
```html
<html>
  <head></head>
  <body>
    <div></div>
    <p></p>
  </body>
</html>
```
知识回顾：
nodeType === 1   表示元素节点
nodeType === 2   表示属性节点

document.documentElement   表示根节点

<!--more-->
```javascript 
    let map = {};
    let getTags = function(node){
        if( node.nodeType != 1)    return;
        let tagsName = node.nodeName;
        map[tagsName] = ( map[tagsName] ? map[tagsName]++ : 1 )

        for(let i = 0; i < node.childNodes.length; i++){
            getTags( node.childNodes[i] )
        }
    }
    getTags(document.documentElement);

    console.log(map.values)
```