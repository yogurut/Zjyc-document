# Vue checkbox框问题记录

# 一、代码片段

```html
<div id="app">
        {{ message }}
        <input type="checkbox" v-model="item.isSelected" :true-value="'t'" :false-value="'f'" @click="clickWeekNum(item,item.isSelected)">
        {{item.remark}}
</div>

<script>
    var app = new Vue({
        el: '#app',
        data: {
            message: 'Hello Vue!',
            item:{isSelected:"f", code:6, remark:'星期六'}
        },
        methods:{
            clickWeekNum:function(item,isSelected){
                console.log(item,isSelected);
            },
            changeWeekNum:function(item, isSelected){
                    console.log(item, isSelected);
            }
        }

    })
</script>
```
## 二、代码运行结果展示

1. 上面的代码中，复选框的true-value, false-value可以把v-model绑定的值其结果由true, false变更为指定的值。

2. 结果展示

   #### IE

   ![](https://github.com/SN1997/Zjyc-document/blob/master/picture/IE%E7%BB%93%E6%9E%9C%E6%BC%94%E7%A4%BA.png)

火狐

![](https://github.com/SN1997/Zjyc-document/blob/master/picture/%E7%81%AB%E7%8B%90%E7%BB%93%E6%9E%9C%E6%BC%94%E7%A4%BA.png)

1. click与change区别
   一个是点击就触发，一个是改动后才触发