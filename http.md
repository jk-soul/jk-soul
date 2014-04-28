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
