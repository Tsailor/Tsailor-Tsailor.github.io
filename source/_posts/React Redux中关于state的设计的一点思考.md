---
    title: React Redux中关于state的设计的一点思考,
    tags: React
    date: 2020-08-18
---
## React Redux中关于state的设计的一点思考 ##

Redux应用执行过程中任何一个时刻本质上都是该时刻的应用state的反映，state驱动了Redux的逻辑运转。
现在以一个购物车的小案例做分析。
https://github.com/Tsailor/web-Practice/tree/master/Redux%E5%AE%9E%E7%8E%B0Shoppping-Cart/shoppingcart
内容截图如下
![shopping-Cart.png](shopping-Cart.PNG)
<!--more-->
整体分为两个部分：商品栏、购物车栏；
商品栏：标题、价格、库存量( title, price, inventory );
购物车栏：标题、价格、数量、小计金额( title , price, quality , unitPrice);
**先看一个错误的state设计用例**
```javascript
{    // 全局state
    products:[    // 商品栏state， 数组表示
        {"id": 1, "title": "iPad 4 Mini", "price": 500.01, "inventory": 2},
        {"id": 2, "title": "H&M T-Shirt White", "price": 10.99, "inventory": 10},
        {"id": 3, "title": "Charli XCX - Sucker CD", "price": 19.99, "inventory": 5}
    ]
    carts:{
        cartProducts:[
            {"id": 1, "title": "iPad 4 Mini", "price": 500.01, "quanlity": 1, "unitPrice" : 500.01},
            {"id": 2, "title": "H&M T-Shirt White", "price": 10.99, "quanlity": 1, "unitPrice" : 10.99},
        ],
        total:511.00
    }
}
```
这样设计的存在问题：
1、大量数据重复存储，如id,title,price等
2、触发事件Add To Cart时，需要修改的不止一个地方并且需要遍历products和cartProducts中的所有数据根据id进行比较。

存储浪费且性能消耗。

####该如何合理设计state呢 ####

记住一句话：**像设计数据库一样设计state**。把state看做一个数据库，state中每一部分状态看做数据库里的一张表，状态中的每一个字段对应表中的一个字段。设计数据库遵循的三个原则：
（1）数据按照不同的领域(domain)分类存放在不同的表中，不同表中存储的数据不能重复
（2）表中每一列的数据都依赖这张表的主键
（3）表中除了主键，其他列互相不能有直接依赖关系
于是，根据这三个原则，翻译出设计state时的原则：

**(1) 整个应用状态按照领域划分若干个子状态，子状态不能保存重复的数据**
**(2) state以键值对的形式保存数据，以记录的key或id作为索引，记录中其他字段都依赖于这个索引**
**(3) state中不能保存可以通过其他字段计算得出的数据，即state中的数据不能相互依赖**

**重新设计state**
```javascript
{
    products:{  // 分析：按照领域划分子状态， 子状态products 
        byId: {  // 键值对的形式保存数据 id/key 作为索引
            1: {"id": 1, "title": "iPad 4 Mini", "price": 500.01, "inventory": 2},
            2: {"id": 2, "title": "H&M T-Shirt White", "price": 10.99, "inventory": 10},
            3: {"id": 3, "title": "Charli XCX - Sucker CD", "price": 19.99, "inventory": 5}
        }
        containId:[1,2,3]  // state包含的key
    }
    cart:{     // 子状态 carts
        addedGoodId：[1,2] // 保存添加到购物车的商品的 id   没有重复数据
        quantityById: {   //  保存每个商品的数量， id作为key
            1: 1,
            2: 1
        }
    }
}
```
分析：
1、按照领域划分子状态，products 和 cart, 不同表中数据不能重复
2、state以键值对的形式保存数据，id/key作为索引
3、state中的数据不能相互依赖，小计金额可以通过price x quantity得出，不需要存放在state中，total同理。
4、商品栏数据通过 containId.map((id) => byId[ id] )取出。
5、触发事件Add To Cart时，products.byTd[ id ].inventory - 1, addedGoodId没有这个id则加入。quantityById 没有这个id则加入id作为属性，value = 1，有则value + 1

*The End*
*我还是一个初学者，有不足之处请见谅*