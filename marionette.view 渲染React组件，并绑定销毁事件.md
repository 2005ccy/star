### 情境 S:
> 1. marionette.view 渲染React组件，并绑定销毁事件
### 任务 T:  
> 1. 使用示例代码
> 2. 代码实现逻辑
### 行动 A: 
> 1. 使用示例代码
> ```javascript
>    onShow() {
>       // 渲染组件到页面，并绑定销毁方法
>       new util.dom.Render(this, <MainPage companyId={ '287167' } />, '#relation-atlas-box');
>    },
> ```
> 2. 代码实现逻辑
> ```javascript
>import _ from 'lodash';
>import ReactDOM from 'react-dom';
>
>// 扩展 _ 方法
>Object.assign(window.util = window.util || {}, {
>    // 对dom的操作
>    dom: {
>        // 从Backbone View页面，渲染React组件到Dom；并自动处理资源回收
>        Render: class Render {
>
>            // 渲染构造方法
>            constructor(view, react, container) {
>                // 页面组件
>                this.view = view;
>                // 覆盖视图销毁方法
>                this.overrideViewDestory();
>                // 赋值react组件
>                this.react = react;
>                // 构建容器
>                this.buildContainer(container);
>                // 将组件渲染到页面
>                this.render();
>            }
>
>            // 覆盖视图的销毁方法
>            overrideViewDestory() {
>                // 当前对象别名
>                let _this = this;
>                // 设置销毁前回调
>                this.view.onBeforeDestroy = function() {
>                    // 销毁组件方法
>                    ReactDOM.unmountComponentAtNode(_this.container);
>                }
>            }
>
>            // 构建Dom容器
>            buildContainer(container) {
>                // 如果是字符串，选择器
>                if (_.isString(container)) {
>                    this.container = $(container)[0];
>                // 是一个jquery类型
>                } else if (container instanceof jQuery) {
>                    this.container = container[0];
>                // 是一个dom 类型
>                } else {
>                    this.container = container;
>                }
>            }
>
>            // 渲染组件到页面
>            render() {
>                ReactDOM.render(this.react, this.container);
>            }
>        }
>    }
>});
> ```

### 结果 R:
> 1. 只要负责使用，框架负责销毁
