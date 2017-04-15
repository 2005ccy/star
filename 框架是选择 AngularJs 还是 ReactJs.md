### 情境 S:
> 1. 框架是选择 AngularJs 还是 ReactJs
### 任务 T:  
> 这里反应出两个问题，
> 1. 框架选择技巧
> 2. 网页性能本质
> 3. 平台扩展性
### 行动 A: 
> #### 1. 先回答第一个问题，怎样选择框架。
>
> + 看研发团队实力 
>```
> 1. angularJs 由google前端团队开发，实力毋庸置疑
> 2. reactJs 由Facebook前端团队开发，成绩斐然
> 3. 比较来看得分相当，各得一票
>
>```
> + 看github得分
>```
>1. angularJs   获得 star：21726  fork： 5573  watch：2357
>2. reactJs     获得 star：61950  fork：11400  watch：4234
>3. 比较来看reactJs得分更高，这票给react
>```
> + 框架历史，时间是衡量一切事物最重要的尺子
>```
>1. AngularJS  诞生于2009年，由Misko Hevery 等人创建，后为Google所收购
>2. React 起源于 Facebook 的内部项目，2013年被开发出来
>3. reactJs用更短的时间，取得更多的成就，这票给react
>```
> + bug修复与版本更新速度
>```
> 1. angularJs  数据 commits: 6899  branches: 10 releases: 112 contributors: 408  Issues: 1076  pull requests: 128
> 2. reactJs    数据 commits: 8337  branches: 32 releases:  56 contributors: 954  Issues:  566  pull requests: 118
> 3. 看两个重要指标：成员数、代码提交次数reactJs得分更高，这票给react
>```
> #### 2. 回答第二个问题，网页性能本质
>
> + 网页生成过程
>```
> 1. HTML代码转化成DOM
> 2. CSS代码转化成CSSOM（CSS Object Model）
> 3. 结合DOM和CSSOM，生成一棵渲染树（包含每个节点的视觉信息）
> 4. 生成布局（layout），即将所有渲染树的所有节点进行平面合成
> 5. 将布局绘制（paint）在屏幕上
>```
> + 重排和重绘，网页生成的时候，至少会渲染一次。用户访问的过程中，还会不断重新渲染【重新生成布局和重新绘制。前者叫做"重排"（reflow），后者叫做"重绘"（repaint）】
>```
> 下面的操作，会导致网页重排、重绘
>
>1. 修改DOM
>2. 修改样式表
>3. 用户事件（比如鼠标悬停、页面滚动、输入框键入文字、改变窗口大小等等）
>
> *提高网页性能，就是要降低"重排"和"重绘"的频率和成本，尽量少触发重新渲染
>```
>  + 从上面的角度看，angularJs只是提高了开发效率，并没有解决较大块DOM的重新渲染问题，而react使用虚拟DOM比较，仅对较小块DOM重新渲染，极大的提高性能
> #### 3. 最后一个问题“平台扩展性”
> + H5的app程序，已经被各大互联网公司抛弃，在交互上体验非常糟糕；这是DOM的本质决定的不是程序员不优秀；用任何框架开发H5的app结果是一样的
>
> + React-Native给我们换了一个思路，使用ReactJs编写规则，通过中间层，将react组件转换为原生Android与IOS组件，并且两个手机平台85%代码是相同的。
### 结果 R:
> #### 经过以上较为选择ReactJs更有价值
