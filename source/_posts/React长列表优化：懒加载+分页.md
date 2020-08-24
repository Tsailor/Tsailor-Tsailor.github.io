---
   title: React长列表优化：懒加载+分页 
   tags: React
   date: 2020-08-21
---

### React长列表优化：懒加载+分页 ###

背景：React项目中渲染一个长列表，后端返回10000+条数据。如果全部渲染出来，耗时即长，长时间白屏。
使用懒加载+分页的方式，每次滚动到底部，加载下一页数据。

步骤1：启动一个后台服务，返回10000条数据，这里用koa.js来做
```javascript
const Koa = require("koa");
const Router = require('koa-router'); //路由模块
const app = new Koa();
const router = new Router();
router.get('/api/getMock',async ctx =>{
    let list = [];
    function getRandomWords(n) {
        let words1 = "但使龙城飞将在，不教胡马度阴山0123456789";
        let ret = '';
        let len = words1.length;
        for (let i = 0; i < n; i++) {
            ret += words1[Math.floor(Math.random() * len)]
        }
        return ret;
    }
    for (let i = 0; i < 100000; i++) {
        list.push({
            title: getRandomWords(12),
            id: `id-${i}`,
        })
    }
    ctx.body = {
        state: 200,
        data: list
    }
})
router.get('/test',async ctx=>{
    ctx.body="ok"
})
app.use(router.routes());
app.listen(8080)
```
由于React项目是以服务的方式启动的，所以配置package.json，添加` "proxy": "http://localhost:8080"` 
<!--more-->
或者使用CORS跨域来做,二选一即可
```javascript
const cors = require('koa2-cors'); //跨域处理
app.use(
    cors({
        origin: function(ctx) { //设置允许来自指定域名请求
            if (ctx.url === '/test') {
                return '*'; // 允许来自所有域名请求
            }
            return 'http://localhost:3000'; //只允许http://localhost:3000这个域名的请求
        },
      
      //  credentials: true, //是否允许发送Cookie
        allowMethods: ['GET', 'POST', 'PUT', 'DELETE', 'OPTIONS'], //设置所允许的HTTP请求方法
        allowHeaders: ['Content-Type', 'Authorization', 'Accept'], //设置服务器支持的所有头信息字段
        exposeHeaders: ['WWW-Authenticate', 'Server-Authorization'] //设置获取其他自定义字段'
    })
);
```
步骤2：App.js里使用useState() 将所有的数据放入state
```javascript
const usedata = ()=>{
     return fetch('/api/getMock').then(res => res.json()) 
      //  return fetch('http://localhost:8080/api/getMock').then(res => res.json())   CORS跨域
}

function App() {
  let [list, setList ] = useState([]);    // 全部数据
  let [page, setPage ] = useState(1);  //  第一次展示 第一页的数据
  let getData  = usedata();

  useEffect(()=>{     //  第一次挂载的时候，请求数据
    getData.then(res=>{
      setList(res.data)
    })
   
  },[])
  const getNextPage = ()=>{
     setPage((preState)=>preState+1)
  }
  return (
    <div className="App">
         <div className="title">长列表优化：懒加载+分页</div> 
         {list.length!== 0 ? <List data = {list.slice(0,page*20)} handleNextPage={getNextPage} curPage ={page}/> :null}
    </div>
  );
}
```
步骤3：List组件，防抖优化
```JavaScript
const List = (props) => {
    let { data, handleNextPage, curPage } = props;
    useEffect(()=>{
      document.querySelector(".App").addEventListener("scroll", debounce(fn, 300));

      return ()=>document.querySelector(".App").removeEventListener("scroll",debounce(fn, 300))
    },[data])

    function debounce(fn, wait){     //  防抖 。 非立即执行版本
      let timer;
      return function(...args){   
          let context = this;
          if(timer)
             clearTimeout(timer)
          timer = setTimeout(
            ()=>fn.call(context,...args) , wait
          )          
      }
    }

    function fn(){
      // const maxScrollTop = document.querySelector(".App").scrollHeight
      // const innerHeight= document.querySelector(".App").offsetHeight;
      
      const currentScrollTop = document.querySelector(".App").scrollTop;   //  滚动元素上面超出的距离
      const maxScrollTop = document.querySelector(".App").scrollHeight - document.querySelector(".App").offsetHeight;     
        //  滚动页面长度 - 此容器高度 =  滚动元素上面超出的距离 + 底部未出现的高度

      if (maxScrollTop - currentScrollTop < 20) {
          handleNextPage()    //  请求下一页
      }
    }
    return (
      <div className="list-panel">
        { data.map((list)=><Li key={list.id} list={list} />) }
      </div>
    )  
} 
const LiItem = ({list})=><li className="listitem" >{list.title}</li>

const Li = React.memo( LiItem )   //  React.memo包裹子组件，避免不必要的渲染

```
总结：此案例中用到了前端用到了 React hooks 、防抖、懒加载、React.memo(), 后台用到了koa.js CORS跨域。
