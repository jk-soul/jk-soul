http://blog.csdn.net:81/violet_day/article/details/17403207?a=1&b=2&c=3#hi


*  *protocol*       协议    http://   svn://    ftp://
*  `hostname`       blog.csdn.net   域名
*  `path`           /violet_day/article/details/17403207     路径
*  `port `          :81   端口号   默认端口为80
*  `queryString`    a=1&b=2&c=3    键值对
*  `fragment`       hi             碎片


*   `method – {string} `  HTTP方法
*   `rl – {string}`       被需求的资源的绝对或相对的URL
*   `params – {Object.<string|Object>}`  紧跟在url之后的，如果值不是个字符串，那一定是个json
*   `data – {string|Object}`             作为需求的信息的数据被发出
*   `headers – {Object}`                 发送HTTP header到服务器，如果函数返回的是个null，那么HTTP header不会被发送。
*   


$modal为快速创建AngularJS供电模式窗口的服务。创建一个自定义模态很简单：创建一部分视图，还有控制器，并且在使用服务室引用它们。在$modal中只有一个方法,`open(options)`其中可用的选项如下图表：
*   `templateUrl`   模板的路径代表了模态的内容。
*   `template`      内置模板表示模态的内容。
*   `scope`         一个作用域的实例被用为模态的内容（实际上是$modal服务将创建一个提供作用域的子作用域）默认的为 $rootScope。
*   `controller `   一个模态实例的控制器，它能通过modal初始化作用域。控制器可以用$modalInstance注入。
*   `resolve `      成员将会被解决，并作为当地传递到控制器上，它相当于AngularJS路线的决心属性。
*   `backdrop`      控制存在的背景。允许的值：true(默认)，false（无背景）。`static` 背景是存在的，但在模态窗口外单击时的模态窗口未关闭。
*   `keyboard `     指出对话框是否能在敲击ESC键时关闭，默认值为true。
*   `windowClass`   额外的CSS类（ES）被添加到一个模态窗口模板
*   
`open`方法返回一个模式实例，一个对象具有以下属性：
*   `close(result)` 这个方法可以用关闭模态，并且传递结果。
*   `dismiss(reason)`可以用来撤销一个模态，传递一个方法。
*   `result`        当一个modal被撤销时，模态被关闭和拒绝时承诺会被解决。
*   `opened`        当一个模式在下载内容的模板，并解决所有变量之后被打开，承诺被解决。
*   
除了与模态的内容相关联的范围扩充2个方法： * `$close(result)` * `$dismiss(reason)`，这些方法可以很容易地关闭模态窗口而不需要创建一个专用控制器
