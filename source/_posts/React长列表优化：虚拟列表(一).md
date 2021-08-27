---
    title: React长列表优化：虚拟列表(一)
    tags: React
    date: 2020-08-24
---
### React长列表优化：虚拟列表(一) ###

单条表单高度固定的长列表，虚拟滚动

步骤1：新建server.js 返回10000+条数据
```javascript
const Koa = require("koa");
const Router = require('koa-router'); //路由模块
const app = new Koa();
const router = new Router();

```

<!--more-->

```javascript
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

    for (let i = 0; i < 10000; i++) {
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

步骤2：接受后端数据,并传入到子组件
```javascript
const useData = ()=>{
    return fetch('/api/getMock').then(res=>res.json())
}
function App(){
    let getListData = useData();
    const [ listDatas, setListDatas ] = useState([]);  //  所有数据
    useEffect(()=>{
        getListData.then(res=>setListDatas(res.data))
    },[])
    return(
       listDatas.length!==0 ? <VirtualList lists={listDatas} vitemHeight={50}/> : <div>正在加载ing...</div>
    )
}
```
步骤3：子组件布局
```javascript
<div className="list-view" ref = {listView} > 
    <div className="list-view-plantom" style={{height : `${totalLength*itemHeight}px`}}></div>
    <div className="list-content" ref = {listContent}>  
        {visibleData.map((item) => <LiItem item={item} itemHeight={itemHeight} key={item.id}/>)}
    </div>
</div>

.list-view {           
    margin-top: 100px;
    width: 400px;
    height: 600px;
    border: 1px solid lightgray;
    overflow-y: auto;
    position: relative;
    
}
.list-view-plantom {
    position: absolute;
    left: 0;
    top: 0;
    right: 0;
    z-index: -1;
}
.list-content {
    left: 0;
    right: 0;
    top: 0;
    bottom: 0;
    position: absolute;
}
.list-items {
    width: 100%;
    vertical-align: middle;
    text-align: center;
    list-style: none;
    border-bottom:1px solid black;
}
```
解释一下：最外层容器相对定位，里面一个plantom使用绝对定位并把高度设定为lists.length * itemHeight，使最外层容器出现滚动。里层的content容器绝对定位避免频繁的回流与重绘。
![virtualList.PNG](virtualList.PNG)
步骤3：VirtuaList组件
```javascript
const Li = (props) =>{
    let { item,itemHeight } = props;
    return <li className="list-items" style={{ lineHeight:`${itemHeight}px` , height:`${itemHeight}px`}} >{item.title}</li>
}
const LiItem = React.memo((props)=><Li {...props}/>);  // 避免不必要渲染

const VirtualList = (props)=>{
    let {lists, vitemHeight} = props;
    let totalLength = lists.length;  // 总list长度

    const listView = useRef(null);
    const listContent = useRef(null);

    let [visibleData, setVisibledata ] = useState([]);
    let itemHeight = vitemHeight|| 50;   // 每个listItem 高度
    let buffreSize = 5;
    
    const updateVisible = (scrollTop)=>{
        let curScrollTop = scrollTop || 0;
     //   console.log(listContent.current.clientHeight)
        let visibleCount = Math.ceil( listView.current.clientHeight / itemHeight);
        let start = Math.floor(curScrollTop/itemHeight);
        let end = start + visibleCount + buffreSize;    
        setVisibledata(lists.slice(start, end));               // 重新获取数据
        listContent.current.style.transform = `translate3d(0, ${start *itemHeight}px,0)`;   // content始终保证在可视区域
    }
    const handleScrol = (e)=>{
        const curScrollTop = listView.current.scrollTop;
        updateVisible(curScrollTop)
    }
    useEffect(()=>{
        updateVisible();
        listView.current.addEventListener("scroll",handleScrol)
        return ()=>listView.current.removeEventListener("scroll", handleScrol)
    },[])
    return(
        <div className="list-view" ref = {listView} >
             <div className="list-view-plantom" style={{height : `${totalLength*itemHeight}px`}}></div>
             <div className="list-content" ref = {listContent}>  
                    {visibleData.map((item) => <LiItem item={item} itemHeight={itemHeight} key={item.id}/>)}
             </div>
        </div>
      )
}

```
参考：
[再谈前端虚拟列表的实现](https://www.zhihu.com/search?q=%E8%99%9A%E6%8B%9F%E5%88%97%E8%A1%A8&utm_content=search_history&type=content)
[react-tiny-virtual-list](https://github.com/clauderic/react-tiny-virtual-list)
目前这个还很粗鄙，后续还会发布更多的版本
*the end*