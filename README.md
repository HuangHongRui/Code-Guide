# 代码规范

## 程序员三大难题

1. 变量命名
2. 缓存失效
3. 循环边界

可以看出变量命名是很重要的，大部分时间我们都在想如何去命名变量，其余时间用来对这些变量进行操作。

## 提交规范
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

## HTML属性顺序及部分属性的命名方法
1. `class`——使用`_`分隔（如：`class="xxx_container"`）
2. `id, name`——使用`_`分隔（同上）
3. `data-*`
4. `src, for, type, href, value`
5. `title, alt`
6. `role, aria-*`

## 命名规范

### 注意词性

- 普通变量/属性用**名词**

  ```javascript
  var name = 'xxx';
  var age = 18;  
  ```

- boolean变量/属性用**形容词**/**be动词**/**情态动词**/**hasX**
  
  ```javascript
  var person = {
    dead: false, // 如果是形容词，就没必要加isXXX了，画蛇添足
    canSpeak: true, // 情态动词（can、should、will、need等） + 动词
    isVip: false, // be动词（is、was等等） + 名词
    hasChildren: true // has + 名词
  }
  ```
  
- 普通函数/方法以**动词**开头

- 回调、钩子函数用**介词**开头，或用**动词的现在完成时态**

- 容易混淆的地方加前缀
  
  ```javascript
  // DOM元素和jQuery元素分开
  
  domDiv1.classList.add('active');
  $div2.addClass('active');
  ```

- 属性访问器函数可以用名词

  ```javascript
  $div.text(); // 其实是$div.getText()
  $div.text('hello world'); // 其实是$div.setText('hello world');
  ```
  
  
### 注意一致性

- 介词一致性

  - 如果你使用了`before/after`，就在所有的地方都用这两个
  - 如果你使用了`before + 完成时`，就在所有地方都用这两个
  
  如果改来改去，会导致代码的不一致，不一致会导致**不可预测**，别人不知道你后面的代码会怎么写。

- 顺序一致性

  比如`updateContainerWidth`和`updateHeightOfContainer`，虽然这两个函数的命名都能表达意思，但若放在一起就会觉得很别扭，同样也会引发**不可预测**的问题。
  
- 表里一致性

  函数名必须完美体现函数的功能，既不能多也不能少
  
  ```javascript
  function getSongs() {
    return $.get('./songs').then(response => {
      div.innerText = response.songs;
    })
  }
  ```
  
  上述代码违背了表里一致性，`getSongs`从函数作用来说应当仅仅是用来获取歌曲，并没有表示这个函数会更新页面，但是实际上函数更新了div，这就是表里不一。
  
  那么怎么去优化这个代码？
  
    - 纠正函数名，如`getSongsAndUpdateDiv`
    - 写成两个函数


- 时间一致性

  有可能随着代码的变迁，一个变量的含义已经不同于它一开始的含义了，这个时候要及时改掉这个变量的名字。这一条是最难做到的，因为写代码容易，改代码难。如果这个代码组织得不好，很可能会出现牵一发而动全身的情况（如全局变量就很难改）。
 
### [如何写出无法维护的代码](https://coolshell.cn/articles/4758.html)

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

### 如何改代码

- 使用函数来改代码

  1. 将一坨代码放到一个函数里
  2. 将代码依赖的外部变量作为参数
  3. 将代码的输出作为函数的返回值
  4. 给函数取一个合适的名字
  5. 调用这个函数并传入参数
  6. 这个函数里的代码如果超过5行，则依然有优化空间，回到1

- 使用对象来改代码
  
  1. 如果使用了函数改造法改造后，发现有太多的小函数，则可以使用对象

### 一些固定的套路

- 表驱动编程

优化前

```javascript
function howManyDays(month) {
  if (month === 1 || 
      month === 3 || 
      month === 5 || 
      month === 7 || 
      month === 8 || 
      month === 10 || 
      month === 12) {
    return 31
  } else if (month === 2) {
    return 28
  } else {
    return 30
  }
}
```

优化后

```javascript
function howManyDays(month) {
  var monthTable = {
    1: 31,
    2: 28,
    3: 31,
    4: 30,
    5: 31,
    6: 30,
    7: 31,
    8: 31,
    9: 30,
    10: 31,
    11: 30,
    12: 31
  }
  return table[month];
}
```

另一个例子：

优化前：

```javascript
function calculateGrade(score) {
  if (score >= 90) {
    return 'A';
  } else if (score >= 80) {
    return 'B';
  } else if (score >= 70) {
    return 'C';
  } else if (score >= 60) {
    return 'D';
  } else {
    return 'E';
  }
}
```

优化后：

```javascript
function calculateGrade(score) {
  var table = {
    10: 'A',
    9: 'A',
    8: 'B',
    7: 'C',
    6: 'D',
    others: 'E'
  };
  var n = parseInt(score/10, 10);
  return table[n] || table['others'];
}
```

- 自说明代码（把别人关心的放在显眼的位置）

```javascript
class Dialog {
  constructor(options) {
    let defaultOptions = {
      title: '',
      content: '<h2>请填写content</h2>',
      buttons: [{
          text: '确定',
          action: function(close) {
            close();
          }
        },
        {
          text: '取消',
          action: function(close) {
            close();
          }
        }]
    }
  }
  this.options = Object.assign({}, defaultOptions, options);
}
```

### 代码的坏味道

有些代码虽然能用，但是很**臭**

1. 表里不一的代码
2. 过时的注释
3. 逻辑很简单，但是看起来很复杂的代码
4. 重复的代码
5. 相似的代码
6. 总是一起出现的代码

### 程序员的自我修养

程序员要做到：只要是经过手的代码，都要力求比之前好一点。

补充内容： [破窗效应](https://zh.wikipedia.org/wiki/%E7%A0%B4%E7%AA%97%E6%95%88%E5%BA%94)