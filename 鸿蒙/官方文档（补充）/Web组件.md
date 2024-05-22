#### 建立应用侧与前端页面数据通道

createWebMessagePorts()

```ts
try {
    //1 创建两个消息端口
    this.ports = this.controller.createWebMessagePorts();
    //2 在应用侧的消息端口（如端口1）上注册回调事件
    this.ports[1].onMessageEvent((result: web_webview.WebMessage) => {
        this.receivedFromHtml = `Message: ${result}`;
    })
    //3 将另一个消息端口（如端口0）发送到HTML侧，由HTML侧保存并使用
    this.controller.postMessage('__init_port__', [this.ports[0]],'*')
} catch (error) {
    hilog.error(0,"BrowserDemo",`ErrorCode: ${error.code}, Message: ${error.message}`)
}

//4 发送消息
try {
    if (this.ports && this.ports[1]) {
        this.ports[1].postMessageEvents(this.sendFromEts);
    } else {
        hilog.error(0,"BrowserDemo",`ports is null, please initialize first`);
    }
} catch (error) {
    hilog.error(0,"BrowserDeom",`ErrorCode: ${error.code}, Message: ${error.message}`);
}
```



```js
var h5Port;
var output = document.querySelector('.output');
window.addEventListener('message', function (event) {
    console.log(JSON.stringify(event))
    if (event.data === '__init_port__') {
        if (event.ports[0] !== null) {
            h5Port = event.ports[0];//1 保存从ets侧发送过来的端口
            h5Port.onmessage = function (event) {
                //2 接受ets侧发送过来的消息
                output.innerHTML = `Message: ${event.data}`;
            }
        }
    }
})

//3 使用h5Port往ets侧发送消息
function PostMsgToEts(data) {
    if (h5Port) {
        h5Port.postMessage(data);
    } else {
        console.error('h5Port is null, please initialize first');
    }
}
```



#### Q&A

Q：LocalStorage与ets组件共享数据吗？

A：不行，WebView单独线程，与UIAbility、ServiceExtensionAbility不在一个线程，所以不共享。



Q：web组件加载pdf吗？

A：官方默认目前不行，尝试三方组件。

（后台将pdf转base64）



