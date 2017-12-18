### 情境 S:
> 1. 基于react-redux框架优化
> 2. 智能投顾开发小结
### 任务 T:  
> 1. 成员方法自动bind
> 2. 提供通用shouldComponentUpdate
> 3. 封装stateToProps模板方法
> 4. 封装dispatchToProps模板方法
> 5. 支持mock数据
> 6. ajax 透明支持indexedDB
> 7. antd-mobile由1.x 升级到 2.x
> 8. utils通用方法封装
> 9. highcarts 效果支持
> 10. 使用svg代替 jpg、png图片
> 11. offline-plugin 浏览器缓存资源，使用html5 Service-Worker、向下兼容ApplicationCache
> 12. 屏幕兼容解决方案
> 13. 开发的模块及页面
### 行动 A: 
> #### 1. 成员方法自动bind 代码参看 /app/component.js
>```javascript
> // 相关代码使用
> import Component from 'component';
> export default class PullList extends Component { 
>    // 所有成员方法自动bind
> }
>```
>这五步里面，第一步到第三步都非常快，耗时的是第四步和第五步。
>
>"生成布局"（flow）和"绘制"（paint）这两步，合称为"渲染"（render）。
![image](http://note.youdao.com/yws/public/resource/ab3a761e622c95399c376f66000eb692/xmlnote/WEB2f50ba7e90949a07865fc753179e7073/64E0E21E7EB548F097B52165A15B5293/14854)
> #### 2. 不能中断用户操作
> 如果是多页系统，进行页面切换，会重新执行上面的操作。
>
> 浏览器出现白屏、用户等待、体验很差
> #### 3. 单页系统的原理
> 单页系统，只是在替换网页的某部分
> 
> 其他操作还是能继续使用、交互体验更优
### 结果 R:
> 封装模板代码，提升开发效率
> 框架封装通用场景，提升开发效率
