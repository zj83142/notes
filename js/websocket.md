## websocket 教程

> 原文来自[阮一峰 - websocket教程](http://www.ruanyifeng.com/blog/2017/05/websocket.html)

websocket 是一种网络通信协议，诞生于2008年，2011年成为国际标准。所有浏览器都已经支持了。

它的最大特点就是，服务器可以主动向客户端推送信息，客户端也可以主动向服务器发送信息，是真正的双向平等对话，属于服务器推送技术的一种。

![](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017051502.png)

**其他特点包括：**
- 建立在 TCP 协议之上，服务器端的实现比较容易。
- 与 HTTP 协议有着良好的兼容性。默认端口也是80和443，并且握手阶段采用 HTTP 协议，因此握手时不容易屏蔽，能通过各种 HTTP 代理服务器。
- 数据格式比较轻量，性能开销小，通信高效。
- 可以发送文本，也可以发送二进制数据。
- 没有同源限制，客户端可以与任意服务器通信。
- 协议标识符是ws（如果加密，则为wss），服务器网址就是 URL。 如ws://example.com:80/some/path
![](http://www.ruanyifeng.com/blogimg/asset/2017/bg2017051503.jpg)

### 客户端的简单示例
```
var ws = new WebSocket("wss://echo.websocket.org");

ws.onopen = function(evt) { 
  console.log("Connection open ..."); 
  ws.send("Hello WebSockets!");
};

ws.onmessage = function(evt) {
  console.log( "Received Message: " + evt.data);
  ws.close();
};

ws.onclose = function(evt) {
  console.log("Connection closed.");
};      
```
### 客户端API

#### WebSocket 构造函数

WebSocket 对象作为一个构造函数，用于新建 WebSocket 实例。
> var ws = new WebSocket('ws://localhost:8080');

执行上面语句之后，客户端就会与服务器进行连接。

#### websocket实例对象的属性值和方法

|Attribute | Type | Description|
|----------|------|------------------------------------------------------------------|
|binaryType | DOMString | A string indicating the type of binary data being transmitted by the connection. This should be either "blob" if DOM Blob objects are being used or "arraybuffer" if ArrayBuffer objects are being used.|
|bufferedAmount | unsigned long | The number of bytes of data that have been queued using calls to send() but not yet transmitted to the network. This value resets to zero once all queued data has been sent. This value does not reset to zero when the connection is closed; if you keep calling send(), this will continue to climb. Read only|
|extensions | DOMString | The extensions selected by the server. This is currently only the empty string or a list of extensions as negotiated by the connection.|
|onclose | EventListener | An event listener to be called when the WebSocket connection's readyState changes to CLOSED. The listener receives a CloseEvent named "close". |
|onerror | EventListener | An event listener to be called when an error occurs. This is a simple event named "error".
|onmessage | EventListener | An event listener to be called when a message is received from the server. The listener receives a MessageEvent named "message".|
|onopen | EventListener | An event listener to be called when the WebSocket connection's readyState changes to OPEN; this indicates that the connection is ready to send and receive data. The event is a simple one with the name "open".|
|protocol | DOMString | A string indicating the name of the sub-protocol the server selected; this will be one of the strings specified in the protocols parameter when creating the WebSocket object.|
|readyState | unsigned short | The current state of the connection; this is one of the Ready state constants. Read only.|
|url | DOMString | The URL as resolved by the constructor. This is always an absolute URL. Read only.|

#### webSocket.readyState

readyState属性返回实例对象的当前状态，共有四种。
- CONNECTING：值为0，表示正在连接。
- OPEN：值为1，表示连接成功，可以通信了。
- CLOSING：值为2，表示连接正在关闭。
- CLOSED：值为3，表示连接已经关闭，或者打开连接失败。
 
  **实例**
  ```
  switch (ws.readyState) {
    case WebSocket.CONNECTING:
      // do something
      break;
    case WebSocket.OPEN:
      // do something
      break;
    case WebSocket.CLOSING:
      // do something
      break;
    case WebSocket.CLOSED:
      // do something
      break;
    default:
      // this never happens
      break;
  }
  ```

#### webSocket.onopen

实例对象的onopen属性，用于指定连接成功后的回调函数。
```
ws.onopen = function () {
  ws.send('Hello Server!');
}
```
如果要指定多个回调函数，可以使用addEventListener方法。
```
ws.addEventListener('open', function (event) {
  ws.send('Hello Server!');
});
```

#### webSocket.onclose

实例对象的onclose属性，用于指定连接关闭后的回调函数。
```
ws.onclose = function(event) {
  var code = event.code;
  var reason = event.reason;
  var wasClean = event.wasClean;
  // handle close event
};

ws.addEventListener("close", function(event) {
  var code = event.code;
  var reason = event.reason;
  var wasClean = event.wasClean;
  // handle close event
});
```
#### webSocket.onmessage

实例对象的onmessage属性，用于指定收到服务器数据后的回调函数。

```
ws.onmessage = function(event) {
  var data = event.data;
  // 处理数据
};

ws.addEventListener("message", function(event) {
  var data = event.data;
  // 处理数据
});
```

注意，服务器数据可能是文本，也可能是二进制数据（blob对象或Arraybuffer对象）。
```
ws.onmessage = function(event){
  if(typeof event.data === String) {
    console.log("Received data string");
  }

  if(event.data instanceof ArrayBuffer){
    var buffer = event.data;
    console.log("Received arraybuffer");
  }
}
```
除了动态判断收到的数据类型，也可以使用binaryType属性，显式指定收到的二进制数据类型。
```
// 收到的是 blob 数据
ws.binaryType = "blob";
ws.onmessage = function(e) {
  console.log(e.data.size);
};

// 收到的是 ArrayBuffer 数据
ws.binaryType = "arraybuffer";
ws.onmessage = function(e) {
  console.log(e.data.byteLength);
};
```
#### webSocket.send()

实例对象的send()方法用于向服务器发送数据。

发送文本的例子。 
```
ws.send('your message');
```
发送 Blob 对象的例子。
```
var file = document
  .querySelector('input[type="file"]')
  .files[0];
ws.send(file);
```
发送 ArrayBuffer 对象的例子。
```
// Sending canvas ImageData as ArrayBuffer
var img = canvas_context.getImageData(0, 0, 400, 320);
var binary = new Uint8Array(img.data.length);
for (var i = 0; i < img.data.length; i++) {
  binary[i] = img.data[i];
}
ws.send(binary.buffer);
```

#### webSocket.bufferedAmount

实例对象的bufferedAmount属性，表示还有多少字节的二进制数据没有发送出去。它可以用来判断发送是否结束。
```
var data = new ArrayBuffer(10000000);
socket.send(data);

if (socket.bufferedAmount === 0) {
  // 发送完毕
} else {
  // 发送还没结束
}
```

#### webSocket.onerror

实例对象的onerror属性，用于指定报错时的回调函数。
```
socket.onerror = function(event) {
  // handle error event
};

socket.addEventListener("error", function(event) {
  // handle error event
});
```
