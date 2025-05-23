# 操作属性的基本方法

:::tip 每日回顾

1. 节点属性
   a. 哪些属性可以获取注释文本节点哪些不可以获取
   b. 其它常用的节点属性 nodeType、nodeValue 等
   c. 什么是标准属性，标准属性的对应规则，一些标准属性的属性值
2. 属性的操作
   a. CRUD 属性
   b. 自定义特征
   c. attributes

:::
[[toc]]

## DOM 节点属性

### 节点关系的一些属性

![文档结构](https://s3.bmp.ovh/imgs/2025/05/12/edbd2525cc4abc5c.png)

| 节点属性                 | 是否忽略文档/注释节点 | 详细说明                                                      |
| ------------------------ | --------------------- | ------------------------------------------------------------- |
| `parentElement`          | ✅ 忽略               | 只返回 Element 类型的父节点，文档/注释节点的父元素会返回 null |
| `children`               | ✅ 忽略               | 只包含子元素节点，过滤掉文本/注释节点                         |
| `childElementCount`      | ✅ 忽略               | 只统计子元素数量，等价于 children.length                      |
| `firstElementChild`      | ✅ 忽略               | 只返回第一个子元素节点                                        |
| `lastElementChild`       | ✅ 忽略               | 只返回最后一个子元素节点                                      |
| `previousElementSibling` | ✅ 忽略               | 只返回前一个元素兄弟节点                                      |
| `nextElementSibling`     | ✅ 忽略               | 只返回后一个元素兄弟节点                                      |
| `contains`               | ❌ 不忽略             | 会检查所有类型的后代节点（包括文档/注释）                     |
| `parentNode`             | ❌ 不忽略             | 返回任何类型的父节点（包括文档/注释节点的父节点）             |
| `childNodes`             | ❌ 不忽略             | 包含所有类型的子节点（包括文本/注释节点）                     |

### 节点的标准属性

元素的标准属性（Standard Properties）是指通过 DOM 对象直接暴露的、与 HTML 标签原生属性对应的属性。

```html {4}
<a id="link" href="/">Click</a>
<script>
  const link = document.getElementById('link');
  // html id 对应 dom对象的id
  console.log(link.id); // "link"（字符串）
  console.log(link.href); // "http://example.com/"（自动补全完整URL）
</script>
```

操作规则：
| 规则 | 说明 | 示例 |
|-------------------------------|----------------------------------------------------------------------|----------------------------------------------------------------------|
| **区分大小写** | DOM 属性名严格区分大小写 | `tabIndex`（正确） vs `tabindex`（错误，HTML 标签属性不区分但 DOM 区分）|
| **驼峰命名法** | 多单词属性转为驼峰形式（如 `maxlength` → `maxLength`） | `<input maxlength="10">` → `input.maxLength` |
| **属性值类型多样性** | 不全是字符串，可能是布尔值、对象等 | `input.checked`（布尔值）、`element.style`（CSSStyleDeclaration 对象）|
| **事件处理程序属性** | 以 `on` 开头，值为函数 | `button.onclick = function() { ... }` |

特殊属性示例：

| 属性        | 类型                  | 说明                                                                |
| ----------- | --------------------- | ------------------------------------------------------------------- |
| `className` | `string`              | 对应 HTML 的 `class` 属性（因 `class` 是 JavaScript 保留字）        |
| `style`     | `CSSStyleDeclaration` | 通过对象操作 CSS 样式（如 `element.style.backgroundColor = "red"`） |
| `dataset`   | `DOMStringMap`        | 自定义数据属性（如 `<div data-id="123">` → `div.dataset.id`）       |
| `hidden`    | `boolean`             | 控制元素显隐（等价于设置 `display: none`）                          |

### 节点的其它常用属性

#### nodeType

nodeType 指以数值返回节点类型
以下是基于图片中 **Node 原型** 上下文和 DOM 标准整理的 `nodeType` 值说明：

| 值   | 常量（常量名）                | 说明         | 示例节点                            |
| ---- | ----------------------------- | ------------ | ----------------------------------- |
| `1`  | `Node.ELEMENT_NODE`           | 元素节点     | `<div>`、`<p>`                      |
| `2`  | `Node.ATTRIBUTE_NODE`         | 属性节点     | `<div id="test">` 中的 `id` 属性    |
| `3`  | `Node.TEXT_NODE`              | 文本节点     | `<p>文字</p>` 中的 "文字"           |
| `8`  | `Node.COMMENT_NODE`           | 注释节点     | `<!-- 注释 -->`                     |
| `9`  | `Node.DOCUMENT_NODE`          | 文档节点     | `document` 对象                     |
| `10` | `Node.DOCUMENT_TYPE_NODE`     | 文档类型节点 | `<!DOCTYPE html>`                   |
| `11` | `Node.DOCUMENT_FRAGMENT_NODE` | 文档片段节点 | `document.createDocumentFragment()` |

```html
<div id="app">
  <div class="bloom" data="hd">bloom.com</div>
  <div class="lmh">lmh.com</div>
  <div class="@bloom"><!--bloom --></div>
</div>
<script>
  const node = document.querySelector(`#app`);
  console.log(node.nodeType); //1
  console.log(node.firstChild.nodeType); //3
  console.log(node.attributes.id.nodeType); //2
  const xj = document.querySelector('.@bloom');
  console.log(xj.childNodes[0].nodeType); //8
</script>
```

#### nodeName

nodeName 指定节点的名称
以下是基于图片中 **Node 原型** 的属性和 DOM 标准整理的 `nodeType` 与 `nodeName` 对应关系：

| nodeType 值 | 常量名（对应图片中的原型链）  | nodeName 示例          | 说明                                                           |
| ----------- | ----------------------------- | ---------------------- | -------------------------------------------------------------- |
| **1**       | `Node.ELEMENT_NODE`           | `"DIV"`、`"P"`         | 元素节点，标签名以**大写形式**返回（如 `<div>` → `"DIV"`）     |
| **2**       | `Node.ATTRIBUTE_NODE`         | `"id"`、`"class"`      | 属性节点（已废弃，推荐用 `Element.getAttribute()` 替代）       |
| **3**       | `Node.TEXT_NODE`              | `"#text"`              | 文本节点，内容为标签内的文本（如 `<p>文字</p>` 中的 `"文字"`） |
| **8**       | `Node.COMMENT_NODE`           | `"#comment"`           | 注释节点（如 `<!-- 注释 -->`）                                 |
| **9**       | `Node.DOCUMENT_NODE`          | `"#document"`          | 文档根节点（即 `document` 对象）                               |
| **10**      | `Node.DOCUMENT_TYPE_NODE`     | `"html"`               | 文档类型声明节点（如 `<!DOCTYPE html>`）                       |
| **11**      | `Node.DOCUMENT_FRAGMENT_NODE` | `"#document-fragment"` | 文档片段节点（通过 `document.createDocumentFragment()` 创建）  |

```javascript
// 元素节点
const div = document.createElement('div');
console.log(div.nodeType); // 1
console.log(div.nodeName); // "DIV"

// 文本节点
const text = document.createTextNode('hello');
console.log(text.nodeType); // 3
console.log(text.nodeName); // "#text"

// 注释节点
const comment = document.createComment('注释');
console.log(comment.nodeType); // 8
console.log(comment.nodeName); // "#comment"
```

#### tagName

tagName 仅能用于获取标签节点的名称,文本、注释节点值为 undefined,获取的值为大写的标签名

```html
<div id="app">
  <div class="bloom" data="myData">bloom.com</div>
  <div class="lmh">lmh.com</div>
  <div class="@bloom"><!-- lmh --></div>
  <span> bloom_lmh</span>
</div>
<script>
  const div = document.querySelector(`#app`);
  const span = document.querySelector('span');

  // 标签节点为大写的标签名 如DIV、SPAN
  console.log(div.tagName);
  console.log(span.tagName);

  // 文本节点为undefined
  console.log(div.firstChild.tagName);

  //属性节点为undefined
  console.log(div.attributes.id.tagName);

  // 注释节点为 undefined
  const xj = document.querySelector('.@bloom');
  console.log(xj.childNodes[0].tagName);
</script>
```

#### nodeValue

使用 nodeValue 或 data 函数获取节点值，也可以使用节点的 data 属性获取节点内容
| nodeType | nodeValue |
|----------|--------------|
| 1 | null |
| 2 | 属性值 |
| 3 | 文本内容 |
| 8 | 注释内容 |
下面来看 nodeValue 的示例

```html
<div class="bloom">
  <!-- 注释节点 -->
  <div class="lmh">文本节点</div>
</div>
<script>
  let d1 = document.querySelector('.bloom');
  console.log(d1.nodeValue); // null
  console.log(d1.attributes.class.nodeValue); //bloom
  console.log(d1.childNodes[1].nodeValue); // 注释节点
  console.log(d1.children[0].childNodes[0].nodeValue); // 文本节点
</script>
```

使用 data 属性可以获取文本与注释内容

```html
<div id="app">
  bloom
  <!-- lmh 注释内容-->
</div>

<script>
  const app = document.querySelector('#app');
  console.log(app.childNodes[0].data); // bloom
  console.log(app.childNodes[1].data); // lmh
</script>
```

## DOM 属性的操作

### 获取节点属性

属性值都为字符串，所以数值类型需要进行转换

```html
<input class="inputClass" type="number" name="age" value="88" />
<script>
  let input = document.querySelector('.inputClass');
  console.log(input.getAttribute('value'));
</script>
```

### 对节点属性值的设置

```html
<input class="inputClass" type="number" name="age" value="88" />
<script>
  let input = document.querySelector('.inputClass');
  input.setAttribute('value', 300);
</script>
```

### 对节点属性的移除

使用 removeAttribute 删除元素的 class 属性，并通过 hasAttribute 进行检测删除结果

```html
<input class="inputClass" type="number" name="age" value="88" />
<script>
  let input = document.querySelector('.inputClass');
  input.removeAttribute('value');
</script>
```

### 判断是否有节点属性

使用 hasAttribute 检测是否含有某个属性

```html
<input class="inputClass" type="number" name="age" value="88" />
<script>
  let input = document.querySelector('.inputClass');
  input.removeAttribute('value');
  console.log(input.hasAttribute('value')); // false
</script>
```

### 自定义特征属性

虽然可以随意定义特征并使用 getAttribute 等方法管理，但很容易造成与标签的现在或未来属性重名。建议使用以 data-为前缀的自定义特征处理，针对这种定义方式 JS 也提供了接口方便操作。

- 元素中以 data-为前缀的属性会添加到属性集中
- 使用元素的 dataset 可获取属性集中的属性
- 改变 dataset 的值也会影响到元素上

下面演示使用属性集设置 DIV 标签内容
