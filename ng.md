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



```html
<body ng-app>

<script src="../bower_components/angular/angular.min.js"></script>
<input type="text" id="test" ng-controller="TestCtrl" ng-model="a"/>
<p>{{a}}</p>
<script>
    var TestCtrl=function($scope){
        $scope.a='123';
    }
</script>

</body>
```
输入框中改变a值。

双向绑定
--------
```html
<body ng-app>
<script src="../bower_components/angular/angular.min.js"></script>
<div ng-controller="TestCtrl">
    <div style="width: 100px; height: 100px;background:blue"></div>
    <input type="text" name="width" ng-model="width"/>
</div>
<script>
    var TestCtrl=function($scope,$element){
        $scope.width=100;
        $scope.$watch('width',
            function(to,from){
                $element.children(':frist').width(to)
            }
        );
    }
</script>
</body>

未实现




<body ng-app>
<script src="../bower_components/angular/angular.min.js"></script>
<div ng-controller="TestCtrl">
    <div style="width: 100px; height: 100px;background:blue " ng-style="style"></div>
    <input type="text" name="width" ng-model="style.width"/>
</div>
<script>
    var TestCtrl=function($scope,$element){
        $scope.style={width:100+'px'};
    }
</script>
</body>




<body ng-app>
<script src="../bower_components/angular/angular.min.js"></script>
<div ng-controller="TsetCtrl">
    <div style="width: 100px; height: 100px;background:blue " ng-click="click()"></div>
</div>
<div>
    <p>{{w}}x{{h}}</p>
    <p>W:<input type="text" ng-model="w" /></p>
    <p>H:<input type="text" ng-model="h" /></p>
</div>

<script>
    var TestCtrl = function ($scope) {
        $scope
    }
</script>
</body>
```


重复ng-repeat
```html
<body ng-app>
<script src="../bower_components/angular/angular.min.js"></script>
<div ng-controller="TestCtrl">
    <ul ng-repeat="member in obj_list">
        <li>{{ member }}</li>
    </ul>
</div>
<script>
    var TestCtrl = function($scope){
        $scope.obj_list = [1,2,3,4];
    }
</script>
</body>
```

load 1 2 交换
-------------
```html
<body ng-controller="TestCtrl">
<script type="text/ng-template" id="tpl1">
    <div>{{ 1 }}</div>
</script>
<script type="text/ng-template" id="tpl2">
    <div>{{ 2}}</div>
</script>


<a ng-click="check()">load</a>

<div ng-include src="v">
</div>
</body>
</html>
<script>
    var TestCtrl = function ($scope) {
        $scope.check = function () {
            if (!$scope.v) {
                $scope.v = 'tpl1';
            }
            else if ($scope.v == 'tpl1') {
                $scope.v = 'tpl2';
            }
            else {
                $scope.v = 'tpl1';
            }
        }
    }
</script>





switch
------
<body ng-controller="TestCtrl">
<a ng-click="check()">load</a>

<div ng-init="v">
    <div ng-switch on="v">
        <div ng-switch-when="1">2</div>
        <div ng-switch-when="2">1</div>
    </div>
</div>
</body>
</html>
<script>
    var TestCtrl = function ($scope) {
        $scope.check = function () {
            if (!$scope.v) {
                $scope.v = 2;
            }
            else if ($scope.v == 1) {
                $scope.v = 2;
            }
            else {
                $scope.v = 1;
            }
        }
    }
</script>
```


表单控件
--------
```html
<body>
<form name="t_f" ng-controller="TestCtrl">
    <input type="text" name="a" required ng-model="a"/>
    <input type="text" name="b" required ng-model="b" ng-minlength="2"/>
    <span ng-click="see()">{{ t_f.$error }},{{ t_f.b.$error }}</span>

</form>
</body>


</html>
<script>
var TestCtrl=function($scope){
    $scope.see=function(){
        console.log($scope.t_f.$error);
        console.log($scope.t_f.a)
    }
}
</script>
```


input 匹配
----------
```html
<body>
<form name="test_form" ng-controller="TestCtrl">
    <input type="text" name="a" ng-model="a" required ng-pattern="/abc/" />
    <span ng-click="see()">{{ test_form.$error }}</span>
</form>
</body>
</html>
<script>
var TestCtrl=function($scope){
    $scope.see=function(){
        console.log($scope.t_f.$error);
        console.log($scope.t_f.a)
    }
}
</script>
```


select
------
x.v as x.name for x in o
------------------------
```html
<body>
<form name="test_form" ng-controller="TestCtrl" ng-init="o=[0,1,2,3]; a=o[1];">
    <select ng-model="a" ng-options="x for x in o" ng-change="show()">
        <option value="">可以加这个空值</option>
    </select>
</form>
</body>
</html>
<script>
    var TestCtrl = function($scope){
        $scope.show = function(){
            console.log($scope.a);
        }
    }
</script>




 x.name group by x.g for x in o
 ------------------------------
<body>
<form name="test_form" ng-controller="TestCtrl"
      ng-init="o=[{name: 'AA'}, {name: 'BB'}]; a=o[1];">
    <select ng-model="a" ng-options="x.name for x in o" ng-change="show()">
    </select>
</form>
</body>
</html>
<script>
    var TestCtrl = function($scope){
        $scope.show = function(){
            console.log($scope.a);
        }
    }
</script>


x.v as x.name group by x.g for x in o 
-------------------------------------
<form name="test_form" ng-controller="TestCtrl"
      ng-init="o=[{name: 'AA', v: '00'}, {name: 'BB', v: '11'}]; a=o[1].v;">
    <select ng-model="a" ng-options="x.v as x.name for x in o" ng-change="show()">
    </select>
</form>
</body>
</html>
<script>
    var TestCtrl = function($scope){
        $scope.show = function(){
            console.log($scope.a);
        }
    }
</script>


x.v as x.name group by x.g for x in o 
-------------------------------------
<body>
<form name="test_form" ng-controller="TestCtrl"
      ng-init="o=[{name: 'AA', g: '00'}, {name: 'BB', g: '11'}, {name: 'CC', g: '00'}]; a=o[1];">
    <select ng-model="a" ng-options="x.name group by x.g for x in o" ng-change="show()">
    </select>
</form>
</body>
</html>
<script>
    var TestCtrl = function($scope){
        $scope.show = function(){
            console.log($scope.a);
        }
    }
</script>


键值
-----
<body>
<form name="test_form" ng-controller="TestCtrl" ng-init="o={a: 0, b: 1}; a=o.a;">
    <select ng-model="a" ng-options="k for (k, v) in o" ng-change="show()">
    </select>
</form>

<form name="test_form" ng-controller="TestCtrl"
      ng-init="o={a: {name: 'AA', id: '00'}, b: {name: 'BB', id: '11'}}; a=o.a.id;">
    <select ng-model="a" ng-options="v.id as v.name for (k, v) in o" ng-change="show()">
    </select>
</form>

<form name="test_form" ng-controller="TestCtrl"
      ng-init="o={a: {name: 'AA', v: '00', g: '=='}, b: {name: 'BB', v: '11', g: '=='}}; a=o.a;">
    <select ng-model="a" ng-options="v.name group by v.g for (k, v) in o" ng-change="show()">
    </select>
</form>

<form name="test_form" ng-controller="TestCtrl"
      ng-init="o={a: {name: 'AA', v: '00', g: '=='}, b: {name: 'BB', v: '11', g: '=='}}; a=o.a.v;">
    <select ng-model="a" ng-options="v.v as v.name group by v.g for (k, v) in o" ng-change="show()">
    </select>
</form>
</body>
</html>
<script>
    var TestCtrl = function($scope){
        $scope.show = function(){
            console.log($scope.a);
        }
    }
</script>
```
