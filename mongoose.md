
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
