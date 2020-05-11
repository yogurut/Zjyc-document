# Vue对数组、对象的深拷贝、复制

当组件间传递对象时，由于此对象的引用类型指向的都是一个地址（除了基本类型跟null,对象之间的赋值，只是将地址指向同一个，而不是真正意义上的拷贝），如下

```js
数组：var a = [1,2,3];var b = a;
b.push(4); // b中添加了一个4alert(a); // a变成了[1,2,3,4]
对象：var obj = {a:10};var obj2 = obj;
obj2.a = 20; // obj2.a改变了，alert(obj.a); // 20，obj的a跟着改变
```

这就是由于对象类型直接赋值，只是将引用指向同一个地址，导致修改了obj会导致obj2也被修改

 

所以在vue中，如果多个组件引用了同一个对象作为数据，那么当其中一个组件改动对象数据时，其他对象的数据也会同步改动。有这种双向绑定的需要的话，那么自然是最好的，但如果不需要这种绑定而希望各组件的对象数据之间相互独立，即是互不关联的对象副本的话，可以用下面的方法解决



```js
computed: {  
     data: function () {  
         var obj={};  
         obj=JSON.parse(JSON.stringify(this.templateData)); //this.templateData是父组件传递的对象  
         return obj  
    }  
 }
```

