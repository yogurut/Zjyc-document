# sessionStorage简介-牛育林

1.1 说明

sessionStorage 是HTML5新增的一个会话存储对象，用于临时保存同一窗口(或标签页)的数据，在关闭窗口或标签页之后将会删除这些数据。

在JavaScript语言中可通过 window.sessionStorage 或 sessionStorage 调用此对象。

1.2 特点

1) 同源策略限制。若想在不同页面之间对同一个sessionStorage进行操作，这些页面必须在同一协议、同一主机名和同一端口下。(IE 8和9存储数据仅基于同一主机名，忽略协议（HTTP和HTTPS）和端口号的要求)

2) 单标签页限制。sessionStorage操作限制在单个标签页中，在此标签页进行同源页面访问都可以共享sessionStorage数据。

3) 只在本地存储。seesionStorage的数据不会跟随HTTP请求一起发送到服务器，只会在本地生效，并在关闭标签页后清除数据。(若使用Chrome的恢复标签页功能，seesionStorage的数据也会恢复)。

4) 存储方式。seesionStorage的存储方式采用key、value的方式。value的值必须为字符串类型(传入非字符串，也会在存储时转换为字符串。true值会转换为"true")。

5) 存储上限限制：不同的浏览器存储的上限也不一样，但大多数浏览器把上限限制在5MB以下。

可访问 http://dev-test.nemikor.com/web-storage/support-test/ 测试浏览器的存储上限。

1.3 浏览器最小版本支持

支持sessionStorage的浏览器最小版本：IE8、Chrome 5。

1.4 适合场景

sessionStorage 非常适合SPA(单页应用程序)，可以方便在各业务模块进行传值。

2.1 属性

 readonly int sessionStorage.length ：返回一个整数，表示存储在 sessionStorage 对象中的数据项(键值对)数量。

2.2 方法

方法 string sessionStorage.key(int index) ：返回当前 sessionStorage 对象的第index序号的key名称。若没有返回null。

方法 string sessionStorage.getItem(string key) ：返回键名(key)对应的值(value)。若没有返回null。

方法 void sessionStorage.setItem(string key, string value) ：该方法接受一个键名(key)和值(value)作为参数，将键值对添加到存储中；如果键名存在，则更新其对应的值。

方法 void sessionStorage.removeItem(string key) ：将指定的键名(key)从 sessionStorage 对象中移除。

方法 void sessionStorage.clear() ：清除 sessionStorage 对象所有的项。

3.1 存储数据

3.1.1 采用setItem()方法存储

sessionStorage.setItem('testKey','这是一个测试的value值'); // 存入一个值

3.1.2 通过属性方式存储　　

sessionStorage['testKey'] = '这是一个测试的value值';

3.2.1 通过getItem()方法取值

sessionStorage.getItem('testKey'); // => 返回testKey对应的值

3.2.2 通过属性方式取值

sessionStorage['testKey']; // => 这是一个测试的value值

3.3 存储Json对象

sessionStorage也可存储Json对象：存储时，通过JSON.stringify()将对象转换为文本格式；读取时，通过JSON.parse()将文本转换回对象。

var userEntity = {

    name: 'tom',

    age: 22

};

// 存储值：将对象转换为Json字符串

sessionStorage.setItem('user', JSON.stringify(userEntity));

// 取值时：把获取到的Json字符串转换回对象

var userJsonStr = sessionStorage.getItem('user');

userEntity = JSON.parse(userJsonStr);

console.log(userEntity.name); // => tom 