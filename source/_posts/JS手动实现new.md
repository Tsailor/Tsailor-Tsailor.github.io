---
    title: JS手动实现new
    tags: 前端基础
    date: 2020-6-30
---
new 构造函数() 
执行顺序
1、在堆内存开辟对象内存空间，命名为obj;
2、在obj中添加**proto**属性，指向 构造函数.prototype;
3、更改this指向，将构造函数内的this指向obj;
4、执行构造函数内语句；
5、若构造函数内没有return或者没有return this、return基本数据类型的值，则返回obj在堆内存中地址;若return引用类型，则返回这个引用类型。
```javascript

function Person(name,age){    
    this.name=name;   
    this.age=age;    
    return 12;
}
var per=new Person("jack",18);
console.log(per);      //Person { name: "jack", age: 18} 
```
<!--more-->
return 基本数据类型时，return语句无意义
```javascript
function Person(name,age){    
     this.name=name;    
     this.age=age;   
     return {name:"TangM"};
}
var per=new Person("jack",18);
console.log(per);   //  {name: "TangM"}                

```
return引用类型，则返回这个引用类型,相等于 new失效。

手动实现 new

```javascript
function Person(name,age){    
    this.name=name;    
    this.age=age;
}
Person.prototype.address="beijing"; 
function Create(Con,...args){    //Create=>new    
    let obj={};  //开辟obj内存空间     
    Object.setPrototypeOf(obj,Con.prototype);//添加obj原型执行 构造函数的原型对象   result保存的是构造函数的返回值    
    let result=Con.apply( obj,args)   //更改this指向obj 并执行构造函数内语句 
    return result instanceof Object  ? result：obj;
}
var per=new Person("jack",18);
console.log(per);      //Person {name: "jack", age: 18}
console.log(per.address);  //   beijing
var _per=Create(Person,"jack",18);
console.log(_per);      //Person {name: "jack", age: 18}
console.log(_per.address );  ////   beijing                   
```

**result保存的是构造函数的返回值，如果返回值是一个对象则返回这个值，否则返回obj**
