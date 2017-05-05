### 情境 S:
> 1. 页面上SVG 可视范围一般，800px * 600px，如何导出 1980px * 1670px的图片呢 
### 任务 T:  
> 1. 考虑到导出效率，优先考虑前端解决方案
> 2. svg 本地资源转换
> 3. 使用canvas 转换到base64 格式
> 4. 使用<a 标签下载
### 行动 A: 
> 1. 相关代码如下
> ```javascript
>// 导出图片
>    outputSvgPng() {
>
>        // 当前对象别名
>        let _this = this;
>
>        // 展示导出弹窗
>        this.setState({
>            visibleSvgToPng: true,
>            visibleOutput: false,
>            sppingSvgToPng: true
>        });
>
>        // 轮播查询, 需展示的图片
>        this.selectPoll(`#img-svg-${this.id}`, (sel) => {
>
>            // 获取svg DOM
>            let svgSel = _this.select('.relation-svg');
>            // 获取全局对象
>            let gg = svgSel.find('.global-g');
>            // 获取转换属性
>            let tl = gg.attr('transform');
>            // 使缩为原始大小
>            tl && gg.attr('transform', tl.replace(/scale\([^)]+\)/, 'scale(1)'));
>
>            // 获取关系图谱的，上、下、左、右【边界】
>            let {left, right, up, down} = _this.coreAtlas.state.relation.getWidthHeight();
>            // 计算图谱宽度
>            let width = right - left;
>            // 计算图谱高度
>            let height = down - up;
>
>            // 设置svg 为新宽度
>            svgSel.width(width).height(height);
>            // 设置svg 为新高度
>            sel.width(width).height(height);
>
>            // 使图像在，新的画布，完全展示
>            gg.attr('transform', `translate(${Math.abs(left)},${Math.abs(up)})scale(1)`);
>            // 将svg对象，转换成字符串表达形式
>            let svgString = new XMLSerializer().serializeToString(svgSel[0]);
>
>            // 获取canvas 画布对象
>            let canvas = $(`#canvas-${_this.id}`)[0];
>            // 设置画布宽度，有 padding: 50px 的间距
>            canvas.width = width + 100;
>            // 设置画布高度，有 padding: 50px 的间距
>            canvas.height = height + 100;
>            // 获取画布上下文
>            let ctx = canvas.getContext("2d");
>            // 清除画布内容
>            ctx.clearRect(0, 0, canvas.width, canvas.height);
>            // 设置白色背景
>            ctx.fillStyle = "#fff";
>            ctx.fillRect(0, 0, canvas.width, canvas.height);
>
>            // 构建url转换对象
>            let DOMURL = self.URL || self.webkitURL || self;
>            // 获取图谱 dom 对象
>            let img = sel[0];
>            // 生成svg 二进制形式
>            let svg = new Blob([svgString], {
>                type: "image/svg+xml;charset=utf-8"
>            });
>            // 创建二进制访问url
>            let url = DOMURL.createObjectURL(svg);
>            // 图片加载成功，回调方法
>            img.onload = function() {
>                // 在画布中，绘制图谱
>                ctx.drawImage(img, 50, 50);
>                // 生成图谱base64 格式，字符串
>                let png = canvas.toDataURL("image/png");
>                // 设置下载按钮href， 为base64 字符串
>                $(`#img-download-${_this.id}`).attr('href', png);
>                // 清除url 对象
>                DOMURL.revokeObjectURL(png);
>                // 取消生成，中间状态
>                _this.setState({
>                    sppingSvgToPng: false
>                });
>                // 设置svg，为原始宽高
>                svgSel.css({
>                    width: '100%',
>                    height: '100%'
>                });
>            };
>            img.src = url;
>        });
>    }
> ```

### 结果 R:
> 1. 以上代码就能，实现svg全尺寸输出
