---
    title: JS深拷贝与浅拷贝
    date: 2020-06-26
    tags: JS基础
---

JS数据类型划分：
基本数据类型：string,number,boolean,null,undefined
引用数据类型：Object(包括 Array,String,Function,Math,Date等)

JS内存分为堆（heap）和栈（stack）
栈：由系统自动分配，自动回收，效率高，但容量小。
堆：由程序员手动分配内存，并且手动销毁（高级语言如JS中有垃圾自动回收机制），效率不如栈，但容量大。

JS的基本类型分配在栈中。
<!--more-->
而因为**引用类型大小的不固定，系统将存储该引用类型的地址存在栈中，并赋值给变量本身，而具体的内容存在堆中。**
所以当访问一个对象的时候，先访问栈中它的地址，然后按照这个地址去堆中找到它的实际内容。

当复制的时候，对于基本类型的变量，系统会为新的变量在栈中开辟一个新的空间，赋予相同的值，然后这两个变量就各自独立，毫无牵连。
而对于引用类型的变量，新的变量复制的是那个**对象在堆中的地址**，这两个变量指向的是同一个对象。

JS的基本类型不存在浅拷贝还是深拷贝的问题，主要是针对于对象，比如一般的对象，和特殊的数组对象。

**浅拷贝是指复制对象的时候，指对第一层键值对进行独立的复制。**

浅拷贝实现方式1：
```javascript
function shaCopy(source){   //浅拷贝    
    var target={};    
    if(!source||typeof source!== "Object")       
       return;    
    for(var key in source){      
       target[key]=source[key];    
    }     
    return target;
}
var a={ name:"jack", age:18 };
var res=shaCopy(a);console.log(res);  
    //  {name: "jack", age: 18}
res.age=20;
console.log(a);     // {name: "jack", age: 18}
console.log(res);   //  {name: "jack", age: 20} 
```

浅拷贝实现方式2：

Object.assign是ES6的新函数。
Object.assign()
方法可以把任意多个的源对象自身的可枚举属性拷贝给目标对象，然后返回目标对象。
但是 Object.assign()
进行的是浅拷贝，拷贝的是对象的属性的引用，而不是对象本身。
```javascript
    Object.assign(target, ...sources)                                   
```
参数：

target：目标对象。
sources：任意多个源对象。
返回值：目标对象会被返回。

```javascript
var obj1={ name:"jack", friend:{ name:"tom",age:18 }};
var res1=Object.assign({},obj1);console.log(res1)  |
   // { name:"jack", friend:{ name:"tom",age:18 }}  
    //但是修改res1中friend对应的属性，obj1中friend对应的属性也会改变res1.friend. |
name="lili";
console.log(obj1);   // { name:"jack", friend:{ name:"lili",age:18 }}                     
```
**那如何完全独立地拷贝出一份呢？这也就是所谓的深拷贝。**

深拷贝实现方式1：

Object.assign();
当对象只有一层时，Object.assign()也能实现深拷贝。

深拷贝实现方式2：
用JSON.stringify把对象转成字符串，再用JSON.parse把字符串转成新的对象。

但是只有对象内属性值不为undefined和Function时可用。

深拷贝实现方式3：
递归实现

```javascript
function deepClone(source) {
    var target = {};
    if (!source ||typeof source !== "object") return;
    for (var key in source) {
        var value = source[key]; 
        if (typeof
            value === 'object') {  //属性值是数组或对象     
            target[key] = deepClone(value)
        } else {
            target[key]= value;
        }
    };
    return target;
}; 
var obj1 = { 
    name: "jake",
    friend: { 
        name: "Tom",
        address: {
            city: "Beijing"
        }
    }
};
var res1=deepClone(obj1);  
res1.friend.address.city = "ShangHai";
    //属性值为对象
console.log(res1);      //  { name: "jake", friend: {name: "Tom", address: { city: "ShangHai" }}}; 
console.log(obj1);      // { name:"jake",friend: { name: "Tom", address: { city: "Beijing"}}};
//属性值为数组
var obj2={ name:"jake",friend:[ 13,24,[19,20,18] ]}; 
var  res2 = deepClone(obj2); 
res2.friend[2][1] = 0; 
console.log(res2);    // { name: "jake", friend: [13, 24, [19, 0, 18]]}; 
console.log(obj2);    // { name:"jake",friend:[ 13,24,[19,20,18] ]};  

```
深拷贝实现方式4：
jquery 有提供一个\$.extend可以用来做 Deep Copy。
```javascript
    var $ = require('jquery');
    var obj1 = { a: 1, b: { f: { g: 1 } }, c: [1, 2, 3]};
    var obj2 = $.extend(true, {}, obj1);
    console.log(obj1.b.f === obj2.b.f);// false          
```

深拷贝实现方式5：
另外一个很热门的函数库lodash，也有提供_.cloneDeep用来做 Deep Copy。

```javascript
    var _ = require('lodash');
    var obj1 = { a: 1,  b: { f: { g: 1 }  },  c: [1, 2, 3]};
    var obj2 = _.cloneDeep(obj1);
    console.log(obj1.b.f === obj2.b.f);                         
```
