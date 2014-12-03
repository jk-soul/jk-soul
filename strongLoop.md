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

从关联数据库中发现模型
===================
*   概览
*   简单的发现
*   额外的发现功能

概览
----
LoopBack 能够简化从现有的关联数据库中生成模型，这个过程被称为发现，并需要以下连接器的支持：
*   MySQL connector
*   PostgreSQL connector
*   Oracle connector
*   SQL Server connector
对于NoSQL数据库如MongoDB，使用实例自省代替。
连接到关联数据库的数据源自动的异步获取到 Database discovery API.

示例
----
例如，考虑一个Oracle 数据库，首先，代码设置数据源，然后从数据表中调用discoverAndBuildModels（）来创建模型，调用他的associations，true表示使其发现遵循主键或外键的关系。
/server/bin/script.js
```javascript
var loopback = require('loopback');
var ds = loopback.createDataSource('oracle', {
  "host": "demo.strongloop.com",
  "port": 1521,
  "database": "XE",
  "username": "demo",
  "password": "L00pBack"
});

// Discover and build models from INVENTORY table
ds.discoverAndBuildModels('INVENTORY', {visited: {}, associations: true},
function (err, models) {
  // Now we have a list of models keyed by the model name
  // Find the first record from the inventory
  models.Inventory.findOne({}, function (err, inv) {
    if(err) {
      console.error(err);
      return;
    }
    console.log("\nInventory: ", inv);
    // Navigate to the product model
    inv.product(function (err, prod) {
      console.log("\nProduct: ", prod);
      console.log("\n ------------- ");
    });
  });
})
```

其他发现功能
---------
/server/bin/script.js
```javascript
// List database tables and/or views
ds.discoverModelDefinitions({views: true, limit: 20}, cb);

// List database columns for a given table/view
ds.discoverModelProperties('PRODUCT', cb);
ds.discoverModelProperties('INVENTORY_VIEW', {owner: 'STRONGLOOP'}, cb);

// List primary keys for a given table
ds.discoverPrimaryKeys('INVENTORY',  cb);

// List foreign keys for a given table
ds.discoverForeignKeys('INVENTORY',  cb);

// List foreign keys that reference the primary key of the given table
ds.discoverExportedForeignKeys('PRODUCT',  cb);

// Create a model definition by discovering the given table
ds.discoverSchema(table, {owner: 'STRONGLOOP'}, cb);
```

Database discovery API
======================
*   概览
    *   同步方法
*   方法
    *   discoverAndBuildModels
    *   discoverModelDefinitions
    *   discoverModelProperties
    *   discoverPrimaryKeys
    *   discoverForeignKeys
    *   discoverExportedForeignKeys
    *   discoverSchemas
*   通过发现创建模型的例子

概览
-----
LoopBack提供了统一的API来从关联数据库中发现模型定义的信息，任意的数据库可使用如下对应的API：
*   Oracle: loopback-connector-oracle
*   MySQL: loopback-connector-mysql
*   PostgreSQL: loopback-connector-postgresql
*   SQL Server: loopback-connector-mssql

同步方法
-------
下面介绍的方法是异步的，对于Oracle，也有相应的同步方法，完成同样的东西和返回相同的结果：
*   discoverModelDefinitionsSync(options)
*   discoverModelPropertiesSync(table, options)
*   discoverPrimaryKeysSync(table, options)
*   discoverForeignKeysSync(table, options)
*   discoverExportedForeignKeysSync(table, options)

discoverAndBuildModels
------------------------
从指定的owner/modelName中发现并创建模型。
dataSource.discoverAndBuildModels(modelName [, options] [, cb])

discoverModelDefinitions
------------------------
通过调用discoverModelDefinitions（）来获取模型定义，建立在表格或者数据源集合上，该方法返回table/view的名字。
discoverModelDefinitions(options, cb)

discoverModelProperties
------------------------
调用discoverModelProperties（）发现数据表的列中的元数据，此方法返回一个特定的表/视图的列信息。
iscoverModelProperties(table, options, cb)
。。。


从非结构化数据中创建模型
======================
非结构化数据如，NoSQL数据库和其它服务，您可以使用实例自省创建模型。实例自省使用buildModelFromInstance（）从单个模型实例中创建模型。有效数据库为：
*   MongoDB data sources
*   REST data sources
*   SOAP data sources
例：
/server/boot/script.js
```javascript
module.exports = function(app) {
  var db = app.dataSources.db;

  // Instance JSON document
  var user = {
    name: 'Joe',
    age: 30,
    birthday: new Date(),
    vip: true,
    address: {
      street: '1 Main St',
      city: 'San Jose',
      state: 'CA',
      zipcode: '95131',
      country: 'US'
    },
    friends: ['John', 'Mary'],
    emails: [
      {label: 'work', id: 'x@sample.com'},
      {label: 'home', id: 'x@home.com'}
    ],
    tags: []
  };

  // Create a model from the user instance
  var User = db.buildModelFromInstance('User', user, {idInjection: true});

  // Use the model for CRUD
  var obj = new User(user);

  console.log(obj.toObject());

  User.create(user, function (err, u1) {
    console.log('Created: ', u1.toObject());
    User.findById(u1.id, function (err, u2) {
      console.log('Found: ', u2.toObject());
    });
  });
});
```























































































