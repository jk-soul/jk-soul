Angular-ui-router
=================
AngularUI Router
----------------
AngularUI Router是AngularJS的一个路由框架，他能将你的部分接口组织成状态机。不同于Angular的核心服务$route 围绕着Url路由， UI-Router 围绕着状态的 ，其中可能包含路由。as well as other behavior, attached。
状态中加入名字，嵌套平行视图能够让你有力的管理你的用户界面。

入门
----
获取UI-Router的四种方法。
1.克隆或建立库
2.


当你完成后，你的设置应类似于以下步骤.
```html
 <script src="//ajax.googleapis.com/ajax/libs/angularjs/1.1.5/angular.min.js"></script>
    <script src="js/angular-ui-router.min.js"></script>
```

嵌套的状态与视图
----------------
UI-Router的大部分能力都是在其嵌套状态和视图中体现的。
1.首先按之前说的安装。
2.然后在你的app中的body内加入ui-view指示器。
3.你也注意到我们在一些地址中加入了ui-serf的指示器。



angular-ui/ui-router
--------------------
状态管理器
----------
这个全新的$stateProvider和angular的route很相似，但是它是纯粹地注重于状态。
1.状态对应于整体UI和导航中的应用程序中的某个地方。
2.状态（通过控制器、模板、视图）描述了用户界面在看起来的样子以及做的事情。
3状态经常有东西是相似的，在模型中分解出这些共同点的主要方式是通过状态的层次。比如，父/子状态又名嵌套状态。

状态的最简单形式
----------------

模板在哪里插入？
当一个状态被激活，它的模板会自动插入到父类状态的模板的ui-view位置。如果它是第一层状态，因为其没有父状态，那它的父类模板就是当前页面。

激活一个状态
------------
主要有三种方法
1.调用 $state.go(). High-level convenience method
2.单击包含ui-sref的链接。
3.导航到与状态相关的Url。

模板
-----
有几种方法可以配置状态的模板。
最简单的就是通过template配置属性，

```javascript
$stateProvider.state('contacts', {
  template: '<h1>My Contacts</h1>'
})
```
除了写template你还可以载入链接（大部分时候使用的方法）

```javascript
$stateProvider.state('contacts', {
  templateUrl: 'contacts.html'
})
```
templateUrl也可以成为一个返回url的函数，这需要预设一个参数stateParams，它并不是注入。

```javascript
$stateProvider.state('contacts', {
  templateUrl: function (stateParams){
    return '/partials/contacts.' + stateParams.filterBy + '.html';
  }
})
```

或者你也可以使用一个模板提供一个函数，其能够注入，能够访问当地，并且必须返回一个html模板。比如：

```javascript
$stateProvider.state('contacts', {
  templateProvider: function ($timeout, $stateParams) {
    return $timeout(function () {
      return '<h1>' + $stateParams.contactId + '</h1>'
    }, 100);
  }
})
```

如果你想你的ui-view在激活之前有一些默认的值，你可以像下面这样做。其内容会在其状态激活后立刻被替换并且用模板填充到ui-view。

```javascript
<body>
    <ui-view>
        <i>Some content will load here!</i>
    </ui-view>
</body>
```

控制器
------

你可以为你的模板分配一个控制器。警告：如果模板未定义那么控制器也不存在。
你可以这样设置你的控制器：

```javascript
$stateProvider.state('contacts', {
  template: '<h1>{{title}}</h1>',
  controller: function($scope){
    $scope.title = 'My Contacts';
  }
})
```

或者你已经有一个控制器了，你可以这样定义你的模板:

```javascript
$stateProvider.state('contacts', {
  template: ...,
  controller: 'ContactsCtrl'
})
```

如果愿意也可以使用controllerAs语法：
```javascript
$stateProvider.state('contacts', {
  template: ...,
  controller: 'ContactsCtrl as contact'
})
```
对于更高级的要求，你也可以使用 controllerProvider动态返回一个函数或者控制器。
```javascript
$stateProvider.state('contacts', {
  template: ...,
  controllerProvider: function($stateParams) {
      var ctrlName = $stateParams.type + "Controller";
      return ctrlName;
  }
})
```
控制器可以使用$scope.on方法来监听状态转换触发的事件。控制器实例化需要一个基本要求就是当其建立起相应的范围时。当用户通过URL手动导航到一个状态，$ stateProvider将加载正确的模板到视图，则控制器绑定到模板的范围。

Resolve
-------
你可以用resolve为你的控制器提供内容和数据，那就是自定义的状态
resolve是一个可选的会被注入到控制的依赖关系。
如果任何依赖关系被promise，它们将被解析并转换为一个数值，控制器被实例化之前和$ routeChangeSuccess事件被触发。



控制器被实例化之前，在resolve之下的每个对象都会被解决（如果是一个promis，则通过deferred.resolve()），注意每个resolve对象被当做参数注入到控制器中。

附加自定义数据到状态对象
-----------------------
```javascript
// Example shows an object-based state and a string-based state
var contacts = { 
    name: 'contacts',
    templateUrl: 'contacts.html',
    data: {
        customData1: 5,
        customData2: "blue"
    }  
}
$stateProvider
  .state(contacts)
  .state('contacts.list', {
    templateUrl: 'contacts.list.html',
    data: {
        customData1: 44,
        customData2: "red"
    } 
  })

在上面例子中你可以这样访问数据
function Ctrl($state){
    console.log($state.current.data.customData1) // outputs 5;
    console.log($state.current.data.customData2) // outputs "blue";
}
```

onEnter 和 onExit 回调
----------------------
当一个状态分别为有效和无效是将会有onEnter和onExit回调。
```javascript
$stateProvider.state("contacts", {
  template: '<h1>{{title}}</h1>',
  resolve: { title: 'My Contacts' },
  controller: function($scope, title){
    $scope.title = 'My Contacts';
  },
  onEnter: function(title){
    if(title){ ... do something ... }
  },
  onExit: function(title){
    if(title){ ... do something ... }
  }
})
```

状态改变事件
------------
所有的事件都是在$scoperoot层被激活。
1.$stateChangeStart 
```javascript
$rootScope.$on('$stateChangeStart', 
function(event, toState, toParams, fromState, fromParams){ ... })

注意：用 event.preventDefault()组织过渡发生。
$rootScope.$on('$stateChangeStart', 
function(event, toState, toParams, fromState, fromParams){ 
    event.preventDefault(); 
    // transitionTo() promise will be rejected with 
    // a 'transition prevented' error
})
```

2.$stateNotFound 
如果被请求的状态不能被找到，那么在过渡期间使用提供的状态。
```html
// somewhere, assume lazy.state has not been defined
$state.go("lazy.state", {a:1, b:2}, {inherit:false});

// somewhere else
$scope.$on('$stateNotFound', 
function(event, unfoundState, fromState, fromParams){ 
    console.log(unfoundState.to); // "lazy.state"
    console.log(unfoundState.toParams); // {a:1, b:2}
    console.log(unfoundState.options); // {inherit:false} + default options
})
```
3.$stateChangeSuccess
4.$stateChangeError

View Load Events
-----------------
1.$viewContentLoading
```html
$scope.$on('$viewContentLoading', 
function(event, viewConfig){ 
    // Access to all the view config properties.
    // and one special property 'targetView'
    // viewConfig.targetView 
});

2.$viewContentLoaded
$scope.$on('$viewContentLoaded', 
function(event){ ... });
```


嵌套状态的方法
--------------
状态可以相互嵌套
1.用dot notation，例.    .state('contacts.list', {})
2.用 ui-router.stateHelper建立一个状态树。
3.用带字符串名字的父类属性parent。
4.用带有父类对象的父类属性。

点符号
------
你可以通过点语法推断出层次结构
```javascript
$stateProvider
  .state('contacts', {})
  .state('contacts.list', {});
```

stateHelper模块
---------------
这是一个第三方模块，所以你必须把他引入ui-route。
```html
angular.module('myApp', ['ui.router', 'ui.router.stateHelper'])
  .config(function(stateHelperProvider){
    stateHelperProvider.setNestedState({
      name: 'root',
      templateUrl: 'root.html',
      children: [
        {
          name: 'contacts',
          templateUrl: 'contacts.html',
          children: [
            {
              name: 'list',
              templateUrl: 'contacts.list.html'
            }
          ]
        },
        {
          name: 'products',
          templateUrl: 'products.html',
          children: [
            {
              name: 'list',
              templateUrl: 'products.list.html'
            }
          ]
        }
      ]
    });
  });
```

父类属性使用状态名
------------------
```javascript
$stateProvider
  .state('contacts', {})
  .state('list', {
    parent: 'contacts'
  });
```
  
基于对象的状态
--------------
如果你不喜欢使用基于字符串的状态，您还可以使用基于对象的状态。name被放入对象同时parent属性被设置在所有子状态上。
```javascript
var contacts = { 
    name: 'contacts',  //mandatory
    templateUrl: 'contacts.html'
}
var contactsList = { 
    name: 'contacts.list', //mandatory. This counter-intuitive requirement addressed in issue #368
    parent: contacts,  //mandatory
    templateUrl: 'contacts.list.html'
}

$stateProvider
  .state(contacts)
  .state(contactsList)
```
您可以在使用其他方法和属性比较时，通常直接引用该对象：
```javascript
$state.transitionTo(states.contacts);
$state.current === states.contacts;
$state.includes(states.contacts)
```

嵌套的状态与视图
----------------
当一个应用程序处于特定的状态时，若它的状态为激活的，它所有的祖先状态也处于隐式状态，若"contacts.list为激活状态，那么contactst状态也是隐式的，因为它是contacts.list的父类状态。
子类状态会将模板加载到其父类的ui—view。
