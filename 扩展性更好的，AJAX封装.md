### 情境 S:
> 为了最求更好的扩展性，AJAX可以封装如下：
### 任务 T:  
>1. 抽取配置项
>2. ajax调用形式
>3. 工具实现代码
### 行动 A:
>1. 抽取配置项
> ```javascript
>// 扩展 window.api
>Object.assign(window.api = window.api || {}, {
>    // 关系图谱相关接口
>    relation: {
>        // 单个公司详情
>        nodeDetail: {
>            // 获取接口url
>            url: (param) => {
>                return `/relationMap/nodeDetail`
>            },
>            // 请求方式
>            method: 'get',
>            // 接口请求过期时间
>            expire: 1000 * 60 * 60 * 24,
>            // 修改接口数据
>            change: {
>                'data.datalist': {
>                    'position': (val) => {
>                        return _.isString(val) && val.replace(/;/g, ' ');
>                    },
>                    'ratio': (val) => {
>                        return val && (val + '%');
>                    }
>                }
>            },
>            // 请求参数
>            param: (widget) => {
>                // 数据查询条数
>                let param = {
>                    size: 10
>                };
>                // 存在类型选择器
>                if (widget.selectValue) {
>                    // 设置类型参数
>                    param.type = widget.selectValue;
>                }
>                // 设置查询文本
>                param.input = _.trim(widget.searchValue);
>                // 返回请求参数
>                return param;
>            },
>            // 设置状态值
>            setState: {
>                'dataSource': 'data'
>            },
>            // 节流请求【如搜索场景，用户输入完毕后，再发起ajax搜索请求】 
>            throttle: 500,
>        }
>    }
>});
> ```
>2. ajax调用形式
> ```javascript
>// 发起tab 数据查询请求
>new util.request.Ajax(api.relation.nodeDetail, this)
>    // 发起请求前
>    .before({
>        [`${key}Loading`]: true
>    })
>    .param({
>        nodeId,
>        nodeType,
>        field: key,
>        pageNow: 1,
>        pageSize: ''
>    })
>    .setState({
>        [`${key}Datas`]: 'data.datalist',
>        [`${key}Loading`]: false
>    });
>
>  // 节流请求场景
>  this.doAjax(this.props.api);
>
>```
>3. 工具实现代码
> ```javascript
>// 扩展 _ 方法
>Object.assign(window.util = window.util || {}, {
>    // 声明一个请求对象
>    request: {
>        // 初始化方法
>        init: () => {
>            // 扩展jquery ajax支持put delete方法.
>            jQuery.each(["put", "delete"], function(i, method) {
>                // 构建ajax 请求方法
>                jQuery[method] = function(url, data, callback, type) {
>                    // shift arguments if data argument was omitted
>                    if (jQuery.isFunction(data)) {
>                        type = type || callback || 'json';
>                        callback = data;
>                        data = undefined;
>                    }
>                    // 返回一个ajax请求对象
>                    return jQuery.ajax({
>                        url: url,
>                        type: method,
>                        dataType: type || 'json',
>                        data: data,
>                        success: callback || jQuery.noop
>                    });
>                };
>            });
>        },
>        // 后端接口请求
>        Ajax: class Ajax {
>            // ajax构造方法
>            constructor(config, widget) {
>                // Ajax对象，没有绑定组件对象；输出错误信息
>                if (!widget) {
>                    throw Error('Ajax 必须绑定，组件对象');
>                }
>                // 设置ajax 配置文件
>                this.config = config;
>                // 设置ajax 组件对象
>                this.widget = widget;
>                // 设置节流方法
>                if (this.config.throttle) {
>                    // 重写有节流特性的，ajax请求方法
>                    this.do = _.throttle(this.do.bind(this), this.config.throttle, {
>                        // 关闭节流开始前调用
>                        'leading': false,
>                        // 节流结束后开始调用
>                        'trailing': true
>                    });
>                }
>            }
>
>            // 请求前，设置状态信息
>            before(state) {
>                this.beforeState = state;
>                return this;
>            }
>
>            // 设置请求前状态修改
>            _setBeforeState() {
>                // 设置组件状态值
>                this.widget.setState(this.beforeState);
>            }
>
>            // 设置参数
>            param(param) {
>                this._param = param;
>                return this;
>            }
>
>            // 设置组件状态
>            setState(state) {
>                // 定义对象别名
>                let _this = this;
>                // 设置对象状态
>                this.state = state;
>                // 获取承诺对象
>                let dfd = $.Deferred();
>                // 将请求放入下一帧
>                window.requestAnimationFrame(() => {
>                    // 执行ajax 请求
>                    _this.do().done((data) => {
>                        // ajax 执行成功，返回结果
>                        dfd.resolve(data);
>                    }).fail((err) => {
>                        // 执行失败，返回错误
>                        dfd.reject(err);
>                    });
>                });
>                // 返回承诺对象
>                return dfd.promise();
>            }
>
>            // 向组件设置状态值
>            _setState(data) {
>                // 初始化状态值
>                let r = {};
>                // 遍历状态值
>                for (let k in this.state) {
>                    // 获取设置值
>                    let val = this.state[k];
>                    // 如果是字符串，则设置ajax结果值
>                    if (_.isString(val)) {
>                        r[k] = _.get(data, val, val);
>                    // 如果是函数类型，则调用函数
>                    } else if (_.isFunction(val)) {
>                        r[k] = val(data);
>                    // 如果是其他，则直接赋值
>                    } else {
>                        r[k] = val;
>                    }
>                }
>                // 设置组件状态值
>                this.widget.setState(r);
>            }
>
>            // 获取请求url
>            getUrl() {
>                return window.getapi('rrp', this.config.url(this._param));
>            // return `https://gw.wmcloud.com/rrp/web${this.config.url(this._param)}`;
>            }
>
>            // 发起ajax get请求
>            get() {
>                return $.get(this.getUrl(), this._param);
>            }
>
>            // 支持缓存请求
>            getCache() {
>                // 计算请求key
>                let key = `${this.getUrl()}.${hash(this._param)}`;
>                // 获取定时器key
>                let timeoutKey = `${key}.expire`;
>                // 获取定时器
>                let tf = util.storage.cache[timeoutKey];
>                // 清除定时器
>                tf && clearTimeout(tf);
>                // 定时清除数据
>                util.storage.cache[timeoutKey] = setTimeout(() => {
>                    util.storage.cache[key] = null;
>                }, 1000 * 60 * 5);
>                // 获取缓存数据
>                let c = util.storage.cache[key];
>                // 如果存在缓存数据
>                if (c) {
>                    // 返回克隆数据
>                    return util.resolve(_.cloneDeep(c));
>                }
>                // 发起ajax 请求
>                return this.get().done((data) => {
>                    // 备份数据
>                    util.storage.cache[key] = _.cloneDeep(data);
>                });
>            }
>
>            // 发起ajax post请求
>            post() {
>                return $.post(this.getUrl(), this._param);
>            }
>
>            // 发起ajax put请求
>            put() {
>                return $.put(this.getUrl(), this._param);
>            }
>
>            // 发起ajax delete请求
>            delete() {
>                return $.delete(this.getUrl(), this._param);
>            }
>
>            // json类型请求
>            json(type) {
>                return $.ajax({
>                    type: type,
>                    url: this.getUrl(),
>                    data: JSON.stringify(this._param),
>                    dataType: "json",
>                    contentType: "application/json"
>                });
>            }
>
>            // 发起post JSON 请求
>            postJSON() {
>                return this.json('POST');
>            }
>
>            // 发起put JSON 请求
>            putJSON() {
>                return this.json('PUT');
>            }
>
>            // 改变单个对象，属性值
>            _changeUnitObj(obj, changeFuncs) {
>                // 遍历改变函数列表
>                for (let k in changeFuncs) {
>                    // 获得单个改变函数
>                    let fuc = changeFuncs[k];
>                    // 如果数据中不存在，该数据
>                    if (_.isUndefined(obj[k])) {
>                        // 则生成新属性
>                        obj[k] = fuc(obj);
>                    // 存在属性值
>                    } else {
>                        // 使用函数修改属性值
>                        obj[k] = fuc(obj[k]);
>                    }
>                }
>            }
>
>            // 改变一个数据属性
>            _changeDataAttr(attr, changeFuncs) {
>                // 如果属性为数组类型
>                if (_.isArray(attr)) {
>                    // 遍历该数组
>                    _.each(attr, (obj) => {
>                        // 改变每个元素的属性
>                        this._changeUnitObj(obj, changeFuncs);
>                    })
>                // 如果属性为对象类型
>                } else {
>                    // 改变元素属性
>                    this._changeUnitObj(obj, changeFuncs);
>                }
>            }
>
>            // 改变接口数据
>            _changeData(data) {
>                // 获取属性变更配置
>                let change = this.config.change;
>                // 遍历配置列表
>                for (let k in change) {
>                    // 获取属性修改，函数列表
>                    let cfs = change[k];
>                    // 查询属性值
>                    let d = _.get(data, k);
>                    // 如果属性存在
>                    if (d) {
>                        // 使用函数列表，修改属性值
>                        this._changeDataAttr(d, cfs);
>                    }
>                }
>            }
>
>            // 执行ajax请求
>            do() {
>                // 如果存在过期设置, 且没有页码参数
>                if (this.config.expire) {
>                    // 获取页码
>                    let pageNow = this._param['pageNow'];
>                    // 检查当前组件，指定状态；过期时间；未过期，则终止ajax 请求
>                    let noExpire = !this.widget.checkStateStampExpire(this.state, this.config.expire);
>                    // 页码存在，第一页判断过期；不存在页码，判断过期
>                    if (pageNow ? pageNow === 1 && noExpire : noExpire) {
>                        // 终止ajax
>                        return;
>                    }
>                }
>                // 存在请求前，状态修改
>                !_.isEmpty(this.beforeState) && this._setBeforeState();
>                // 存在配置参数配置, 使用配置参数
>                if (_.isFunction(this.config.param)) {
>                    // 通过配置文件参数，获得参数值
>                    this._param = this.config.param(this.widget);
>                }
>                // 存在配置状态，优先使用配置选项
>                this.state = this.config.setState ? this.config.setState : this.state;
>                // 执行后端接口请求
>                return this[this.config.method]().done((data) => {
>                    // 改变接口数据
>                    !_.isEmpty(this.config.change) && this._changeData(data);
>                    // 如果存在组件对象
>                    this.widget && this.state && this._setState(data);
>                });
>            }
>        }
>    }
>});
>// 执行初始化方法
>util.request.init();
>
>```
### 结果 R:
> 1. 封装api可变性
> 2. 集成复杂ajax请求方法
> 3. 增加全局控制逻辑，更容易
