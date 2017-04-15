### 情境 S:
> 1. 表单组件数据抽取
### 任务 T:  
> 1. 表单的构建、表单行为是不变的，如表单通常水平或垂直排列、提交表单都要验证表单
> 2. 表单项是变化的，但通常分为3部分，表单项值、验证规则、展现组件
### 行动 A: 
> 1. 表单抽取的结构如下:
> ```javascript
> // 构造一个表单
> let Form = CsForm.normal({
>     // 表单唯一标识，可以通过_.getComponent(`${info.code}${_.uuid()}`) 获取
>     id: `${info.code}${_.uuid()}`,
>     // 表单中的所有表单项
>     fields: [{
>         // 表单项名称
>         name: info.code,
>         // 表单项验证规则
>         rules: [
>           // 必填验证
>           {
>               required: true,
>               message: _.i18n.message('请输入工时')
>           }, 
>           // 正则验证
>           {
>               pattern: /^[0-9]+(.[0-9]{1})?$/,
>               message: _.i18n.message('仅支持1位小数')
>           }
>         ],
>         // 表单项初始值
>         value: info.content || 0,
>         // 表单项 DOM 组件构成
>         input: (<Input addonAfter={ <ButtonGroup>
>                         <CsButton
>                                   icon="check"
>                                   htmlType="submit"></CsButton>
>                         <CsButton
>                                   icon="close"
>                                   onClick={ this.onCancelEdit.bind(this, info.code) }></CsButton>
>                     </ButtonGroup> } />)
>     },
>     // 更多表单项，重复上面的结构
>     ],
>     // 向表单 <form 添加属性
>     formProps: {
>         className: `${info.code} work_time_form ${this.state[info.code] ? 'editable' : 'hide'}`
>     },
>     // 当 htmlType="submit" 按钮被点击，且所有表单项验证通过
>     // 回调该函数，参数为{name: 'input value'}，所有表单项值
>     submit: this.onChangeWorkTime
> });
> ```

### 结果 R:
> 1. 不用考虑表单项布局
> 2. 减少表单项手动验证
> 3. 极大提高表单开发效率
