# vue基础--王少欢
1. 双向绑定，
2. 组件化开发，（代码复用）,
3. 模板语法：{{}}，vue指令，修饰符，过滤器（工具函数）
4. 条件语句：v-if和v-show，
    *      v-if的原理是根据判断条件来动态的进行增删DOM元素
    *      v-show是根据判断条件来动态的进行显示和隐藏元素。
    *      v-for比v-if优先级高，
5. watch 和 computed 和 methods 区别是什么？
    *    当页面中有某些数据依赖其他数据进行变动的时候，可以使用computed（计算属性）。computed是具有缓存的
    *    (2)数据变化的同时进行异步操作或者是比较大的开销。watch为一个对象，键是需要观察的表达式，值是对应回调函数。值也可以是方法名，或者包含选项的对象。
    *    data中没有声明的数据可以在computed中使用，但是不能在watch中使用。
    *    在computed和watch方面，一个是计算，一个是观察。
        *   计算是通过变量计算来得出数据。而观察是观察一个特定的值，根据被观察者的变动进行相应的变化。

```  js
watch: {
    data: {
      handler(n) {
        if (n && !this.setVal) {
          this.currentValue = comdify(delcommafy(this.data))
          this.setVal = true
        } else {
          this.currentValue = this.data
        }
      },
      immediate: true
    }
  },
```
6. Vue路由：
    * 标签导航：标签导航<router-link><router-link>是通过转义为<a></a>标签进行跳转，其中router-link标签中的to属性会被转义为a标签中的href属性；
    * 编程式导航：我们可以通过this.$router.push()这个方法来实现编程式导航，当然也可以实现参数传递，这种编程式导航一般是用于按钮点击之后跳转；

7. Vue路由守卫：
    * 应用场景：在项目开发中每一次路由的切换或者页面的刷新都需要判断用户是否已经登录，
    *  应用例子
    ![应用例子](https://gitee.com/Yoguruto/image/raw/master/img/20200927194332.png)
8. Vue生命周期钩子函数有8个，ajax获取数据在mounted（模板渲染完毕后）中执行。
9. Vue组件之间通信（传值）
    * 使用$children获取子组件和父组件对象，（父传子），
    * 通过props进行通信，（父传子），
    * 使用$emit传递事件给父组件，父组件监听该事件，（子传父）
    * 使用$parent.获取父组件对象，然后再获取数据对象，（子传父）
    * Slot：插槽传值，
    * 使用事件总线，（任意组件）
    * Vuex（仓库），（任意组件）
10. Vuex
    * State，状态（数据）管理，
    * Mutations，状态修改的方法，
    * Getters：状态的进一步加工，（相当于组件的computed），
    * Actions，状态的异步操作，（ajax请求），
11. Vue实例属性或方法
    * $refs：获取模板的中dom节点（元素），
    * $route：获取路由传递的参数，
    * $emit：向父级组件触发一个事件，
    * $event：事件发生对象，
    * $set：在vue中，给对象添加属性,
    * $options：获取当前组件的属性（获取当前组件的name）
  
 