#Vuex中的核心应用store(仓库)
## 在Vuex中 store就相当于一个仓库容器，里面可以存取数据

可以参考文档:

## <img alt="" class="has" height="400" src="https://img-blog.csdn.net/20180728101741548?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MTEwNDY5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="413">

可以结合我之前发的博客 vuex的配置使用，

## 1.Vue项目的结构，和store位置

在使用store的时候我们需要创建一个store的file文件，在里面写一个js用来寄存他的读取状态

<img alt="" class="has" height="250" src="https://img-blog.csdn.net/20180728102101486?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3MTEwNDY5/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70" width="300">

### 2.vuex的核心概念

    vuex中有5个核心概念 分别是 state,getters,mutations,acitons,module

```
​
//index.js
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex);

const store = new Vuex.Store({
  state:{
  },
  getters:{
  },
  mutations:{
  },
  actions:{
  },
  Module:{
  }
})

​
```

### 具体介绍可以去看文档

store的具体使用流程

State单一状态树让我们能够直接地定位任一特定的状态片段，在调试的过程中也能轻易地取得整个当前应用状态的快照。

```
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex);
const store = new Vuex.Store({
  state: {
    //类型没有规定
    testFlag:false, //在这里存储你的状态
    testList:{},  
  }
})
```

Getter用来在js页面中读取你要的状态

```
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex);
const store = new Vuex.Store({
  state: {
    //类型没有规定
    testFlag:false, //在这里存储你的状态
    testList:{},  
  }
  getter:{
    getTestFlag:state =&gt;state.testFlag, //就和java里面个get，set操作一样
    getTestList:state =&gt;state.testList,
  }
})
```

js页面的读取操作 (记得使用mapGetters 辅助函数映射局部属性) 具体看文档

```
//testGetter.js
import {mapGetters} from 'vuex' //mapGetters 辅助函数仅仅是将 store 中的 getter 映射到局部计算属性：

export default{
  name: "test-getter",//设置名字
  created(){
    if(this.getTestFlag){  //从store中获取getTestFlag并判断true/false;
      
    }
    
    if(this.getTestFile==null){  //与上一步一样 测试数据，判断getTestFile为空？
    }
  }
}
```

更改 Vuex 的 store 中的状态的唯一方法是提交 mutation,在store中的配置

```
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex);
const store = new Vuex.Store({
  state: {
    //类型没有规定
    testFlag:false, //在这里存储你的状态
    testList:{},  
  },
  getter:{
    getTestFlag:state =&gt;state.testFlag, //就和java里面个get，set操作一样
    getTestList:state =&gt;state.testList,
  },
  mutations:{
    SetTestFlag(state,data){ //设置改变TestFlag的值data和state状态
      state.testFlag=data    //data具体传值看下面的testMutation\
    } 
    SetTestList(state,data){
      state.testList=data    //同上
    } 
  }
})
```

在这里我们需要对state赋值，所以单独一个js文件用来写入state状态改变他的值

```
//testMutation.js
export default {
  name:"test-mutation",
  created(){  
    this.$store.commit("SetTestFlag",true)  //使用commit方法来存入,将TestFlag改为true
    this.$store.commit("SetTestList",this.lists); //同上
  },
  data(){
    lists:[{
      name:"我是测试数据用来放入SetTestList",
      code:"0",
    }]
  }
}
```

到这里store的基本使用已经可以完成

Action和Module本人使用较少，本文给入门的同学参考，有的了解这个的大佬也可以补充一下。
