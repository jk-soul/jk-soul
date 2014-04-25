ng-grid
=======
入门
----
1.添加引用到jQuery 和 AngularJS中。
2.添加文件到ng-grid的js文件和css文件中。
3.在你声明模块的地方加入ngGrid
  angular.module('myApp',['ngGrid']);
4.在html文件中，你准备使用ngGrid的的控制器内加入：
  <div class="gridStyle" ng-grid="gridOptions"></div>
gridOptions是我们将要绑定到初始化网格选项的变量。
5.我们将在grid中定义一个基本的风格。
 .gridStyle {
     border: 1px solid rgb(212,212,212);
     width: 400px; 
     height: 300px
}
6.在js文件中我们将要使用到ngGrid的控制器中，加入数据，那么grid会用到：
  $scope.myData = [{name: "Moroni", age: 50},
                 {name: "Tiancum", age: 43},
                 {name: "Jacob", age: 27},
                 {name: "Nephi", age: 29},
                 {name: "Enos", age: 34}]
7.现在在相同的网格控制下初始化网格选项。
  $scope.gridOptions = { data: 'myData' }; 
