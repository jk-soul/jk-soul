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

其他发现功能1
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

定制模型
=======
一旦你用模型生成器创建模型(slc loopback:model)，你就可以开始定制它了。你可以使用slc来定制、通过编辑Model definition JSON file和添加js代码。
*   通过slc定制模型
*   使用JSON定制模型
    *   扩展另一个模型
    *   制定另一个模型的设置
*   通过js代码定制模型
    *   改变内置方法的实现

通过slc定制模型
--------------
一旦你用模型生成器创建模型(slc loopback:model)，你就不能使用模型生成器修改该模型了。
你可以使用slc定制一个刚创建的模型，具体如下：
*   使用 slc loopback:property添加模型的属性。查看 Property generator 以了解更多。
*   使用 slc loopback:relation在模型之间添加关系。查看 Relation generator 以了解更多。
*   使用 slc loopback:acl添加模型的访问权限，查看 ACL generator 以了解更多。

通过JSON定制模型
----------------
你可以定义模型的很多面通过编辑在/common/models中的模型JSON（例如，customer.json），默认值如下：
/common/models/model.json
```javascript
{
  "name": "myModel",
  "base": "PersistedModel",
  "properties": {
     // Properties listed here depend on your responses to the CLI
  },
  "validations": [],
  "relations": {},
  "acls": [],
  "methods": []
}
```

扩展其他模型
-----------
你可以做一个模型扩展或从一个现有的模型继承，可以使像User这样内置的模型，或者你在应用程序中定义的模型。
编辑模型的JSON文件，并且设置你想要扩展的模型的名字的基本属性，内置的模型或者用户在应用程序中定义的模型。
例如：下面是 loopback-example-app中的一个片段，通过继承内置的User模型来定义Customer模型
common/models/model.json
```javascript
{
  "name": "Customer",
  "base": "User",
  "idInjection": false,
...
```
总的来说，你可以用这种方法扩展任意模型，不仅仅是内置的模型。

你可以从一个基本的定义模型中扩展成自定义模型。例如，要从一个你已经定义的mMyBaseModel的模型中定义一个叫做MyModel 的自定义模型，，需要通过slc loopback:model 创建MyModel，然后编辑common/models/MyModel.json如下:
/common/models/model.json
```javascript
{
  "name": "Example",
  "base": "MyBaseModel",
}
```
当你扩展一个模型的时候你可以添加新的元素：
/common/models/model.json
```javascript
{
   "name": "Customer",
   "base": "User",
   "properties": {
      "favoriteMovie": {
        "type": "string"
      }
   }
}
```
定义其他模型设置
--------------
这里有一些很重要的属性你可以去定义
*   plural 设置一个String类型的值用来代替标准的复数形式
*   strict 设置成true使得一个模型接受预先设置好的属性,默认为false
*   idInjection 是否自动为一个模型添加id属性，默认为false
*   http.path customized HTTP path of REST endpoints

用js代码定义一个模型
---------------------
定义模型最经典的方法是以编程方式编辑 common/models/ directory的js文件，例如，一个customer模型会有/common/models/customer.js 文件（如果你是使用 slc loopback:model创建模型的话），
脚本会在模型定义完成后立刻执行，把脚本作为模型定义的一部分，用它来做模型的配置及登记，你也可以添加模型关系，复杂的验证，默认的函数，或者已经确定的属性，基本上是你在JSON中不能做的事。
注意，在这一点上脚本没有访问app实例。



改变内置模型的实现
----------------
当你将一个模型连接到持久型数据库时，它将变成一个扩展了 PersistedModel的持久型模型，并且LoopBack 会为CRUD 操作添加一些内置操作的设置。在某些情况下，你也可以改变这些实现。使用/server/boot
可以做到。下面的例子就实现了用Note.find()重写了内置的方法 find()：
/server/boot/script.js
```javascript
module.exports = function(app) {
  var Note = app.models.Note;
  var find = Note.find;
  var cache = {};

  Note.find = function(filter, cb) {
    var key = '';
    if(filter) {
      key = JSON.stringify(filter);
    }
    var cachedResults = cache[key];
    if(cachedResults) {
      console.log('serving from cache');
      process.nextTick(function() {
        cb(null, cachedResults);
      });
    } else {
      console.log('serving from db');
      find.call(Note, function(err, results) {
        if(!err) {
          cache[key] = results;
        }
        cb(err, results);
      });;
    }
  }
}
```

连接模型到数据库
===============
*   概览
*   添加数据库
*   添加数据源验证
*   让模型使用数据库

概览
-----
一个数据源能够是一个模型在后台连接并修改数据，就像一个关联的数据库。数据源封装了用于在模型与像关系型数据库这样各种后台系统之间的进行数据交互的代码，这样的后台系统还有REST APIs,SOAP web services, storage services
等等，数据源一般提供创建，检索，更新，和删除（CRUD）这几类函数。
模型通过可拓展可定制的连接器访问数据源。一般而言，应用程序代码是不会直接使用连接器的。相反，数据源的类通过了一个API来配置下层的连接器。
默认的，slc创建一个易于研发的 memory connector,使用一个不同的数据源：
1.  使用 slc loopaback:datasource创建一个新的数据源，并将其添加到应用程序的datasources.json中去。
2.  编辑 datasources.json，为数据源添加一个证书。
3.  创建一个模型连接到数据源，或者修改一个存在的模型定义来使用连接器。

添加一个数据源
=============
使用数据源生成器添加一个新的数据源：
shell
$ slc loopback:datasource
它会提示你新数据源的名字，以及使用哪个连接器。例如：MySQL, Oracle, REST等等。该工具将添加一个条目，如下面的datasources.json：
/server/datasources.json
```json
...
  "corp1": {
    "name": "corp1",
    "connector": "mysql"
  }
...
```
这个例子创建了一个叫corp1的数据源，标示符确定的名称指向的是数据源，也可以使任意字符串。

添加数据源凭证
-------------
编辑 datasources.json 为数据源添加必要的验证。典型的 主机名、用户名、密码和数据源名称。例：
/server/datasources.json
```json
"corp1": {
    "name": "corp1",
    "connector": "mysql",
    "host": "your-mysql-server.foo.com",
    "user": "db-username",
    "password": "db-password",
    "database": "your-db-name"
  }
```
让模型使用数据源
--------------
编辑应用程序中的模型定义的JSON文件，来设置被模型使用的数据源：例如，使用之前叫mysql的例，如果你按之前 Creating an application 中创建了例子books的数据源，编辑他的dataSource属性为corp1
来使用相应的MySQL 数据库：
/common/models/model.json
```json
"book": {
   "properties": {
     ...
   },
   "public": true,
   "dataSource": "corp1",
   "plural": "books"
 }
```
在REST上暴露模型
=================
*   概览
    *   REST路径
    *   使用REST路由。
    *   请求格式
        *   使用HTTP查询字符串传递JSON格式的object或数组
    *   相应格式
    *   禁用API浏览器
    *   预定义远程方法
*   暴露模型
    *   隐藏方法以及其他端点
    *   为相关模型隐藏端点

概览
-----
LoopBack 会自动有一套标准的HTTP端点,其可以提供 REST APIs在模型上创建，读取，更新和删除操作。在model-config.json中的公共属性可以指定是否暴露模型的REST APIs。
/server/model-config.json
```json
...
  "MyModel": {
    "public": true,
    "dataSource": "db"
  },
...
```
REST路径
--------
默认的，REST的API会安装到模型的复数形式下，具体的：
*   Model.settings.http.path
*   Model.settings.plural，如果定义在 models.json中，查看 Project layout reference以了解更多。
*   自动化多元模型名称

使用REST路由
------------
默认情况下，脚手架应用程序暴露在使用 loopback.rest路由的REST上
/server/server.js
```javascript
var app = loopback();
app.use(loopback.rest());

// Expose the `Product` model
app.model(Product);
```
之后，模型Product 将会有 create, read, update, and delete (CRUD) functions 能够从移动端远程。

请求格式
--------
对于For POST and PUT 请求，请求体可以使JSON、XML 和urlencoded 格式
POST /api/Notes HTTP/1.1
Host: localhost:3000
Connection: keep-alive
Content-Length: 61
Accept: application/json
Content-Type: application/json

{
  "title": "MyNote",
  "content": "This is my first note"
}

用HTTP请求传递JSON对象或数组
有些REST的API接收请求中的JSON对象或者数组，LoopBack 支持两种编码格式的object/array值作为查询参数。
*   node-querystring (qs)的语法
*   字符串化的JSON
例：
http://localhost:3000/api/users?filter[where][username]=john&filter[where][email]=callback@strongloop.com
http://localhost:3000/api/users?filter={"where":{"username":"john","email":"callback@strongloop.com"}}

下面的表格显示了如何编译在不同形式下编译的JSON object/array。

验证模型的数据
=============


创建模型关系
===========
模型关系概览
-----------
单个模型易于理解和使用。但在现实中，模型之间通常连接或与相关联的。当你建立一个多模型的实际应用，你通常会需要定义模型之间的关系。例如：
*   一个客户有多个订单，而一个订单会有属于一个客户。
*   等等



你可以再模型之间定义如下关系：
*   BelongsTo relations
*   HasMany relations
*   HasManyThrough relations
*   HasAndBelongsToMany relations
*   Polymorphic relations
*   Embedded relations (embedsOne and embedsMany)

你可以再模型的JSON文件中定义模型关系或者在js代码中，结果都是一样的。当你创建一个模型的关系时，LoopBack 会为模型添加一组方法作为该类关系的
详细描述。

Relation options
大部分关系类型都有这三个选项：
*   Scope
*   Properties
*   Custom scope methods

Scope
Scope属性可以使对象也可以使函数，适用于所有的过滤/条件对相关Scope
该对象，或者返回的对象（函数被调用时）有所有的一般过滤选项：where, order, include, limit, offset, ..
These options are merged into the default filter, which means that the where part will be AND-ed. The other options usually override the defaults (standard mergeQuery behavior).
当scope是个函数时，他接收一个当前实例以及默认的筛选对象。
```javascript

// only allow products of type: 'shoe', always include products
Category.hasMany(Product,
                 { as: 'shoes',
                   scope: { where: { type: 'shoe' },
                   include: 'products'
});
Product.hasMany(Image,
                { scope: function(inst, filter) {
                           return { type: inst.type };
                         }
});  // inst is a category - match category type with product type.
```

Properties
-------------
你可以有两种方法指定properties 属性：
*   作为一个object:
*   作为一个function:

```javascript
Category.hasMany(Product,
                 { as: 'shoes',
                 properties: { type: 'type', category: 'categoryName' });
```
```javascript
Product.hasMany(Image,
                { properties: function(inst) { // inst is a category
                                return { type: inst.type, categoryName: inst.name };
                              }
});
```

Custom scope methods
-----------------------
最后，你可以使用scopeMethods 属性来自定义，同样，选项可以使object 或者 function 。
```javascript
var reorderFn = function(ids, cb) {
  // `this` refers to the RelationDefinition
  console.log(this.name); // `images` (relation name)
  // do some reordering here & save
  cb(null, [3, 2, 1]);
};

// manually declare remoting params
reorderFn.shared = true;
reorderFn.accepts = { arg: 'ids', type: 'array', http: { source: 'body' } };
reorderFn.returns = { arg: 'ids', type: 'array', root: true };
reorderFn.http = { verb: 'put', path: '/images/reorder' };

Product.hasMany(Image, { scopeMethods: { reorder: reorderFn } });
```
Exposing REST APIs for related models
---------------------------------------
```javascript
var db = loopback.createDataSource({connector: 'memory'});
  Customer = db.createModel('customer', {
    name: String,
    age: Number
  });
  Review = db.createModel('review', {
    product: String,
    star: Number
  });
  Order = db.createModel('order', {
    description: String,
    total: Number
  });

  Customer.scope("youngFolks", {where: {age: {lte: 22}}});
  Review.belongsTo(Customer, {foreignKey: 'authorId', as: 'author'});
  Customer.hasMany(Review, {foreignKey: 'authorId', as: 'reviews'});
  Customer.hasMany(Order, {foreignKey: 'customerId', as: 'orders'});
  Order.belongsTo(Customer, {foreignKey: 'customerId'});
```
































































