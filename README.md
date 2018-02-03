# 代码规范

### 提交规范
| Logo                    | abbr                        | KEY               | Description |
| ----------------------- | --------                    | -----------       |-------------|
| :book:                  | `:book:`                    | Docs:             | 文档         |
| :sparkles:              | `:sparkles:`                | Feat:             | 新特性       |
| :rocket:                | `:rocket:`                  | Improved:         | 改进/提升     |
| :heavy_minus_sign:      | `:heavy_minus_sign:`        | Removed:          | 移除         |
| :bug:                   | `:bug:`                     | Fixed:            | 修复bug      |
| :wrench:                | `:wrench:`                  | Modified:         | 修改         |
| :art:                   | `:art:`                     | Style:            | 样式         |
| :building_construction: | `:building_construction:`   | Refactor:         | 重构         |
| :recycle:               | `:recycle:`                 | Test:             | 测试         |
| :package:               | `:package:`                 | Build:            | 构建         |

示例：
```
格式： Logo 名称/#序号: 描述
实例： :bug: Fixed: 修复XXX...
```

### HTML属性顺序及部分属性的命名方法
1. `class`——使用`_`分隔（如：`class="xxx_container"`）
2. `id, name`——使用`_`分隔（同上）
3. `data-*`
4. `src, for, type, href, value`
5. `title, alt`
6. `role, aria-*`

### 命名规范

- 属性：小写开头&驼峰命名法


- 方法：
  - 事件处理方法：`handle[Sth]`：如`handleClick`
  - 数据生成方法：`generate[Sth]`
  - 数据获取方法：
    - 从接口获取：`fetch[Sth]`
    - 从其它地方获取：`get[Sth]`
- 变量：
  - `var/let`：小写开头&驼峰命名法
  - `const`：全大写，以`_`分隔

（tips：`boolean`以`is`开头命名。用于控制元素显隐的类名按`isHidden`命名。）

### 代码如何尽量优雅

从上次我看到[CSS为什么这么难学](https://zhuanlan.zhihu.com/p/29888231)之后，我就对“正交”这个词产生了一些思考。

正交是一个数学概念，让我们来看张图感受下

![不正交](https://ws1.sinaimg.cn/large/005AMlhfgy1fnkljjykdwg30ak06yb2c.jpg)

明明只是一个地方漏水而已，我也明明只是想把开关弄紧点而已，结果最后整个都炸了，这是我们始料未及的，因为这**不正交**，因为不正交，所以我们无法预知这个操作会不会影响别的东西，真可谓牵一发而动全身也。

作为FEer，在写代码的时候也遇到过这些问题。在以前直接操作DOM的时候，假设我现在有如下需求——控制某个元素的显隐：

如果我用jQuery，我可能会写出这样的代码，

```javascript
let show = false;
$btn.onclick = () => {
  if (show) {
    $div.hide();
    show = false;
  } else {
    $div.show();
    show = true;
  }
}
$div.hide()
$div.show()
```

，简单粗暴，功能实现，over。现在我对这样的代码是嗤之以鼻的，因为什么？——不正交啊。我如果这么写会不会好一点：

```
$div.removeClass("active")
$div.addClass("active")
```
这样我们就通过类来控制，出现样式上的改动我们只需要调整CSS就好了，js和css分离。

再深入点，产品又提需求了，现在这个元素直接显隐太丑，要渐渐地显示/隐藏：

如果我在原代码的基础上修改，我可能会这样写：

```javascript
let show = false;
$btn.onclick = () => {
  if (show) {
    $div.hide('slow');
    show = false;
  } else {
    $div.show('slow');
    show = true;
  }
}
```

再后来，万恶的PM/甲方又来需求了，说这个还不行，我要滑动显示/隐藏，代码开始变得有些臃肿起来：

```javascript
let show = false;
$btn.onclick = () => {
  if (show) {
    $div.slideUp();
    show = false;
  } else {
    $div.slideDown();
    show = true;
  }
}
```

每次来一个新的需求，你就得重写这段代码，有时候还得查看文档。如果你一开始就用class的方式控制样式，那你只需要修改CSS即可，还可以用上GPU加速，岂不美哉？

从另一个角度来讲，样式，不就是尽量去操作CSS吗？为什么要去修改JS，这种耦合是我们应该极力减少甚至避免的。当然现在有`CSS in JS`的东西出现，笔者对此尚未有深入研究，暂不介绍。
