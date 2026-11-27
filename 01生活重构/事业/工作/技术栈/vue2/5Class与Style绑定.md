# Class 与 Style 绑定

操作元素的 class 列表和内联样式是数据绑定的一个常见需求。因为它们都是 attribute，所以我们可以用 `v-bind` 处理它们：只需要通过表达式计算出字符串结果即可。不过，字符串拼接麻烦且易错。因此，在将 `v-bind` 用于 `class` 和 `style` 时，Vue.js 做了专门的增强。表达式结果的类型除了字符串之外，还可以是对象或数组。

> ps：class和内联本质都是控制样式，class更适合一些统一的预设的样式，内联style更适合单独的、计算得到的、动态的样式，且两者涵盖同一样式时，内联设置会覆盖class设置

## [绑定 HTML Class](https://v2.cn.vuejs.org/v2/guide/class-and-style.html#%E7%BB%91%E5%AE%9A-HTML-Class "绑定 HTML Class")

[观看本节视频讲解](https://learning.dcloud.io/#/?vid=7 "Vue.js 教程 - class 与 style 绑定")

### [对象语法](https://v2.cn.vuejs.org/v2/guide/class-and-style.html#%E5%AF%B9%E8%B1%A1%E8%AF%AD%E6%B3%95 "对象语法")

我们可以传给 `v-bind:class` 一个对象，以动态地切换 class：

```html
<div v-bind:class="{ active: isActive }"></div>
```

上面的语法表示 `active` 这个 class 存在与否将取决于数据 property `isActive` 的 [truthiness](https://developer.mozilla.org/zh-CN/docs/Glossary/Truthy)。

你可以在对象中传入更多字段来动态切换多个 class。此外，`v-bind:class` 指令也可以与普通的 class attribute 共存。当有如下模板：

```html
<div  
	class="static"  
	v-bind:class="{ active: isActive, 'text-danger': hasError }"  
></div>
```

> ps：'text-danger' 和 active 本质一样，只是因为连字符所以需要单引号

和如下 data：

```js
data: {  
	isActive: true,  
	hasError: false  
}
```

结果渲染为：

```html
<div class="static active"></div>
```

当 `isActive` 或者 `hasError` 变化时，class 列表将相应地更新。例如，如果 `hasError` 的值为 `true`，class 列表将变为 `"static active text-danger"`。

绑定的数据对象不必内联定义在模板里：

```html
<div v-bind:class="classObject"></div>
```

```js
data: {  
	classObject: {  
		active: true,  
		'text-danger': false  
	}  
}
```

渲染的结果和上面一样。我们也可以在这里绑定一个返回对象的[计算属性](https://v2.cn.vuejs.org/v2/guide/computed.html)。这是一个常用且强大的模式：

```html
<div v-bind:class="classObject"></div>
```

```js
data: {  
	isActive: true,  
	error: null  
},  
computed: {  
	classObject: function () {  
		return {  
			active: this.isActive && !this.error,  
			'text-danger': this.error && this.error.type === 'fatal'  
		}  
	}  
}
```

### [数组语法](https://v2.cn.vuejs.org/v2/guide/class-and-style.html#%E6%95%B0%E7%BB%84%E8%AF%AD%E6%B3%95 "数组语法")

我们可以把一个数组传给 `v-bind:class`，以应用一个 class 列表：

```html
<div v-bind:class="[activeClass, errorClass]"></div>
```

```js
data: {  
	activeClass: 'active',  
	errorClass: 'text-danger'  
}
```

渲染为：

```html
<div class="active text-danger"></div>
```

如果你也想根据条件切换列表中的 class，可以用三元表达式：

```html
<div v-bind:class="[isActive ? activeClass : '', errorClass]"></div>
```

这样写将始终添加 `errorClass`，但是只有在 `isActive` 是 truthy 时才添加 `activeClass`。

> truthy 不是 `true`，详见 [MDN](https://developer.mozilla.org/zh-CN/docs/Glossary/Truthy) 的解释。

不过，当有多个条件 class 时这样写有些繁琐。所以在数组语法中也可以使用对象语法：

```html
<div v-bind:class="[{ active: isActive }, errorClass]"></div>
```

### [用在组件上](https://v2.cn.vuejs.org/v2/guide/class-and-style.html#%E7%94%A8%E5%9C%A8%E7%BB%84%E4%BB%B6%E4%B8%8A "用在组件上")

> 这个章节假设你已经对 [Vue 组件](https://v2.cn.vuejs.org/v2/guide/components.html)有一定的了解。当然你也可以先跳过这里，稍后再回过头来看。

当在一个自定义组件上使用 `class` property 时，这些 class 将被添加到该组件的根元素上面。这个元素上已经存在的 class 不会被覆盖。

例如，如果你声明了这个组件：

```js
Vue.component('my-component', {  
	template: '<p class="foo bar">Hi</p>'  
})
```

然后在使用它的时候添加一些 class：
```html
<my-component class="baz boo"></my-component>
```

HTML 将被渲染为：
```html
<p class="foo bar baz boo">Hi</p>
```

对于带数据绑定 class 也同样适用：
```html
<my-component v-bind:class="{ active: isActive }"></my-component>
```

当 `isActive` 为 truthy 时，HTML 将被渲染成为：
```html
<p class="foo bar active">Hi</p>
```


## [绑定内联样式](https://v2.cn.vuejs.org/v2/guide/class-and-style.html#%E7%BB%91%E5%AE%9A%E5%86%85%E8%81%94%E6%A0%B7%E5%BC%8F "绑定内联样式")

### [对象语法](https://v2.cn.vuejs.org/v2/guide/class-and-style.html#%E5%AF%B9%E8%B1%A1%E8%AF%AD%E6%B3%95-1 "对象语法")

`v-bind:style` 的对象语法十分直观——看着非常像 CSS，但其实是一个 JavaScript 对象。CSS property 名可以用驼峰式 (camelCase) 或短横线分隔 (kebab-case，记得用引号括起来) 来命名：

```html
<div v-bind:style="{ color: activeColor, fontSize: fontSize + 'px' }"></div>
```

```js
data: {  
	activeColor: 'red',  
	fontSize: 30  
}
```

直接绑定到一个样式对象通常更好，这会让模板更清晰：

```html
<div v-bind:style="styleObject"></div>
```

```js
data: {  
	styleObject: {  
		color: 'red',  
		fontSize: '13px'  
	}  
}
```

同样的，对象语法常常结合返回对象的计算属性使用。

### [数组语法](https://v2.cn.vuejs.org/v2/guide/class-and-style.html#%E6%95%B0%E7%BB%84%E8%AF%AD%E6%B3%95-1 "数组语法")

`v-bind:style` 的数组语法可以将多个样式对象应用到同一个元素上：

```html
<div v-bind:style="[baseStyles, overridingStyles]"></div>
```

### [自动添加前缀](https://v2.cn.vuejs.org/v2/guide/class-and-style.html#%E8%87%AA%E5%8A%A8%E6%B7%BB%E5%8A%A0%E5%89%8D%E7%BC%80 "自动添加前缀")

当 `v-bind:style` 使用需要添加[浏览器引擎前缀](https://developer.mozilla.org/zh-CN/docs/Glossary/Vendor_Prefix)的 CSS property 时，如 `transform`，Vue.js 会自动侦测并添加相应的前缀。

### [多重值](https://v2.cn.vuejs.org/v2/guide/class-and-style.html#%E5%A4%9A%E9%87%8D%E5%80%BC "多重值")

> 2.3.0+

从 2.3.0 起你可以为 `style` 绑定中的 property 提供一个包含多个值的数组，常用于提供多个带前缀的值，例如：

```html
<div :style="{ display: ['-webkit-box', '-ms-flexbox', 'flex'] }"></div>
```

这样写只会渲染数组中最后一个被浏览器支持的值。在本例中，如果浏览器支持不带浏览器前缀的 flexbox，那么就只会渲染 `display: flex`。

> ps：多重值是为了兼容不同浏览器用的，尤其是一些老浏览器（标准不统一之类的）


