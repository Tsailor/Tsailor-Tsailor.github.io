---
    title: JS数组去重的多种方式比较
    date: 2020-07-20
    tags: JS基础
---
1、双重for循环

时间复杂度（O\^2）,所有方式中时间开销最大。
仅仅去除数组中的基本类型元素（不包含 NaN,NaN!==NaN）。

```javascript
function distinct(arr){    
    for(let i=0;i<arr.length;i++){       
         for(let j=i+1;j<arr.length;j++){        
            if(arr[j]===arr[i]){            
                arr.splice(j,1);                
                j--;            
            }        
        }   
    }     
    return arr;
}                         
```
<!--more-->

测试样例
```javascript
var arr1=[1,2,2,3,"a","d","s","a ","s","1",null,null,undefined,undefined,NaN,NaN,new String("name"),new String("name")];
var res1=distinct(arr1);
console.log(res1);                //[1, 2, 3, "a", "d", "s", "1", null , undefined, NaN, NaN, String, String]                                   

```
**总结：NaN和Object不能去重**

2、Array.filter()和indexOf()

Array.filter()
:为每个元素执行一次callback，将返回所有结果为true的数组元素创建的新的数组。\
Array.indexOf():返回数组中第一次出现该元素的索引

思路：比较元素在数组中第一次出现的位置（索引）和自身的位置（索引）是否相等，不等则重复。

```javascript
function distinct(arr){    
    var res=arr.filter((ele,index)=>{         
         return arr.indexOf(ele)===index;     
    });    
    return res;
}                  
```
测试样例：

```javascript
var arr1=[1,2,2,3,"a","d","s","a","s","1",null,null,undefined,undefined,NaN,NaN,new String("name"),new String("name")];
var res1=distinct(arr1);console.log(res1);     //    [1, 2, 3, "a", "d", "s", "1", null, unde fined， String, String]arr1.indexOf(NaN);  
                                                //  -1  indexOf 找不到NaN  因为 NaN!==NaN                              
```

**总结：NaN丢失，Object不去重**
3、ES6 Set去重

Array.from()：方法从一个类似数组或可迭代对象创建一个新的，浅拷贝的数组实例

```javascript
function distinct(arr){    
    return Array.from(new Set(arr));
}         
```
测试样例：

```javascript
var arr1=[1,2,2,3,"a","d","s","a","s","1",null,null,undefined,undefined,NaN,NaN,new String("name"),new String("name")];
var res1=distinct(arr1);
console.log(res1);     //   [1, 2 , 3, "a", "d", "s", "1", null, undefined, NaN, String, String]           
```
**总结：Object不能去重**
4、Object键值对
```javascript
function distinct(arr){    
    let obj={};    
    var res=arr.filter(function(ele,index,arr){        //对象只能用字符串作 key,所以转为 字符串。        
    //  1 和 "1" 转 为字符串后相等，为了避免 加上类型        
        return obj.hasOwnProperty (typeof ele + ele)? false : obj[typeof ele + ele]=true;   
    })    
    return res;
}                
```
测试样例：

```javascipt
var arr1=[1,2,2,3,"a","d","s","a","s","1",null,null,undefined,undefined,NaN,NaN,new String("name"),new String("name")];
var res1=distinct(arr1);
console.log(res1);     //   [1, 2 , 3, "a", "d", "s", "1", null, undefined, NaN, String]                   
```

**总结：全部去重**
