
js框架，对node.js（JS运行环境）有版本要求，目前是 18 以上


## 基本

Vue实例通过传递一个 根组件 给 `createAPP` 函数来创建新的应用实例

### 根组件

我们传入 `createApp` 的对象实际上是一个组件，每个应用都需要一个“根组件”，其他组件将作为其子组件。

如果你使用的是单文件组件，我们可以直接从另一个文件中导入根组件。

虽然本指南中的许多示例只需要一个组件，但大多数真实的应用都是由一棵嵌套的、可重用的组件树组成的。例如，一个待办事项 (Todos) 应用的组件树可能是这样的：

```
App (root component)
├─ TodoList
│  └─ TodoItem
│     ├─ TodoDeleteButton
│     └─ TodoEditButton
└─ TodoFooter
   ├─ TodoClearButton
   └─ TodoStatistics
```

我们会在指南的后续章节中讨论如何定义和组合多个组件。在那之前，我们得先关注一个组件内到底发生了什么。


### 挂载应用

应用实例必须在调用了 `.mount()` 方法后才会渲染出来。该方法接收一个“容器”参数，可以是一个实际的 DOM 元素或是一个 CSS 选择器字符串：


```html
<div id="app"></div>
```

```js
app.mount('#app')
```

应用根组件的内容将会被渲染在容器元素里面。容器元素自己将**不会**被视为应用的一部分。

`.mount()` 方法应该始终在整个应用配置和资源注册完成后被调用。同时请注意，不同于其他资源注册方法，它的返回值是根组件实例而非应用实例。


## 路由

https://cn.vuejs.org/guide/scaling-up/routing.html


## 工具

### 成员变化监视--watch

在Vue中，你可以使用`watch`来观察`edate`的变化，并在它被修改时执行相关的逻辑。下面是一个可能的实现：
```vue
<template>
  <div>
    <input type="date" v-model="searchItem.bdate" >
    <input type="date" v-model="searchItem.edate" >
  </div>
</template>

<script>
export default {
  data() {
    return {
      searchItem: {
        bdate: '',
        edate: ''
      }
    }
  },
  watch: {
    'searchItem.edate': function(newEdate, oldEdate) {
      // 在这里，`newEdate`是修改后的`edate`，`oldEdate`是修改前的`edate`
      // 我们需要将它们转换为日期对象，以便进行比较
      let edate = new Date(newEdate);
      let bdate = new Date(this.searchItem.bdate);

      // 检查`edate`是否在`bdate`之前
      if(edate < bdate) {
        // 如果`edate`不在`bdate`之前，我们将其值赋为`bdate`的值
        this.searchItem.edate = this.searchItem.bdate;
      }
    }
  }
}
</script>
```



