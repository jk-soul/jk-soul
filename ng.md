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

select+ng-repeat
-----------------
```html
<script src="./angular/angular.min.js"></script>
<body ng-app ng-controller="TestCtrl">
<select ng-model="user" ng-options="user.id as user.name  group by user.age for user in arr">
</select>
<div ng-repeat="user in arr">{{  user.name  }}</div>
</body>
</html>
<script>
    var TestCtrl = function ($scope) {
        $scope.arr = [
            {name: 'a', id: 1, age: 21},
            {name: 'b', id: 2, age: 21},
            {name: 'c', id: 3, age: 22}
        ]
    }
</script>
```

排序 order
---------- 
```html
<body>
<div ng-controller="TestCtrl">
    {{arr| orderBy:'age'}}<br/>
    {{arr| orderBy:'-age'}}<br/>
    {{arr| orderBy:'-age' |limitTo:3}}<br/>
    {{arr| orderBy:['age','name']}}<br/>
</div>
</body>
</html>
<script>
    var TestCtrl = function ($scope) {
        $scope.arr = [
            {name: 'A', age: 3},
            {name: 'B', age: 4},
            {name: 'C', age: 1},
            {name: 'D', age: 4}
        ];
    }
</script>
```

HTTP请求
--------
```html
<body ng-controller="PhoneListCtrl">
Search: <input ng-model="query">
Sort by:
<select ng-model="orderProp ">
    <option value="name">Alphabetical</option>
    <option value="age">Newest</option>
</select>
<ul class="phones">
    <li ng-repeat="phone in phones | filter:query | orderBy:orderProp">
        {{phone.name}}
        <p>{{phone.snippet}}{{phone.age}}</p>
    </li>
</ul>
</body>
</html>
<script>
    function PhoneListCtrl($scope, $http, $timeout) {
        $scope.phones = [
            {
                "age": 1,
                "id": "11",
                "name": "a1",
                "snippet": "Are you ready for everything life throws your way?"
            }
        ];
        $http.get('./phones.json').success(function (res) {
            $timeout(function () {
                $scope.phones = res;
            }, 1000 * 15);
        }).error(function (res) {
            console.log(res);
        });
```    

表格数据
--------
```html
<body ng-controller="TestCtrl">
<table>
    <tr>
        <th ng-click="f='name'; rew=!rew">名字</th>
        <th ng-click="f='age';  rew=!rew">年龄</th>
    </tr>
    <tr ng-repeat="o in dataList | orderBy :f:rew">
        <td>{{ o.name }}</td>
        <td>{{ o.age }}</td>
    </tr>
</table>
<button ng-click="page=page-1" ng-disabled="page===1">上一页</button>
<button ng-click="page=page+1">下一页</button>
<input type="number" ng-model="limit"/>
</body>
</html>
<script>
    /**
     * 1.$http请求
     * 2.定义page，dataList
     * 3.watch->page->array.slice(start,end)获取新的dataList
     * 4.ng-click='page=page-1'
     * 5.http请求回调之后，要手动赋值第一页
     *
     * @param $scope
     * @param $http
     * @constructor
     */
    var TestCtrl = function ($scope, $http) {
        $scope.page = 1;
        $scope.dataList = [];
        $scope.limit = 3;
        $http.get('./phones.json').success(function (res) {
            $scope.data = res;

            $scope.$watch('page', function (newVal, oldVal) {
                console.log($scope.page);
                $scope.dataList = $scope.data.slice(($scope.page - 1) * $scope.limit, $scope.page * $scope.limit)
                if (newVal !== oldVal) {

                }
            });
            $scope.$watch('limit',function(newVal,oldVal){
                $scope.dataList = $scope.data.slice(($scope.page - 1) * $scope.limit, $scope.page * $scope.limit)
            })

        });
    };
//    数组push,slice

</script>
```

选项
----
```html
<body ng-controller="TestCtrl">
<form ng-submit="submit()" name="frm" novalidate>
    <p>Title:<input type="text"  name='title' ng-model="question.title" required/></p>
    <span ng-show='frm.title.$error.required'>不能为空</span>{{frm.title.$error}}
    <div ng-repeat="selection in question.selections">
        内容： <input type="text" ng-model="selection.content" required/>
        <span ng-show="(selection.content==null||selection.content=='')">不能为空</span>
        正确： <input type="checkbox" ng-model="selection.correct"/>{{$index}}
        <button type="button" ng-click="question.selections.splice($index,1)">删除</button>
    </div>
    <button type="button" ng-click="question.selections.push({content:null,correct:false})">新增</button>
    <button type="submit">提交</button>
</form>

</body>
</html>
<script>
    //表单的input $error ng-show
    /**
     * 1.所有的表单控件（input、select、textarea）都要在表单里面（form）
     * 2.表单中的button需要显示的指定type，若不指定就会在click触发submit事件。
     * 3.从表单中收集数据，ng-model=question.title。
     * 收集的数据中包含数组，即有多组input。
     * @param $scope
     * @constructor
     */
    var TestCtrl = function ($scope) {
        $scope.question = {
            title: null,
            selections: [
                {
                    content: null,
                    correct: false
                }
            ]
        };
        $scope.submit = function () {
            console.log($scope.question);
        };
//        $scope.f=function(){
//            if(selection.content==null) return true;
//            return false
//        };
    }
</script>
```
