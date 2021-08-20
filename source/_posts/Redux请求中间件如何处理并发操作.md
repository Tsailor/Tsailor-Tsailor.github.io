---
    title: Redux请求中间件如何处理并发操作？
    tags: React
    date: 2020-09-18
---
## Redux请求中间件如何处理并发操作？ ##

问题提出背景：有一个数组A，然后让你根据数组A的每一项发起一个请求，拿到所有项的返回结果后，组装起来渲染视图

思路：利用redux-thunk中间件来操作，redux-thunk使得 dispatch()得到增强，能接收一个函数，在这个函数里使用promise.all()执行完异步操作后，再dispatch一个action对象。
<!--more-->
```javascript
//App.js
import React  from 'react';
import {connect} from 'react-redux';
import { getLists} from './reducer';
import { requestLists } from './action';
function App(props){
    const value = ["user01","user02","user03"]    // 假设列表测试数据
    const { lists, requestLists } = props;
    const handleClick = (value)=>{
      requestLists(value)
    }
    return(
      <div>
          <button onClick = {()=>handleClick(value)}>点击获取列表view </button>
              { lists && lists.map((list) =>(<li key={list.name}>{list.name}</li>))}
      </div>
    )
}
const mapStateToProps = (state) =>({
    lists:getLists(state)
})
const mapDispatchToProps = (dispatch) =>({
    requestLists: requestLists(dispatch)
})
export default connect(
    mapStateToProps,
    mapDispatchToProps
)(App);
```
再来看看 action.js
```javascript
// action.js
import axios from "axios";
const requreAllLists = (value) =>(dispatch)=>{  // 这个dispatch是applyMiddlewares传递进去的增强后的dispatch
    axios.all( value.map((v)=>{                    // 并发
        return axios.get(`/users`,{ params: {
            ID: v
          }})
    })).then(axios.spread(function (...args) {
        let data = args.map((v)=>v.data)
        dispatch({
            type: "RECELIVED_ALL",
            data    // 这里就是将所有返回结果送至store
        })
  }));
}

export const requestLists = (dispatch) =>(value)=>{
    dispatch(requreAllLists(value))      //  redux-thunk能接收异步函数
}
```
再新建一个server.js服务,用来处理响应
```javascript
const Koa = require('Koa');
const Router = require('koa-router')
const { URL } = require('url');
const app = new Koa();
const router = new Router();
let  base = 'http://localhost:4000/';

router.get('/',async ctx =>{
    ctx.body = "hello, ok"
})
router.get('/users',async ctx =>{
    let myUrl = new URL( ctx.url, base );
    //console.log(myUrl.searchParams.get("ID"))
    let id = myUrl.searchParams.get("ID")
    ctx.body = "name"+id
})
app.use(router.routes())
app.listen(4000)
```
  