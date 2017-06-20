### 点击下载文本到本地文件中
```
function downloadText(content) {
  let fileName = 'xxxx.txt';  // 文件名
  let aLink = document.createElement('a'); 
  let evt = document.createEvent("MouseEvents");
  evt.initEvent('click', true, false, window, 0, 0, 0, 0, 0, false, false, false, false, 0, null); // 点击
  aLink.download = fileName;
  aLink.dispatchEvent(evt);
}
```
**知识点：事件触发器**
> 事件触发器就是用来触发某个元素下的某个事件，IE下有fireEvent()，高级浏览器（chrome，firefox等）有dispatchEvent()

#### IE浏览器
```
var fireEvent = function(element, event) {
   // IE浏览器支持fireEvent方法
  if (document.createEventObject) {
      var evt = document.createEventObject();
      return element.fireEvent('on' + event, evt)
  } else {
      // 其他标准浏览器使用dispatchEvent方法
      var evt = document.createEvent('HTMLEvents');
      // initEvent接受3个参数：
      // 事件类型，是否冒泡，是否阻止浏览器的默认行为
      evt.initEvent(event, true, true);
      return !element.dispatchEvent(evt);
  }
};
或
//document上绑定自定义事件ondataavailable
document.attachEvent('ondataavailable', function (event) {
alert(event.eventType);
});
var obj=document.getElementById("obj");
//obj元素上绑定click事件
obj.attachEvent('onclick', function (event) {
alert(event.eventType);
});
//调用document对象的createEventObject方法得到一个event的对象实例。
var event = document.createEventObject();
event.eventType = 'message';
//触发document上绑定的自定义事件ondataavailable
document.fireEvent('ondataavailable', event);
//触发obj元素上绑定click事件
document.getElementById("test").onclick = function () {
obj.fireEvent('onclick', event);
};
```

#### chrome、firefox等浏览器
```
//document上绑定自定义事件ondataavailable
document.addEventListener('ondataavailable', function (event) {
alert(event.eventType);
}, false);
var obj = document.getElementById("obj");
//obj元素上绑定click事件
obj.addEventListener('click', function (event) {
alert(event.eventType);
}, false);
//调用document对象的 createEvent 方法得到一个event的对象实例。
var event = document.createEvent('HTMLEvents');
// initEvent接受3个参数：
// 事件类型，是否冒泡，是否阻止浏览器的默认行为
event.initEvent("ondataavailable", true, true);
event.eventType = 'message';
//触发document上绑定的自定义事件ondataavailable
document.dispatchEvent(event);
var event1 = document.createEvent('HTMLEvents');
event1.initEvent("click", true, true);
event1.eventType = 'message';
//触发obj元素上绑定click事件
document.getElementById("test").onclick = function () {
obj.dispatchEvent(event1);
};
```