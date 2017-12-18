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
> #### 2. 提供通用shouldComponentUpdate
>```javascript
>// 是否进行组件更新
>	shouldComponentUpdate(nextProps, nextState) {
>		// 遍历下次属性
>		for (let pk in nextProps) {
>			// 获取单个属性值
>			let pv = nextProps[pk];
>			// 获取当前属性值
>			let cpv = _.get(this, `props.${pk}`);
>			// 如果属性值不同
>			if (_.isObject(pv) ? !_.isMatch(cpv, pv) : pv !== cpv) {
>				// 刷新组件
>				return true;
>			}
>		}
>		// 遍历下次状态
>		for (let sk in nextState) {
>			// 获取单个状态值
>			let sv = nextState[sk];
>			// 获取当前状态值
>			let csv = _.get(this, `state.${sk}`);
>			// 如果状态值不同
>			if (_.isObject(sv) ? !_.isMatch(csv, sv) : sv !== csv) {
>				// 刷新组件
>				return true;
>			}
>		}
>		// 属性与状态值相同，不刷新组件
>		return false;
>	}
>```
> #### 3. 封装stateToProps模板方法 代码参看 app/component.js connect()方法
>```javascript
>import Component from 'component';
>export class Headline extends Component {
>	// 状态转属性数组
>	static stateToProps = {
>		headLine: 'headLine'
>	};
>}
>```
> #### 4. 封装封装dispatchToProps模板方法 代码参看 app/component.js connect()方法
>```javascript
>import Component from 'component';
>export class Headline extends Component {
>	// 状态转属性数组
>// 定义发布相关属性
>	static dispatchToProps = {
>		// 获取重点列表
>		getImportList: {
>			// ajax 访问路径
>			url: '/getImportList',
>			// 方法类型
>			method: 'get',
>			// 需要复制的reducer
>			reducer: 'importList',
>			// 使用的mock数据
>			mock: importListJson,
>			// 重命名，数据中的字段
>			rename: {
>				desc2: 'desc'
>			},
>			// 改变数据中的值
>			change: obj => _.set(obj, 'link', `https://${obj.link}`)
>		},
>		// 获取头条列表
>		getHeadList: {
>			url: '/getHeadList',
>			method: 'get',
>			reducer: 'headList',
>			mock: headListJson
>		}
>	};
>}
>```
> #### 5. 支持mock数据
>```javascript
> // 1. 在app/utils/mock 文件夹中配置 mock数据
> // 2. 在组件中引入mock数据
>	import { headListJson, importListJson, refreshHeadListJson } from 'utils/mock';
> // 3. 使用mock数据
> // 定义发布相关属性
>	static dispatchToProps = {
>		// 获取重点列表
>		getImportList: {
>			// ajax 访问路径
>			url: '/getImportList',
>			// 方法类型
>			method: 'get',
>			// 需要复制的reducer
>			reducer: 'importList',
>			// 使用的mock数据
>			mock: importListJson,
>		}
>	};
>```
> 单页系统，只是在替换网页的某部分
> 
> 其他操作还是能继续使用、交互体验更优
### 结果 R:
> #### 封装模板代码，提升开发效率
> #### 框架封装通用场景，提升开发效率
