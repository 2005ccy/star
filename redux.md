# Redux 未必适合您
### 情境 S:
> 1. Redux大家都在用，我不用是不是不入流
> 2. Redux大家都说好，我说不好是不是水平不够
> 2. 为了获得一点好处，这样的代价值吗
> 3. 下面我们来聊一聊
### 任务 T:  
> 1. Redux解决的问题
> 2. Redux使用场景
> 3. Redux设计思想
> 4. 谈谈我的看法
> 5. 相同能力，代码比较
### 行动 A:
> #### 1. Redux解决的问题
>```javascript
> 1. 代码结构，组织方式
> 2. 组件之间的通信
>```
> #### 2. Redux使用场景
>```javascript
> 1. 某个组件的状态，需要共享
> 2. 某个状态需要在任何地方都可以拿到
> 3. 一个组件需要改变全局状态
> 4. 一个组件需要改变另一个组件的状态
>```
> #### 3. Redux设计思路
>```javascript
> 1. Web应该是一个状态机，视图与状态是一一对应的。
> 2. 所有的状态，保存在一个对象里面
>```
> #### 4. 谈谈我的看法
>```javascript
> 1. 代码组织是有了，但是代码量翻翻啦
> 2. 组件是能通信啦，但复杂度上去啦
> 3. 所有的状态，保存在一个对象，不觉得有问题吗？
>    React区别于其它框架，就是“最小粒度”DOM变更，带来的性能提升
>    现在让所有组件渲染一个全新状态，怎么让不该刷新的不刷新？头又大了
>```
> #### 5. 完成相同功能，代码比较
> + 向页面渲染一个“计算器” /index.js
>```javascript
> import React from 'react'
> import ReactDOM from 'react-dom'
> import { createStore } from 'redux'
> import Counter from './components/Counter'
> import counter from './reducers'
> 
> const store = createStore(counter)
> const rootEl = document.getElementById('root')
> 
> const render = () => ReactDOM.render(
>   <Counter
>     value={store.getState()}
>     onIncrement={() => store.dispatch({ type: 'INCREMENT' })}
>     onDecrement={() => store.dispatch({ type: 'DECREMENT' })}
>   />,
>   rootEl
> )
> 
> render()
> store.subscribe(render)
>```
> + “计算器”组件 /components/Counter.js
>```javascript
> import React, { Component, PropTypes } from 'react'
> 
> class Counter extends Component {
>   static propTypes = {
>     value: PropTypes.number.isRequired,
>     onIncrement: PropTypes.func.isRequired,
>     onDecrement: PropTypes.func.isRequired
>   }
> 
>   incrementIfOdd = () => {
>     if (this.props.value % 2 !== 0) {
>       this.props.onIncrement()
>     }
>   }
> 
>   incrementAsync = () => {
>     setTimeout(this.props.onIncrement, 1000)
>   }
> 
>   render() {
>     const { value, onIncrement, onDecrement } = this.props
>     return (
>       <p>
>         Clicked: {value} times
>         {' '}
>         <button onClick={onIncrement}>
>           +
>         </button>
>         {' '}
>         <button onClick={onDecrement}>
>           -
>         </button>
>         {' '}
>         <button onClick={this.incrementIfOdd}>
>           Increment if odd
>         </button>
>         {' '}
>         <button onClick={this.incrementAsync}>
>           Increment async
>         </button>
>       </p>
>     )
>   }
> }
> 
> export default Counter
>```
> + 计算控制 /reducers/index.js
>```javascript
> export default (state = 0, action) => {
>   switch (action.type) {
>     case 'INCREMENT':
>       return state + 1
>     case 'DECREMENT':
>       return state - 1
>     default:
>       return state
>   }
> }
>```
> + 美好的生活应该是这样的
>```javascript
> import React from 'react'
> import ReactDOM from 'react-dom'
> const rootEl = document.getElementById('root')
> 
> class Counter extends Component {
>   state= {
>     value: 0
>   };
>
>   incrementIfOdd = () => {
>     if (this.props.value % 2 !== 0) {
>       this.onIncrement()
>     }
>   }
> 
>   incrementAsync = () => {
>     setTimeout(this.onIncrement, 1000)
>   }
>
>   onIncrement() {
>       this.setState({value: this.state.value + 1});
>   }
>
>   onDecrement() {
>       this.setState({value: this.state.value - 1});
>   }
> 
>   render() {
>     return (
>       <p>
>         Clicked: {this.state.value} times
>         {' '}
>         <button onClick={this.onIncrement}>
>           +
>         </button>
>         {' '}
>         <button onClick={this.onDecrement}>
>           -
>         </button>
>         {' '}
>         <button onClick={this.incrementIfOdd}>
>           Increment if odd
>         </button>
>         {' '}
>         <button onClick={this.incrementAsync}>
>           Increment async
>         </button>
>       </p>
>     )
>   }
> }
>
> const render = () => ReactDOM.render(
>   <Counter/>,
>   rootEl
> )
> 
> render()
>```
> + 等等这是React写法，没有实现“组件之间的通信”；那我们再加点框架代码
>```javascript
> // 导入react 组件
> import React, { Component } from 'react';
> // 使用页面刷新时，数据比较
> import shallowEqual from 'fbjs/lib/shallowEqual';
> // 生成对象hash 字符串
> import hash from 'object-hash/dist/object_hash.js';
> // 事件发布订阅
> import PubSub from 'pubsub-js';
> 
> // 高级定制组件类 【作者：陈朝阳】
> class DsComponent extends Component {
> 
>     // 每个组件都有唯一标识，一般用于组件id <div id={this.id}>...</div>
>     id = _.uuid();
> 
>     // 组件构造方法
>     constructor(props) {
>         // 调用 'React.Component' 构造方法
>         super(props);
>         // 扩展组件状态属性
>         this.extendState();
>         // 覆盖组件生命周期方法，添加全局控制逻辑
>         this.overrideLifecycle();
>         // 将所有方法，绑定this
>         this.autoBind();
>     }
> 
>     // 扩展主键状态属性
>     extendState() {
>         // 设置初始化 state 值
>         this.state = Object.assign({
>             hash: 'init', // 定义初始hash 值
>             visible: true, // 定义组件在可视区域，只处理可视区组件；提升性能 
>         }, this.state);
>     }
> 
>     // 覆盖组件生命周期方法
>     overrideLifecycle() {
> 
>         // 定义一个空函数
>         let empty = () => {
>         };
> 
>         // 页面挂载，触发的生命周期方法
>         // Mounting: Class constructor -> componentWillMount -> render -> componentDidMount
> 
>         // 组件将要被加载到页面
>         this._componentWillMount = this.componentWillMount || empty;
>         this.componentWillMount = this.overrideComponentWillMount;
>         // 组件已经加载到页面
>         this._componentDidMount = this.componentDidMount || empty;
>         this.componentDidMount = this.overrideComponentDidMount;
> 
>         // 页面卸载，触发的生命周期方法
>         // Unmounting: componentWillUnmount
> 
>         // 组件将要从页面移除
>         this._componentWillUnmount = this.componentWillUnmount || empty;
>         this.componentWillUnmount = this.overrideComponentWillUnmount;
> 
>         // 组件属性更新，触发生命周期方法
>         // Props Changes: componentWillReceiveProps -> shouldComponentUpdate -> componentWillUpdate -> render -> componentDidUpdate
>         // 组件状态更新，触发生命周期方法
>         // State Changes: shouldComponentUpdate -> componentWillUpdate -> render -> componentDidUpdate
> 
>         // 组件改变状态方法
>         this._setState = this.setState;
>         this.setState = this.overrideSetState;
>         // 组件将要接收新属性
>         this._componentWillReceiveProps = this.componentWillReceiveProps || empty;
>         this.componentWillReceiveProps = this.overrideComponentWillReceiveProps;
>         // 组件将要更新，通过该方法，返回true：允许组件更新；返回false：拒绝组件更新
>         this._shouldComponentUpdate = this.shouldComponentUpdate;
>         this.shouldComponentUpdate = this.overrideShouldComponentUpdate;
>         // 组件更新前回调函数
>         this._componentWillUpdate = this.componentWillUpdate || empty;
>         this.componentWillUpdate = this.overrideComponentWillUpdate;
>         // 组件更新完成
>         this._componentDidUpdate = this.componentDidUpdate || empty;
>         this.componentDidUpdate = this.overrideComponentDidUpdate;
>     }
> 
>     // 组件将要被加载到页面
>     overrideComponentWillMount() {
>         if (!this.name) {
>             throw new Error('组件必须name属性')
>         }
>         // 全局缓存组件
>         _.setComponent(this.name, this);
>         // 执行子类回调
>         this._componentWillMount();
>     }
> 
>     // 组件已经加载到页面
>     overrideComponentDidMount() {
>         // 可以添加的逻辑： 1. 判断组件是否在可视区域, 如果不在，只渲染<div></div>, 停止ajax请求来 提升性能
> 
>         // 执行子类回调
>         this._componentDidMount();
>     }
> 
>     // 组件将要从页面移除
>     overrideComponentWillUnmount() {
>         // 当前组件已被卸载
>         this.isUnMount = true;
>         // 移除全局组件缓存
>         _.removeComponent(this.name);
>         // 清除相关订阅
>         this.clearPubSub();
>         // 执行子类回调
>         this._componentWillUnmount();
>     }
> 
>     // 组件改变状态方法, 如果param数组编号, hash将被改变
>     overrideSetState(nextState) {
>         try {
>             // 为扩展属性后的对象，生成hash值
>             let no = Object.assign({}, this.state, nextState);
>             // 删除原有的hash
>             delete no.hash;
>             // 计算新的hash 值
>             nextState.hash = hash(no);
>         } catch ( e ) {}
>         // 如果组件已卸载，退出设置状态
>         if (_.isUnMount(this)) {
>             return;
>         }
>         // 设置组件状态
>         this._setState(nextState);
>     }
> 
>     // 组件将要接收新属性
>     overrideComponentWillReceiveProps(nextProps) {
>         // 添加全局，控制逻辑
>         // 执行子类回调
>         this._componentWillReceiveProps(nextProps);
>     }
> 
>     // 组件将要更新，通过该方法，返回true：允许组件更新；返回false：拒绝组件更新
>     overrideShouldComponentUpdate(nextProps, nextState) {
>         // 存在子类覆盖方法，则执行子类逻辑
>         if (this._shouldComponentUpdate) {
>             return this._shouldComponentUpdate(nextProps, nextState);
>         }
>         // 默认属性比对，状态比对
>         return !shallowEqual(this.props, nextProps) || !shallowEqual(this.state, nextState);
>     }
> 
>     // 组件更新前回调函数
>     overrideComponentWillUpdate(nextProps, nextState) {
>         // 添加全局，控制逻辑
>         // 执行子类回调
>         this._componentWillUpdate();
>     }
> 
>     // 组件更新完成
>     overrideComponentDidUpdate(prevProps, prevState) {
>         // 发出属性改变事件
>         if (!shallowEqual(this.props, prevProps)) {
>             this.publishProps(this.props);
>         }
>         // 发出状态改变事件
>         if (!shallowEqual(this.state, prevState)) {
>             this.publishState(this.state);
>         }
>         // 执行子类回调
>         this._componentDidUpdate();
>     }
> 
>     // 绑定方法数组
>     bind(methods) {
>         methods.forEach(method => {
>             this[method] = this[method].bind(this);
>         });
>     }
> 
>     // 执行类的所有方法绑定
>     autoBind() {
>         this.bind(
>             Object.getOwnPropertyNames(this.constructor.prototype)
>                 .filter(prop => typeof this[prop] === 'function')
>         );
>     }
> 
>     // 获取某个组件
>     get(name) {
>         return _.getComponent(name);
>     }
> 
>     // 订阅其他组件
>     subscribeArr = [];
> 
>     // 清除相关订阅
>     clearPubSub() {
>         // 清除组件自身订阅
>         PubSub.unsubscribe(this.name);
>         // 销毁对其他组件的订阅
>         for (let i in this.subscribeArr) {
>             PubSub.unsubscribe(this.subscribeArr[i]);
>         }
>     }
> 
>     // 发布属性变更 触发 subscribe、subscribeProps
>     publishProps(props) {
>         PubSub.publish(`${this.name}.props`, props);
>     }
> 
>     // 发布属性变更，触发 subscribe、subscribeState
>     publishState(state) {
>         PubSub.publish(`${this.name}.state`, state);
>     }
> 
>     // 组件事件订阅，公共方法
>     _subscribe(key, callback) {
>         // 如果回调函数存在
>         if (_.isFunction(callback)) {
>             // 发起组件订阅
>             let sub = PubSub.subscribe(key, callback);
>             // 记录订阅对象，用于销毁处理
>             this.subscribeArr.push(sub);
>         }
>     }
> 
>     // 订阅某组件，属性、状态变更
>     subscribe(name, callback) {
>         this._subscribe(name, callback);
>     }
> 
>     // 订阅某组件，属性变更
>     subscribeProps(name, callback) {
>         this._subscribe(`${name}.props`, callback);
>     }
> 
>     // 订阅某组件，状态变更
>     subscribeState(name, callback) {
>         this._subscribe(`${name}.state`, callback);
>     }
> }
> // 定义为全局组件
> window.DsComponent;
>```
> + 框架代码 + 注释为 240行，我们现在让上面的“计算器”具有组件通信的能力，修改如下：
>```javascript
> // 1. 组件extends DsComponent
> class Counter extends DsComponent {
>    // 2. 定义name 属性
>    name='counter';
>
>    // 组件加载到页面，回调方法
>    componentDidMount() {
>       // 3. 读取其他组件状态数据
>       this.get('header').state
>       // 4. 改变其他组件值
>       this.get('header').setState({key: 'new value'});
>       // 5. 订阅其他组件变化
>       this.subscribe('header', (val) => {
>           this.setState({
>               userImg: val.userImg 
>           });
>       });
>   }
>```
### 结果 R:
> + 上面的代码实现组件通信
> + 代码量基本没有增长
> + 复杂度没有增长
> + 增强组件全局控制力
> + 关系到开发效率，请大家拍砖
