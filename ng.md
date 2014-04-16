NG
===
控制器
------

```html
<body ng-app>
<script src="../bower_components/angular/angular.min.js"></script>
<script src="./heh.js" ></script>
<div ng-controller="TestCtrl">
    {{user.x}}
</div>
</body>
```

浏览器
------
通过http请求从web服务器获取html文档，根据HTML标签渲染（rander），然后加载页面中script标签中js文件，执行js文件。

Angular
-------
在dom（document）ready之后检查页面标签是否有ng-app，如果包含只对ng-app内部标签进行解析。


控制器
------
$scope 控制器与页面之间的桥梁，{{}}访问$scope中的模型。

 
```javascript 
angular.module('MyApp', [], function(){console.log('here')});
```
模块名字，数组（数组内容：当前模块继承的模块），模块启动时触发的函数。

ng 是Angular的核心模块。


依赖注入
-------
为何注入$scope？
$scope作用是向视图提供模型，需求是向页面提供数据服务，因此依赖于$scope服务。
如果现在控制器中需要$http服务，则依赖注入$http服务。