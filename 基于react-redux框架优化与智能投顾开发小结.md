### 情境 S:
> 1. 基于react-redux框架优化
> 2. 智能投顾开发小结
### 任务 T:  
> 1. 成员方法自动bind
> 2. 提供通用shouldComponentUpdate
> 3. 封装stateToProps模板方法
> 4. 封装dispatchToProps模板方法
> 5. 使用reducer相关数据
> 6. 支持mock数据
> 7. ajax 透明支持indexedDB
> 8. antd-mobile由1.x 升级到 2.x
> 9. utils通用方法封装
> 10. highcarts 效果支持
> 11. 使用svg代替 jpg、png图片
> 12. offline-plugin 浏览器缓存资源，使用html5 Service-Worker、向下兼容ApplicationCache
> 13. 屏幕兼容解决方案
> 14. 开发的模块及页面
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
> ####  5. 使用reducer相关数据
>```javascript
>// 1.使用模板方法，生成reducer数据
>import Component from 'component';
>export class Headline extends Component {
>	// 状态转属性数组
>	// 设置reducerTypes类型
>	static setReducerTypes() {
>		this._setReducerTypes(
>			'app/SmartNews/Headline',
>			'headList',
>			'importList'
>		);
>	}
>}
> // 2. 导出模板数据
>import { Headline } from './Headline';
>import ThemeList from './Theme/ThemeList';
>import ThemeDetail from './Theme/ThemeDetail';
>import Announcement from './Announcement';
>import Aggregation from './Announcement/Aggregation';
>
>export default {
>  themes: ThemeList.reducer,
>  themeDetail: ThemeDetail.reducer,
>  headLine: Headline.reducer,
>  announcement: Announcement.reducer,
>  aggregation: Aggregation.reducer,
>};
>```
> #### 6. 支持mock数据、非开发环境，使用mock警告
>```javascript
> // 1. 在app/utils/mock 文件夹中配置 mock数据
> // 2. 在组件中引入mock数据
>	import { headListJson, importListJson, refreshHeadListJson } from 'utils/mock';
> // 3. 使用mock数据
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
> #### 7. ajax 透明支持indexedDB 代码参看： app/utils/ajax.js
>```javascript
> // 1. 读取浏览器indexedDB url缓存数据，并刷新组件
> // 2. 读取url ajax相关数据
> // 3. 比对 indexedDB数据 与 ajax数据，不同刷新组件、更新indexedDB数据
>```
> #### 8. antd-mobile由1.x 升级到 2.x
>```javascript
> // 1. 兼容antd-mobile 1.x组件，参看 app/antd-mobile-wrap 文件夹
> // 2. 扩展antd-mobile 2.x组件，请放置 app/antd-mobile-wrap 文件夹
>```
> #### 9. utils通用方法封装 代码参看 app/utils/base 文件夹
>```javascript
> // 1. core.js 通用方法
> //    getDate 时间相关、call 函数调用、uuid 随机字符串、includes lodash扩展方法、override 扩展现有方法、attrRename 属性重命名
> // 2. dom.js DOM元素方法
> //    jqueryDidMount 元素挂载成功，支持轮询检查 focusStyle 模拟获得焦点效果
> // 3. hammer.js 触屏操作相关类，优化方向精确度
> // 4. indexedDB 浏览器数据库操作
> // 5. is.js 相关逻辑判断
> //    isDev 开发环境、 isUnMount 是否卸载
>```
> #### 10. highcarts 效果支持
>```javascript
> // 1. 雷达图背景、半圆仪表盘、行业配置饼图
> // 2. 理解svg规范
>```
> #### 11. 使用svg代替 jpg、png图片 参看 app/utils/svgs 文件夹
>```javascript
> // 1. 可以控制颜色、大小、减少UI依赖
> // 2. 理解svg规范
>```
> #### 12. offline-plugin 浏览器缓存资源，使用html5 Service-Worker、向下兼容ApplicationCache
>```javascript
> // 1. 理解Service-Worker技术、理解老标准ApplicationCache
>```
> #### 13. 屏幕兼容解决方案
>```javascript
> // 1. 引入修改 html{font-size}组件 app/utils/flexible.js 可修改宽度
> // 2. 按住UI标注宽度，基于iphone6 375px;
> // 3. 如UI标注 120dp，请使用 1.2rem 相关宽度
>```
> #### 14. 开发的模块及页面
>```javascript
> // 1. 通联小智：交互优化、输入框轮询信息、忽略前后空格
> // 2. safari确认窗口，获得焦点效果
> // 3. 聚合页面编写
> // 4. 智能资讯：头条页面、走马灯组件、数据列表组件、推荐页面
>```
### 结果 R:
> #### 封装模板代码，提升开发效率
> #### 框架封装通用场景，提升开发效率
