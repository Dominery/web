# react-router

react-router包含3个库，react-router、react-router-dom和react-router-native。

react-router提供最基本的路由功能，实际使用的时候我们不会直接安装react-router，而是根据应用运行的环境选择安装react-router-dom（在浏览器中使用）或react-router-native（在rn中使用）。

react-router-dom和react-router-native都依赖react-router，所以在安装时，react-router也会自动安装。

## 基本使用

react-router中奉行一切皆组件的思想，路由器-Router、链接-Link、路由-Route、独占-Switch、重定向-Redirect都以组件形式存在。

```jsx
function RouterPage {
    return (
 		<div>
            <BrowserRouter>
                <Link to='/'>首页</Link>
                {/* exact实现精确匹配 */}
                <Switch> {/*独占路由，从上往下匹配，只返回一个*/}
                    <Route exact path='/' component={HomePage} />
                	<Route render={()=><h1>404</h1>}) // 没有path表示一定匹配
                </Switch>
            </BrowserRouter>
        </div>   	
    )
}
```

### Route渲染内容的三种方式

Route渲染优先级：children>component>render。三者能接收到同样的[route props]，包括match, location and history，但是当不匹配的时候，children的match为null。

children接收一个函数，该函数返回一个组件，不管是否匹配，这个组件在页面上一定会被渲染。如果使用了Switch，Route只有匹配了路由才会渲染，没匹配即使有children也不会渲染。

component如果接收的是一个返回组件的函数，每次更新时，函数引用不一样，组件会被卸载再挂载影响性能，这时需要使用render或者children。因为使用component，Route会用React.createElement和组件创建一个新的React Element。

### Router使用

#### 动态路由

使用`:id`形式定义动态路由。

```jsx
<Route path="/product/:id" compoenent={Product}/>
```

组件中获取路由参数。

```jsx
function Product({location, match}) {
    const {id} = match.params;
    return <h1>Production-{id}</h1>
}
```

## API

### Router

#### BrowserRouter

 BrowserRouter使用 HTML5 提供的 history API ( pushState , replaceState 和 popstate 事件) 来保持 UI 和 URL 的同步。

#### HashRouter

 HashRouter使用 URL 的 hash 部分（即 window.location.hash ）来保持 UI 和 URL 的同步。

#### MemoryRouter

把 URL 的历史记录保存在内存中的Router  （不读取、不写入地址栏）。在测试和非浏览器环境中 很有用，如React Native。

### Link

* to:string

  一个字符串形式的链接地址，通过 pathname 、 search 和 hash 属性创建

* to:object

  一个对象形式的链接地址，可以具有以下任何属性:

  * pathname - 要链接到的路径
  * search - 查询参数
  * hash - URL 中的 hash，例如 #the-hash
  * state - 存储到 location 中的额外状态数据

* replace:bool

  当设置为 true 时，点击链接后将替换历史堆栈中的当前条目，而不是添加新条目。默认为 false 。

* others

  还可以传递一些其他属性

### Redirect

## 实现

```jsx
const RouterContext = React.createContext();
const compoeteRootMatch = pathname => ({path: "/", url: "/", params: {}, isExact: pathname === "/"});
function Router({history, children}){
    const [location, setLocation] = useState(history.location);
    useLayoutEffect(()=>{
      	const unlistener = history.listen(location=>{
        	setLocation(location);
    	});
        return unlistener;
    },[history])
    
    return <RouterContext.Provider value={{
            history,
            location, 
            match: compoeteRootMatch(location.pathname)
        }}>
    {children}
    </RouterContext.Provider>
}

function BrowserRouter({children}){
    const history = createBrowserHistory();// history库
    return <Router history={history} children={children} />;
}

function Link({to, children, ...rest}){
    const {history} = useContext(RouterContext);
    const handleClick = e => {
        e.preventDefault();
        history.push(to);
    }
    return <a href={to} onClick={handleClick} {...rest}>{children}</a>
}

function Route(props){
    const context = useContext(RouterContext);
    const {location} = context;
    const {path} = props;
    const match = path ? matchPath(location.pathname, props) : context.match;
    const childProps = {
        ...context,
        location,
        match,
    };
    
    // match children > component > render > null
    // not match children null
    return // 使useMatch获取最新的match
    <RouterContext.Provider value={props}>
        {renderChild(match, props)}
    </RouterContext.Provider>
}

function renderChild(match, props) {
    const {children, component, render} = props;
    if (!match) {
        return typeof children === 'function' ? children(props) : null;
    }
    if (children) {
        return typeof children === 'function' ? children(props) : children;
    }
    if (component) return React.createElement(component, props);
    if (render) return render(props);
    return null;
}

function Switch({children}) {
    const {location, match : defaultMatch} = useContext(RouterContext);
    const container = children instanceof Array ? children : [children];
    let match;
    const child = container.find(child => {
        if(!React.isValidEment(child)) return false;
        match = child.props.path ? matchPath(location.pathname, child.props) : defaultMatch;
            return Boolean(match);}
       );
    
    return child ? React.cloneElement(child, {computedMatch: match}) : null;
}

function useHistoty() {
    return useContext(RouterContext).history;
}

function useLocation() {
    return useContext(RouterContext).location;
}

function useMatch() {
    return useContext(RouterContext).match;
}

const withRouter = WrappedComponent => props => {
    const context = useContext(RouterContext);
    return <WrappedComponent {...props} {...context}/>;
}
```



