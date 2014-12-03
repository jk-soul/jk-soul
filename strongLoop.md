使用内置的模型
=============
*   概述
*   应用程序模型
*   用户模型
*   访问控制器模型
        *   ACL模型
*   邮件模型
        *   发送邮件消息
        *   确认邮件地址

概述
----
LoopBack为常见的案例提供了十分有用的内置模型。
*   应用程序模型 -包含一个有自己身份和连接lookBack服务器的相关配置的客户端应用程序的元数据。
*   从现有的关系中创建模型：通过发现API，然后你能通过LookBackd的Schema/model同步API来使你的模型与数据库同步。
*   通过实例自省创建

在JavaScript引用模型
-------------------
你在js代码中指向模型的引用依赖于这段代码在哪

在模型的js文件中
--------------
在模型的js文件中，（举个栗子）模型已经传递到顶级的函数中，那么这个模型就可以直接被使用，例如：
/common/models/model.js
```javascript
module.exports = function(Customer) {
  Customer.create( ... );  // Customer object is available
  ...
```
在引导javascript中
-----------------
在一个引导js中，通过使用app.model来引用任意一个模型，例如
/server/boot/script.js
```javascript
module.exports = function(app) {
  var User = app.models.user;
  var Role = app.models.Role;
  var RoleMapping = app.models.RoleMapping;
  var Team = app.models.Team;
  ...
```

使用模型生成器
============
*   概率
*   基本使用
*   添加元素

概览
----
创建一个新的模型的最简单方法是使用模型生成器。创建一个新的模型时，发电机将提示您该模型的性质。随后，您可以使用属性生成器添加更多的性能。

当你创建一个模型（举个栗子，称为“myModel”）,改工具：
*   创建common/models/myModel.json，这是个模型定义的JSON文件。
*   创建/common/models/myModel.js，你可以通过编程方式延伸模型，看Adding application logic以获得更多信息。
*   为模型添加一个入口到/server/model-config.json，指定使用的数据源，看 model-config.json 获得更多信息

基本使用
--------
使用LoopBack 的模型生成器创建新模型，在你应用程序的根文件中，输入该命令（例如，创建一个模型叫“books”）：
$ slc loopback:model book
 然后slc会提示你：
*   从内置的模型的类中或应用程序中自定义的模型类中选择模型的基类。
*   选择连接那个数据库，默认为内存中唯一的数据库（名为"db"）,当你通过Data source generator添加更多的数据库时，他们将作为选项列出
*   选择是否暴露模型（over REST）；默认为是。
*   输入一个自定义的复数形式；默认是使用默认的复数形式（例如，“books”）。
*   为模型添加属性；对于每个属性都会提示你：
    *   名称
    *   类型，这个可在 Model definition JSON file中设置类型
    *   该属性是否为必须的，可在Model definition JSON file中设置改属性

添加元素
--------
在你添加模型后，可以通过 Property generator生成更多属性
$ slc loopback:property
然后slc会提示你选择哪些你想添加属性的模型。随着其他属性的设置（方法如前），slc随后会据此修改Model definition JSON file。




























































































