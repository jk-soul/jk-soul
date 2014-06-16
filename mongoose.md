
`$ npm install mongoose`

```javascript
var mongoose = require('mongoose');
mongoose.connect('mongodb://localhost/test');

var db = mongoose.connection;
db.on('error', console.error.bind(console, 'connection error:'));
db.once('open', function callback () {
    // yay!
    console.log('open')
});

var kittySchema = mongoose.Schema({
    name: String
});
```


```javascript
var Kitten = mongoose.model('Kitten', kittySchema);

var silence = new Kitten({ name: 'Silence' });
console.log(silence); // 'Silence'
```


```javascript
// NOTE: methods must be added to the schema before compiling it with mongoose.model()
kittySchema.methods.speak = function () {
    var greeting = this.name
        ? "Meow name is " + this.name
        : "I don't have a name";
    console.log(greeting);
};

var Kitten = mongoose.model('Kitten', kittySchema);

var fluffy = new Kitten({ name: 'fluffy' });
fluffy.speak(); // "Meow name is fluffy"
```

定义自己的schema
----------------
mongoose中任何东西都是由一个schema开始的。每一个schema都指向到一mongoDB中的一个集合，定义该集合中文档的样子。   
```javascript
var mongoose = require('mongoose');
var Schema = mongoose.Schema;

var blogSchema = new Schema({
  title:  String,
  author: String,
  body:   String,
  comments: [{ body: String, date: Date }],
  date: { type: Date, default: Date.now },
  hidden: Boolean,
  meta: {
    votes: Number,
    favs:  Number
  }
});    
```
如果你想在以后添加附加建，可以使用 Schema#add方法。
在blogSchema中的每个键都定义了文档中的一个属性，其会被转化到关联的SchemaType中去，例如我们定义一个会被转化成字符串的标题和一个会被转化成日期格式的日期，键可以值能够包含更多的键/类型定义（如``元以上物业）嵌套对象。
允许的SchemaTypes 有
-   String
-   Number
-   Date
-   Buffer
-   Boolean
-   Mixed
-   ObjectId
-   Array

Schemas 不仅定义你文档和属性的结构，还定义了文档实例的方法，静态模型方法，复合索引和被称为中间件的文件生命周期挂钩。

实例方法
--------
模型的实例是文档。文档有很多内置的方法，我们也可以定义自己的文件实例方法。
```javascript
// define a schema
var animalSchema = new Schema({ name: String, type: String });

// assign a function to the "methods" object of our animalSchema
animalSchema.methods.findSimilarTypes = function (cb) {
  return this.model('Animal').find({ type: this.type }, cb);
}


var Animal = mongoose.model('Animal', animalSchema);
var dog = new Animal({ type: 'dog' });

dog.findSimilarTypes(function (err, dogs) {
  console.log(dogs); // woof
});
```

静态
----
将静态方法添加到文档中也是非常简单的。
```javascript
// assign a function to the "statics" object of our animalSchema
animalSchema.statics.findByName = function (name, cb) {
  this.find({ name: new RegExp(name, 'i') }, cb);
}

var Animal = mongoose.model('Animal', animalSchema);
Animal.findByName('fido', function (err, animals) {
  console.log(animals);
});
```

索引
-----
MongoDB是支持辅助索引的，同过mongo，我们定义通道层次和架构水平中的索引，创建复合索引时，在架构级别定义指标是必要的。
```javascript
var animalSchema = new Schema({
  name: String,
  type: String,
  tags: { type: [String], index: true } // field level
});

animalSchema.index({ name: 1, type: -1 }); // schema level
```
虚函数
------
虚函数的文档属性，可以获取和设置但没有得到持久化到MongoDB的。
```javascript
// define a schema
var personSchema = new Schema({
  name: {
    first: String,
    last: String
  }
});

// compile our model
var Person = mongoose.model('Person', personSchema);

// create a document
var bad = new Person({
    name: { first: 'Walter', last: 'White' }
});
```

假设我们要记录不完整的全名。我们可以手动执行此操作，如下所示：
```javascript
console.log(bad.name.first + ' ' + bad.name.last); // Walter White
```
或者我们可以在我们的personSchema中定义一个虚拟的属性，因此我们并不需要每次都写出这个字符去连接。
```javascript
personSchema.virtual('name.full').get(function () {
  return this.name.first + ' ' + this.name.last;
});
```
现在我们调用虚拟的"name.full"属性，我们的getter函数将被调用，并返回的值：
```javascript
console.log('%s is insane', bad.name.full); // Walter White is insane
```
请注意，如果所产生的记录转换为一个对象或JSON，虚函数不在默认情况之内。同过虚函数： true to either toObject() or to toJSON() to have them returned.
这是不错的方法同过设置this.name.full来设置this.name.first和this.name.last
```javascript
bad.name.full = 'Breaking Bad';
```
mongoose可以让你做到这一点，以及通过其虚拟属性设置：
```javascript
ersonSchema.virtual('name.full').set(function (name) {
  var split = name.split(' ');
  this.name.first = split[0];
  this.name.last = split[1];
});

...

mad.name.full = 'Breaking Bad';
console.log(mad.name.first); // Breaking
console.log(mad.name.last);  // Bad
```
虚拟属性的设置是在其他验证之前，因此上面的例子中，第一个和最后一个名字都被需求，仍然有用。

Options
--------
Schemas 拥有几个可配置属性能够传递给构造函数或者直接设置。
```javascript
new Schema({..}, options);

// or

var schema = new Schema({..});
schema.set(option, value);
```
有效选项
autoIndex
- capped
- collection
- id
- _id
- read
- safe
- shardKey
- strict
- toJSON
- toObject
- versionKey

选项：autoIndex
-------------------
在应用程序启动时，mongoose在你的Schema中都每个索引都发送了ensureIndex命令，由于是mongoosV3，索引是在后台默认情况下创建的，如果你想禁用自动创建功能和手动创建索引时处理，你的架构自动索引选项设置为false，并使用你的模型ensureIndexes方法。
```javascript
var schema = new Schema({..}, { autoIndex: false });
var Clock = mongoose.model('Clock', schema);
Clock.ensureIndexes(callback);
```
选项：bufferCommands
--------------------
When running with the drivers autoReconnect option disabled and connected to a single mongod (non-replica-set), mongoose buffers commands when the connection goes down until you manually reconnect. To disable mongoose buffering under these conditions, set this option to false.
```javascript
var schema = new Schema({..}, { bufferCommands: false });
```
选项：capped
-------------
mongoose支持mongoDB的封顶集合，要指定相关的MongoDB集合封顶，设置上限选项，集合的最大尺寸为以字节为单位。
```javascript
new Schema({..}, { capped: 1024 });
```
限制选项也可以被设置为一个对象，如果你想传输附加选项，比如max 或 autoIndexId.
```javascript
var dataSchema = new Schema({..}, { collection: 'data' });
```
选项：id
----------
mongoose通过在默认情况下同过返回文档的_id给每个模型分配一个id，
```javascript
// default behavior
var schema = new Schema({ name: String });
var Page = mongoose.model('Page', schema);
var p = new Page({ name: 'mongodb.org' });
console.log(p.id); // '50341373e894ad16347efe01'

// disabled id
var schema = new Schema({ name: String }, { id: false });
var Page = mongoose.model('Page', schema);
var p = new Page({ name: 'mongodb.org' });
console.log(p.id); // undefined
```
选项：_id
------------
```javascript
// default behavior
var schema = new Schema({ name: String });
var Page = mongoose.model('Page', schema);
var p = new Page({ name: 'mongodb.org' });
console.log(p); // { _id: '50341373e894ad16347efe01', name: 'mongodb.org' }

// disabled _id
var schema = new Schema({ name: String }, { _id: false });

// Don't set _id to false after schema construction as in
// var schema = new Schema({ name: String });
// schema.set('_id', false);

var Page = mongoose.model('Page', schema);
var p = new Page({ name: 'mongodb.org' });
console.log(p); // { name: 'mongodb.org' }

// MongoDB will create the _id when inserted
p.save(function (err) {
  if (err) return handleError(err);
  Page.findById(p, function (err, doc) {
    if (err) return handleError(err);
    console.log(doc); // { name: 'mongodb.org', _id: '50341373e894ad16347efe12' }
  })
})
```
选项：read
-----------
允许在模式的级别设置查询#读取选项，提供给我们一种方法，对所有来自同一个模型的查询提供了默认的 ReadPreferences。
```javascript
var schema = new Schema({..}, { read: 'primary' });            // also aliased as 'p'
var schema = new Schema({..}, { read: 'primaryPreferred' });   // aliased as 'pp'
var schema = new Schema({..}, { read: 'secondary' });          // aliased as 's'
var schema = new Schema({..}, { read: 'secondaryPreferred' }); // aliased as 'sp'
var schema = new Schema({..}, { read: 'nearest' });            // aliased as 'n'
```
```javascript
// pings the replset members periodically to track network latency
var options = { replset: { strategy: 'ping' }};
mongoose.connect(uri, options);

var schema = new Schema({..}, { read: ['nearest', { disk: 'ssd' }] });
mongoose.model('JellyBean', schema);
```
选项：safe
-------------
这个选项是向mongo传递所有指令操作的，如果出错就会调用回调函数。
```javascript
var safe = true;
new Schema({ .. }, { safe: safe });
```
```javascript
var safe = { w: "majority", wtimeout: 10000 };
new Schema({ .. }, { safe: safe });
```
选项：shardKey
--------------
shardKey选项是在我们有分片MongoDB架构使用的，每个分片集合给出一个分片键，其必须出现在所有的插入/更新操作。我们只需要设置该模式选择相同的分片键,and we’ll be all set.
```javascript
new Schema({ .. }, { shardKey: { tag: 1, name: 1 }})
```
选项：strict
------------
strict选项（默认启用）确保值被传递到到模型中，同过构造函数不符合schema的将不会被保持到数据库中。
```javascript
var thingSchema = new Schema({..})
var Thing = mongoose.model('Thing', thingSchema);
var thing = new Thing({ iAmNotInTheSchema: true });
thing.save(); // iAmNotInTheSchema is not saved to the db

// set to false..
var thingSchema = new Schema({..}, { strict: false });
var thing = new Thing({ iAmNotInTheSchema: true });
thing.save(); // iAmNotInTheSchema is now saved to the db!!
```
这也影响doc.set()设置一个属性值的使用。
```javascript
var thingSchema = new Schema({..})
var Thing = mongoose.model('Thing', thingSchema);
var thing = new Thing;
thing.set('iAmNotInTheSchema', true);
thing.save(); // iAmNotInTheSchema is not saved to the db
```
这个值可以通过另一个布尔参数覆盖在模型实例级别:
```javascript
var Thing = mongoose.model('Thing');
var thing = new Thing(doc, true);  // enables strict mode
var thing = new Thing(doc, false); // disables strict mode
```
注意:不要设置为false,除非你有充分的理由。
注意：在mongoose V2中默认值是false。
注意：任何 不存在于你的模式的key/val 设置的实例总是被忽视，无论模式选项。
```javascript
var thingSchema = new Schema({..})
var Thing = mongoose.model('Thing', thingSchema);
var thing = new Thing;
thing.iAmNotInTheSchema = true;
thing.save(); // iAmNotInTheSchema is never saved to the db
```
选项：toJSON
------------
和 toObject 选项一样，文档调用oJSON方法。
```javascript
var schema = new Schema({ name: String });
schema.path('name').get(function (v) {
  return v + ' is my name';
});
schema.set('toJSON', { getters: true, virtuals: false });
var M = mongoose.model('Person', schema);
var m = new M({ name: 'Max Headroom' });
console.log(m.toObject()); // { _id: 504e0cd7dd992d9be2f20b6f, name: 'Max Headroom' }
console.log(m.toJSON()); // { _id: 504e0cd7dd992d9be2f20b6f, name: 'Max Headroom is my name' }
// since we know toJSON is called whenever a js object is stringified:
console.log(JSON.stringify(m)); // { "_id": "504e0cd7dd992d9be2f20b6f", "name": "Max Headroom is my name" }
```
选项：toObject
--------------
文档有一个toObject方法将mongoose文档转换成一个普通的javascript对象。该方法接受几个选项。而不是per-document基础上应用这些选项我们可以宣布这里的选项,它适用于所有默认的模式文档。、
```javascript
var schema = new Schema({ name: String });
schema.path('name').get(function (v) {
  return v + ' is my name';
});
schema.set('toObject', { getters: true });
var M = mongoose.model('Person', schema);
var m = new M({ name: 'Max Headroom' });
console.log(m); // { _id: 504e0cd7dd992d9be2f20b6f, name: 'Max Headroom is my name' }
```
选项：versionKey
----------------
当被mongoose创建时，versionKey就是一个属性集，这个键值包含了内部文档的修订。本文档属性的名称是可配置的。缺省值是__v。如果与您的应用程序冲突，可以配置是这样的:
```javascript
var schema = new Schema({ name: 'string' });
var Thing = mongoose.model('Thing', schema);
var thing = new Thing({ name: 'mongoose v3' });
thing.save(); // { __v: 0, name: 'mongoose v3' }

// customized versionKey
new Schema({..}, { versionKey: '_somethingElse' })
var Thing = mongoose.model('Thing', schema);
var thing = new Thing({ name: 'mongoose v3' });
thing.save(); // { _somethingElse: 0, name: 'mongoose v3' }
```
文档版本也可以通过设置禁用versionKey为false来控制，不要禁用版本,除非你知道你正在做什么。
```javascript
new Schema({..}, { versionKey: false });
var Thing = mongoose.model('Thing', schema);
var thing = new Thing({ name: 'no versioning please' });
thing.save(); // { name: 'no versioning please' }
```
SchemaTypes
============
SchemaTypes处理路径的定义默认值,验证、getter、setter,查询的默认字段，查看各自的API文档的更多细节。
以下是所有有效的模式类型：
- String
- Number
- Date
- Buffer
- Boolean
- Mixed
- Objectid
- Array
```javascript
var schema = new Schema({
  name:    String,
  binary:  Buffer,
  living:  Boolean,
  updated: { type: Date, default: Date.now }
  age:     { type: Number, min: 18, max: 65 }
  mixed:   Schema.Types.Mixed,
  _someId: Schema.Types.ObjectId,
  array:      [],
  ofString:   [String],
  ofNumber:   [Number],
  ofDates:    [Date],
  ofBuffer:   [Buffer],
  ofBoolean:  [Boolean],
  ofMixed:    [Schema.Types.Mixed],
  ofObjectId: [Schema.Types.ObjectId],
  nested: {
    stuff: { type: String, lowercase: true, trim: true }
  }
})
```
// example use

var Thing = mongoose.model('Thing', schema);

var m = new Thing;
m.name = 'Statue of Liberty'
m.age = 125;
m.updated = new Date;
m.binary = new Buffer(0);
m.living = false;
m.mixed = {[ any: { thing: 'i want' } ]};
m.markModified('mixed');
m._someId = new mongoose.Types.ObjectId;
m.array.push(1);
m.ofString.push("strings!");
m.ofNumber.unshift(1,2,3,4);
m.ofDate.addToSet(new Date);
m.ofBuffer.pop();
m.ofMixed = [1, [], 'three', { four: 5 }];
m.nested.stuff = 'good';
m.save(callback);

使用注意事项:

Dates
-----
内置方法即不连接到mongoose日期便能更改跟踪逻辑，这意味着你在文档中使用日期，其修改方法就类似于setMonth(),mongoose不会意识到这种变化,doc.save()不会持续这一修改。如果你必须修改日期类型使用内置的方法,在保存之前告诉mongoose改变doc.markModified(“pathToYourDate”)。
```javascript
var Assignment = mongoose.model('Assignment', { dueDate: Date });
Assignment.findOne(function (err, doc) {
  doc.dueDate.setMonth(3);
  doc.save(callback) // THIS DOES NOT SAVE YOUR CHANGE
  
  doc.markModified('dueDate');
  doc.save(callback) // works
})
```
Mixed
-----
一个怎样都行的SchemaType，他的灵活性来自于权衡更难维护，可以通过Schema.Types混合，文字混合或通过一个空对象。以下是等价的:
```javascript
var Any = new Schema({ any: {} });
var Any = new Schema({ any: Schema.Types.Mixed });
```
因为它是一种非模式化,你可以改变任何你喜欢的值，但是mongoose没有自动保存这些更改的能力，告诉mongoose，一个MIXd的值发生了改变，调用.markModified(路径)方法文档的传递你刚刚改变了的混合型的路径。
```javascript
person.anything = { x: [3, 4, { y: "changed" }] };
person.markModified('anything');
person.save(); // anything will now get saved
```
ObjectIds
---------
指定一个类型的ObjectId,,,在你的声明中使用Schema.Types.ObjectId。
```javascript
var mongoose = require('mongoose');
var ObjectId = mongoose.Schema.Types.ObjectId;
var Car = new Schema({ driver: ObjectId })
// or just Schema.ObjectId for backwards compatibility with v2
```
Arrays
------
提供创建SchemaTypes或Sub-Documents的数字.
```javascript
var ToySchema = new Schema({ name: String });
var ToyBox = new Schema({
  toys: [ToySchema],
  buffers: [Buffer],
  string:  [String],
  numbers: [Number]
  // ... etc
});
```
注:指定一个空数组相当于MIXd。以下所有创建数组的混合:
```javascript
var Empty1 = new Schema({ any: [] });
var Empty2 = new Schema({ any: Array });
var Empty3 = new Schema({ any: [Schema.Types.Mixed] });
var Empty4 = new Schema({ any: [{}] });
```
Creating Custom Types
---------------------
mongoose也可以通过自定义SchemaTypes扩展,搜索插件网站兼容的类型如mongoose-long和其他类型.

model
-------
model是从我们定义的schema中构造函数,这些文档的实例表示可以从数据库中保存与retreived。数据库中所有文档的创建和retreival是通过这些模型。编译您的第一个模型
-------------------
```javascript
var schema = new mongoose.Schema({ name: 'string', size: 'string' });
var Tank = mongoose.model('Tank', schema);
```
构建文件
---------
文档是我们的模型的实例。创建并保存到数据库中是很容易的
```javascript
var Tank = mongoose.model('Tank', yourSchema);

var small = new Tank({ size: 'small' });
small.save(function (err) {
  if (err) return handleError(err);
  // saved!
})

// or

Tank.create({ size: 'small' }, function (err, small) {
  if (err) return handleError(err);
  // saved!
})
```
注意：没有tanks会被创建、移动，直到模型使用被打开。在本例中我们使用mongoose.model()让我们打开默认的mongoose连接:
```javascript
mongoose.connect('localhost', 'gettingstarted');
```
Querying
---------
在mongoose中查找文件时很容易的，其支持的MongoDB查询语法很丰富，文档可以使用每个retreived模型发现,findById,findOne或静态方法。
```javascript
Tank.find({ size: 'small' }).where('createdDate').gt(oneYearAgo).exec(callback);
```
Removing
--------
模型有一个静态去除方法用于删除所有文档匹配条件。
```javascript
Tank.remove({ size: 'large' }, function (err) {
  if (err) return handleError(err);
  // removed!
});
```

Retrieving
==========

Updating
---------
有许多方法来更新文档。我们首先来看一个使用findById的传统方法:
```javascript
Tank.findById(id, function (err, tank) {
  if (err) return handleError(err);
  
  tank.size = 'large';
  tank.save(function (err) {
    if (err) return handleError(err);
    res.send(tank);
  });
});
```
这方法是首先从Mongo中retreiving文档,然后发出一个更新命令(通过调用触发保存)。然而,如果在我们的应用程序中我们不需要返回的文档,仅仅是想直接更新数据库中属性,, Model#update对我们来说是正确的:
```javascript
Tank.update({ _id: id }, { $set: { size: 'large' }}, callback);
```
如果我们需要在我们的应用程序返回的文档,这里还有个更好的选择。
```javascript
Tank.findByIdAndUpdate(id, { $set: { size: 'large' }}, function (err, tank) {
  if (err) return handleError(err);
  res.send(tank);
});
```
Sub Docs
=========
Sub-documents是一个有自己的schema的doc，并且是父类文件数组的一个元素：
```javascript
var childSchema = new Schema({ name: 'string' });

var parentSchema = new Schema({
  children: [childSchema]
})
```
Sub-documents有所有正常文档拥有的功能，唯一的区别是,他们不是单独保存,只有当他们顶级父文档保存时才被保存。
```javascript
var Parent = mongoose.model('Parent', parentSchema);
var parent = new Parent({ children: [{ name: 'Matt' }, { name: 'Sarah' }] })
parent.children[0].name = 'Matthew';
parent.save(callback);
```
如果一个错误发生在sub-documents中间件,充溢save()回调父类,因此错误处理非常简单!
```javascript
childSchema.pre('save', function (next) {
  if ('invalid' == this.name) return next(new Error('#sadpanda'));
  next();
});

var parent = new Parent({ children: [{ name: 'invalid' }] });
parent.save(function (err) {
  console.log(err.message) // #sadpanda
})
```
Finding a sub-document
-----------------------
每个文档都有一个_id。DocumentArrays有特殊的id的方法查找文档_id。
```javascript
var doc = parent.children.id(id);
```
Adding sub-docs
----------------
MongooseArray methods such as push, unshift, addToSet, and others cast arguments to their proper types transparently:
```javascript
var Parent = mongoose.model('Parent');
var parent = new Parent;

// create a comment
parent.children.push({ name: 'Liesl' });
var subdoc = parent.children[0];
console.log(subdoc) // { _id: '501d86090d371bab2c0341c5', name: 'Liesl' }
subdoc.isNew; // true

parent.save(function (err) {
  if (err) return handleError(err)
  console.log('Success!');
});
```
Sub-docs也可能利用MongooseArrays的创建方法被创建而不是通过添加。
```javascript
var newdoc = parent.children.create({ name: 'Aaron' });
```
Removing docs
=============
每个子文档都有它自己的删除方法。
```javascript
var doc = parent.children.id(id).remove();
parent.save(function (err) {
  if (err) return handleError(err);
  console.log('the sub-doc was removed')
});
```
Alternate declaration syntax
=============================
在v3如果你不需要访问子文档模式实例,您也可以声明sub-docs只需传递一个对象文字:

```javascript
var parentSchema = new Schema({
  children: [{ name: 'string' }]
})
```
Queries
-------
文档可以通过几个静态的模型的辅助方法被索引。任何涉及被指定查询条件的模型都可以同过两种方式被查询：
当一个回调函数：
- 通过时，立即将结果传个回调函数。
- 不通过时，返回查询的实例，其提供了一个特殊的接口。
让我们看看同过回调函数都发生了什么：

```javascript
var Person = mongoose.model('Person', yourSchema);

// find each person with a last name matching 'Ghost', selecting the `name` and `occupation` fields
Person.findOne({ 'name.last': 'Ghost' }, 'name occupation', function (err, person) {
  if (err) return handleError(err);
  console.log('%s %s is a %s.', person.name.first, person.name.last, person.occupation) // Space Ghost is a talk show host.
})
```
这里我们看到，查询立即被停止，结果传递给我们的回调函数。在mongoose中的所有回调函数使用的模式是 callback(error, result)。如果查询执行中出现错误，错误参数将包含在一个错误文档中，结果将是null。如果查询成功，错误参数将为null，结果将为查询结果。
让我们看看如果没有回调函数，会发生什么：

```javascript
// find each person with a last name matching 'Ghost'
var query = Person.findOne({ 'name.last': 'Ghost' });

// selecting the `name` and `occupation` fields
query.select('name occupation');

// execute the query at a later time
query.exec(function (err, person) {
  if (err) return handleError(err);
  console.log('%s %s is a %s.', person.name.first, person.name.last, person.occupation) // Space Ghost is a talk show host.
})
```
查询返回的实例,使我们能够建立我们的查询。继续这个例子：

```javascript
Person
.find({ occupation: /host/ })
.where('name.last').equals('Ghost')
.where('age').gt(17).lt(66)
.where('likes').in(['vaporizing', 'talking'])
.limit(10)
.sort('-occupation')
.select('name occupation')
.exec(callback);
```
Validation
----------
在我们进学习验证语法的细节之前,请记住以下规则:
- 验证是在SchemaType中定义的
- 验证是中间件的一部分
- 验证是在一个文档试图保存时发什么的。
- 验证是异步递归;当你调用模型#保存,执行子文档验证。如果出现错误,您的模型#保存的回调函数接收它。
- 验证支持完全自定义

Built in validators
-------------------
mongoose有几个内置的验证。
- 所有的SchemaTypes 都有内置的验证。
- 数字有最大和最小的验证。
- 字符串有 enum 和math验证。
每个上面链接验证器提供更多的信息关于如何启用它们,以及定制相关的错误消息。

Validation errors
-----------------
```javascript
var toySchema = new Schema({
  color: String,
  name: String
});

var Toy = mongoose.model('Toy', toySchema);

Toy.schema.path('color').validate(function (value) {
  return /blue|green|white|red|orange|periwinkle/i.test(value);
}, 'Invalid color');

var toy = new Toy({ color: 'grease'});

toy.save(function (err) {
  // err is our ValidationError object
  // err.errors.color is a ValidatorError object
  
  console.log(err.errors.color.message) // prints 'Validator "Invalid color" failed for path color with value `grease`'
  console.log(String(err.errors.color)) // prints 'Validator "Invalid color" failed for path color with value `grease`'
  console.log(err.errors.color.type)  // prints "Invalid color"
  console.log(err.errors.color.path)  // prints "color"
  console.log(err.errors.color.value) // prints "grease"
  console.log(err.name) // prints "ValidationError"
  console.log(err.message) // prints "Validation failed"
});
```
验证错误后,文档也会有相同的错误属性:

```javascript
toy.errors.color.message === err.errors.color.message
```



