---
title: mobx初识
date: 2018-06-30 12:15:19
tags:
---

### mobx
> 一个状态管理工具 简单、可扩展
+ Observable state(可观察的状态)
MobX 为现有的数据结构(如对象，数组和类实例)添加了可观察的功能。 通过使用 @observable 装饰器(ES.Next)来给你的类属性添加注解就可以简单地完成这一切。

+ Computed values(计算值)
使用 MobX， 你可以定义在相关数据发生变化时自动更新的值。 通过@computed 装饰器或者利用 (extend)Observable 时调用 的getter / setter 函数来进行使用。(当然，这里也可以再次使用 decorate 来替代 @ 语法)。

+ Reactions(反应)
Reactions 和计算值很像，但它不是产生一个新的值，而是会产生一些副作用，比如打印到控制台、网络请求、递增地更新 React 组件树以修补DOM、等等。 简而言之，reactions 在 响应式编程和命令式编程之间建立沟通的桥梁。

+ Actions(动作)



### mobx的几个核心要点
+ 1. 定义状态并使其可观察
可以用任何你喜欢的数据结构来存储状态，如对象、数组、类。 循环数据结构、引用，都没有关系。 只要确保所有会随时间流逝而改变的属性打上 mobx 的标记使它们变得可观察即可。

+ 2. 创建视图以响应状态的变化
MobX 会以一种最小限度的方式来更新视图。 事实上这一点可以节省了你大量的样板文件，并且它有着令人匪夷所思的高效。

+ 3. 更改状态
第三件要做的事就是更改状态。 也就是你的应用究竟要做什么。 不像一些其它框架，MobX 不会命令你如何如何去做。 这是最佳实践，但关键要记住一点: MobX 帮助你以一种简单直观的方式来完成工作。

只有在严格模式(默认是不启用)下使用 MobX 时才需要 action 包装。 建议使用 action，因为它将帮助你更好地组织应用，并表达出一个函数修改状态的意图。 同时,它还自动应用事务以获得最佳性能。


### 几个关键词的概念

1. State(状态)
状态 是驱动应用的数据。 通常有像待办事项列表这样的领域特定状态，还有像当前已选元素的视图状态。 记住，状态就像是有数据的excel表格。

2. Derivations(衍生)
任何 源自状态并且不会再有任何进一步的相互作用的东西就是衍生。 衍生以多种形式存在:

用户界面
衍生数据，比如剩下的待办事项的数量。
后端集成，比如把变化发送到服务器端。
MobX 区分了两种类型的衍生:

Computed values(计算值) - 它们是永远可以使用纯函数(pure function)从当前可观察状态中衍生出的值。
Reactions(反应) - Reactions 是当状态改变时需要自动发生的副作用。需要有一个桥梁来连接命令式编程(imperative programming)和响应式编程(reactive programming)。或者说得更明确一些，它们最终都需要实现I / O 操作。
刚开始使用 MobX 时，人们倾向于频繁的使用 reactions。 黄金法则: 如果你想创建一个基于当前状态的值时，请使用 computed。

回到excel表格这个比喻中来，公式是计算值的衍生。但对于用户来说，能看到屏幕给出的反应则需要部分重绘GUI。

3. Actions(动作)
动作 是任一一段可以改变状态的代码。用户事件、后端数据推送、预定事件、等等。 动作类似于用户在excel单元格中输入一个新的值。

在 MobX 中可以显式地定义动作，它可以帮你把代码组织的更清晰。 如果是在严格模式下使用 MobX的话，MobX 会强制只有在动作之中才可以修改状态。

原则
MobX 支持单向数据流，也就是动作改变状态，而状态的改变会更新所有受影响的视图。

Action --> State  --> View  单项数据流

当状态改变时，所有衍生都会进行原子级的自动更新。因此永远不可能观察到中间值。

所有衍生默认都是同步更新。这意味着例如动作可以在改变状态之后直接可以安全地检查计算值。

计算值 是延迟更新的。任何不在使用状态的计算值将不会更新，直到需要它进行副作用（I / O）操作时。 如果视图不再使用，那么它会自动被垃圾回收。

所有的计算值都应该是纯净的。它们不应该用来改变状态。

### 实例应用
```
import {observable, autorun} from 'mobx';

var todoStore = observable({
    /* 一些观察的状态 */
    todos: [],

    /* 推导值 */
    get completedCount() {
        return this.todos.filter(todo => todo.completed).length;
    }
});

/* 观察状态改变的函数 */
autorun(function() {
    console.log("Completed %d of %d items",
        todoStore.completedCount,
        todoStore.todos.length
    );
});

/* ..以及一些改变状态的动作 */
todoStore.todos[0] = {
    title: "Take a walk",
    completed: false
};
// -> 同步打印 'Completed 0 of 1 items'

todoStore.todos[0].completed = true;
// -> 同步打印 'Completed 1 of 1 items'

```