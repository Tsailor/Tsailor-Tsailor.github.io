---
  tags: React
  title: React高阶组件与参数传递
  date: 2020-08-11
---

### React高阶组件与参数传递###  

高阶组件是React中的重要概念，主要用来实现组件逻辑的抽象和复用。
高阶组件（简称HOC） 接收React组件作为参数，返回一个新的组件，高阶组件本质上是一个函数。

实例1，写一个高阶组价，用来打印自身的props：
```javascript
    import React , { Component } from 'react';

function Com(){
    let data = {
        name : "jack",
        age : 18
    }
    return(
        <WithLogProps value={data}/>
    )
}
const MyComponent = (props)=>{
    return(
        <div>
            我的名字：{props.value.name}
            我的年龄：{props.value.age}
        </div>
    )
}

const WithProps = (MyComponent)=>{
    return class extends Component {
        componentDidMount(){
            console.log(this.props)  // 为组件添加事件，打印props
        }
        render(){
            return(
               < MyComponent {...this.props}/>
            )
        }
    }
}
// WithProps是一个高阶组件, 但本质是个函数， WithLogProps 对应的就是 return 的那个组件。通过this.props拿到组件的value属性
const WithLogProps = WithProps(MyComponent) 
export default Com;
```

有时候也需要接受其他的参数。
```javascript
import React , { Component } from 'react';

function Com(){
    let data = {
        name : "jack",
        age : 18
    }
    return(
        <WithLogProps value={data}/>
    )
}
let data = "Tsailor"
const MyComponent = (props)=>{
    console.log(props)
    return(
        <div>
            我的名字：{props.value.name}
            我的年龄：{props.value.age}
            我的data :{props.data}
        </div>
    )
}

const WithProps = (data)=> (MyComponent)=>{  //  箭头函数自动return
    return class extends Component {
        componentDidMount(){
            console.log(data)   // 拿到这个data
            console.log(this.props)
        }
        render(){
            return(
               < MyComponent {...this.props} data={data}/>
            )
        }
    }
}
const WithLogProps = WithProps(data)(MyComponent)
export default Com;
```
