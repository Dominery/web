# Redux使用

Redux是JavaScript应用的状态容器，保证程序行为一致性且易于测试。

## Redux

### createStore实现

Redux核心是createStore函数，该函数接收reducer返回一个包含getState、dispatch、subscribe方法的对象.

1. createStore创建store
2. reducer初始化，修改状态函数
3. getState获取状态值
4. dispatch提交更新
5. subscribe变更订阅

```javascript
function createStore(reducer, enhancer) {
    if (enhancer) return enhancer(createStore)(reducer);
    
    let currentState;
    const subscribers = [];
    
    function getState() {
        return currentState;
    }
    
    function dispatch(action) {
        currentState = reducer(currentState, action);
        subscribers.forEach(subscriber => subscriber());
    }
    
    function subscribe(subscriber) {
        subscribers.push(subscriber);
        
        // 取消订阅
        return ()=>{
            const index = subscribers.indexOf(subscriber);
            subscribers.splice(index, 1);
        }
    }
    
    // 赋初始值， type保证不与用户重复
    dispatch({ type: Date.now().toString() })
    
    return {
        getState,
        dispatch,
        subscribe,
    }
}

function combineReducers(reducers) {
    return (state = {},action) => {
        const nextState = {};
        let hasChanged = false;
        
        for(let key in reducers) {
            const reducer = reducers[key];
            nextState[key] = reducer(state, action);
            hasChanged = hasChanged || nextState[key] !== state[key];
        }
        hasChanged = hasChanged || Object.keys(state).length !== Object.keys(nextState).length;
        return hasChanged ? nextState : state;
    }
}
```

### 单向数据流模型

### 中间件

redux只能处理普通对象的action，如果action是函数、promise等类型，那么需要借助中间件。

如果action需要进行网络请求创建，那么称之为异步action。

中间件需要借助applyMiddleware函数强化dispatch，使用中间件执行一些操作后再调用原生dispatch。

* redux-thunk
* redux-promise
* redux-saga

#### applyMiddleware实现

```javascript
function applyMiddleware(...middlewares) {
    return createStore=>reducer=>{
        const store = createStore(reducer);
        const _dispatch = store.dispatch;
        const midApi = {
            getState: store.getState,
            dispatch: (action, ...args)=>_dispatch(action, ...args),
        };
        
        const middlewareChain = middlewares.map(middleware=>middleware(midApi));
        
        // 传入的_dispatch作为最后一个next执行
        const dispatch = compose(...middlewareChain)(_dispatch);
        
        return {
            ...store,
            dispatch,
        }
    }
}

function compose(...funcs){
    return (...args)=>{
     	if (funcs.length === 0) return args=>args;
        if (funcs.length === 1) return funcs[0];
        return funcs.reduce((func1, func2)=>func1(func2(...args)));
    }
}
```

#### 中间件实现

```javascript
function thunk({getState, dispatch}) {
    return next=>action=>{
        if (typeof action === 'function') {
            return action(dispatch, getState);
        }
        return next(action);
    }
}

function promise({getState, dispatch}) {
    return next => action => {
        return isPromise(action) ? action.then(dispatch) : next(action);
    }
}
```

## react-redux

在react中使用redux状态管理器需要手动创建、订阅，较为繁琐，react-redux提供了一种更为优雅的使用方式。

### provider

react-redux提供了Provider组件，将容器传给其store属性，包裹内的组件可以访问dispatch和getState方法。

### connect

通过connect高级组件，能够实现给当前组件在props中添加dispatch方法。connect有三个参数。

第一次参数为一个函数mapStateToProps，该函数入参为state并返回对象，这个对象的属性会合并到props中。mapStateToProps有可选的第二个参数是组件自身的props，如果指定这个参数，只要组件接收新的props，mapStateToProps会被重新调用，影响性能。

第二个参数可以是一个对象或者函数。

* 对象的属性都是方法，方法返回一个action，这些方法会被添加到props中替代dispatch
* 函数mapDispatchToProps，这个函数入参是dispatch，返回一个对象，对象中的方法会被添加到props。mapDispatchToProps也有可选的第二个参数，与mapStateToProps相同。

第三个参数是一个函数mergeProps，如果指定这个参数，mapStateToProps、mapDispatchToProps的执行结果和自身props会作为参数传给mergeProps，返回一个对象作为新的props。

```javascript
@connect(
	({count}) => ({count}),
    /*{
        add: () => ({type: 'ADD'}),
    }*/
    (dispatch) => {
        let creators = {
            add: ()=> ({type:'ADD'}),
        }
        creators = bindActionCreators(creators, dispatch);
        return {dispatch, ...creators};
    }
)
class Demo extends React.Component {
    render() {
        return <div></div>;
    }
}
```

### hook方法

```javascript
function ReactReduxHook(props) {
    const count = useSelector(({count}) => count);
    const dispatch = useDispatch();
    const add = () => {
        dispatch({type: 'ADD'});
    };
    
    return (
        <div>
        	<p>{count}</p>
        	<button onClick={add}>add</button>
		</div>
    )
}
```

### 代码实现

```jsx
const Context = React.createContext();

function Provider({store, children}) {
    return <Context.Provider value = {store}>{children}</Context.Provider>;
}

function mergeDispatch(mapDispatchToProps, dispatch) {
    if (typeof mapDispatchToProps === 'function') return mapDispatchToProps(dispatch);
    if (typeof mapDispatchToProps === 'object')return bindActionCreators(mapDispatchToProps, dispatch);
    return { dispatch };
}

const connect = (mapStateToProps, mapDispatchToProps) => WrappedComponent => props => {
    const store = useContext(Context);
    const stateProps = mapStateToProps(store.getState());
    const dispatchProps = mergeDispatch(mapDispatchToProps, store.dispatch);
    
    const forceUpdate = useForceUpdate();
    
    // 使用useEffect时，可能存在在延迟阶段状态变更了，但组件没有订阅因而没更新的问题
    useLayoutEffect(() => {
        return store.subscribe(forceUpdate);
    }, [store])
    
    
    return <WrappedComponentCompoent {...props} {...stateProps} {...dispatchProps}/>
        }
    
}

function useSelector(selector) {
    const store = useContext(Context);
    const forceUpdate = useForceUpdate();
 
    useLayoutEffect(() => {
        return store.subscribe(forceUpdate);
    }, [store])
    return selector(store.getState());
}

function useDispatch() {
    const store = useContext(Context);
    return store.dispatch;
}

function useForceUpdate(){
    const [,forceUpdate] = useReducer(x => x + 1, 0);
    // 使用useCallback防止参数传递导致多余的渲染
    return useCallback(forceUpdate, []);
}


function bindActionCreator(creator, dispatch) {
    return (...args) => dispatch(creator(...args));
}


function bindActionCreators(creators, dispatch) {
    let newCreators = {};
    
    for (let key in creators) {
        newCreators[key] = bindActionCreator(creators[key], dispatch);
    }
    
    return newCreators;
}
```

## redux-saga

redux-saga是一个用于管理应用程序副作用的库。sagas使用Generator函数实现，Generator函数里yield JavaScript对象表达saga逻辑，这些对象称为Effect。

redux-saga使用主要有以下三个步骤：

1. 调用异步操作 call fork，call 阻塞，fork非阻塞
2. 状态更新 put，背后使用dispatch
3. 监听 takeEvery



### 异步请求方案比较

|               | 优点                   | 缺点                                 |
| ------------- | ---------------------- | ------------------------------------ |
| redux-thunk   | 简单易用               | 不适合处理复杂嵌套，容易形成嵌套地狱 |
| thunk + async | 简单，没有嵌套地狱问题 | 没有generator强大                    |
| saga          | 功能强大               | 麻烦                                 |



### 基本使用

```javascript
const sagaMiddleware = createSagaMiddleware();
const store = createStore(combineReducers({user: loginReducer}),
                          applyMiddleware(sagaMiddleware)
                         );
sagaMiddleware.run(mySaga);

// watcher saga
function* mySaga(props) {
    yield takeEvery('login', loginHandle);
}

// worker saga
function* loginHandle(action) {
    const res = yield call(UserService.login, {name: 'Bob'});
    yield put({type: 'LOGIN_SUCCESS'});
    console.log(action);
}
```

### 实现

```javascript
function* takeEvery(SAGA, handle) {
    while (true) {
        const action = yield take(SAGA);
        yield call(handle, action);
    }
}

const makeEffect = (type, payload) => ({[IO]: IO, type, payload});

function take(pattern) {
    return makeEffect(effectTypes.TAKE, {pattern});
}

function put(action) {
    return makeEffect(effectTypes.PUT, {action});
}

function call(fn, ...args) {
    return makeEffect(effectTypes.CALL, {fn, args});
}

function fork(fn, ...args) {
    return makeEffect(effectTypes.FORK, {fn, args});
}

function all(sagas) {
    return makeEffect(effectTypes.ALL, sagas);
}
```

#### createSagaMiddleware

```javascript
function createSagaMiddleware() {
    let boundRunSaga;
    const channel = stdChannel();
    function sagaMiddleware({getState, dispatch}) {
        boundRunSaga = runSaga.bind(null, {getState, dispatch, channel});
        return next => action => {
            const result = next(action);
            channel.put(action);
            return result;
        }
    }
    
    sagaMiddleware.run = (...args) => boundRunSaga(...args);
    return sagaMiddleware;
}

function runSaga(env, saga, ...args) {
    const iterator = saga(...args);
    proc(env, iterator);
}

function proc(env, iterator, cb) {
    
    next();
    
    function next(arg, err) {
        let result;
        if (err) {
            iterator.throw(err);
        } else {
            result = iterator.next(arg);
        }
        
        if (!result.done) {
            digestEffect(result.value, next);
        } else {
            cb?.();
        }
    }
    
    function digestEffect(effect, cb) {
        let effectSettled = false;
        function curCb(arg, err) {
            if (effectSettled) return;
            effectSettled = true;
            cb(arg, err);
        }
        
        runEffect(effect, curCb);
    }
    
    function runEffect(effect, curCb) {
        if (!effect || !effect[IO]) {
            curCb();
            return;
        }
        const effectRunner = effectMap[effect.type];
        effectRunner(env, effect.payload, curCb);
    }
}
```

#### effectRunner

```javascript
function takeEffectRunner(env, {channel = env.channel, pattern}, cb) {
    const matcher = input => input === pattern;
    channel.take(cb, matcher);
}

function putEffectRunner(env, { action}, cb) {
    const reuslt = env.dispatch(action);
    cb(result);
}

function callEffectRunner(env, {fn, args}, cb) {
    const result = fn.apply(null, args);
    if (isPromise(result)) {
        result.then(data => cb(data), err => cb(err, true));
        return;
    }
    if (isIterator(result)) {
        proc(env, result, cb);
        return;
    }
    cb(result);
}

function forkEffectRunner(env, {fn, args}, cb) {
    proc(env, fn.apply(null, args));
    cb();
}

function allEffectRunner(env, effects, cb) {
    const len = effects.length;
    for (int i = 0; i < len; i++) {
        proc(env, effects[i])
    }
}

function stdChannel() {
    const currentTakers = [];
    
    function take(cb, matcher) {
        cb[MATCH] = matcher;
        currentTakers.push(cb);
    }
    
    function put(input) {
        const takers = currentTakers;
        for (let i = 0, len = takers.length; i < len; i++) {
            const taker = takers[i];
            if (taker[MATCH](input)) {
                taker(input);
            }
        }
    }
    
    return {
        take,
        put,
    }
}
```

