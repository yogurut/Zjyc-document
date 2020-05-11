# Vue中全局监听键盘事件-牛育林

全局监听enter键，是把监听事件绑定到document上

```javascript
created: function(){

  var self = this;

  document.onkeydown = function(e){

   var key = window.enent.keyCode;

   if(key == 13){

     self.submit();

   }

  }

},

methods: {

  submit: function(){

   alert("监听到enter键！");

  }

}
```

