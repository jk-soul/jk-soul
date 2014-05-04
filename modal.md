modal
=====
app.js
------- 
   $stateProvider
                    .state('route1',{
                        url:'/route1',
                        templateUrl:'views/exercise.html',
                        controller:'TestCtrl'
                    })
                    .state('route2',{
                        url:'/route2',
                        templateUrl:'views/ex2.html',
                        controller:'exCtrl'
                    })
                    .state('route3',{
                        url:'/route3',
                        templateUrl:'views/modal.html',
                        controller:'ModalDemoCtrl'
                    })
                  
                  
                  
modal.html
----------
<div>
    <script type="text/ng-template" id="123">
        <div class="modal-header">
            <h3>I'm a modal!</h3>
        </div>
        <div class="modal-body">
            <ul>
                <li ng-repeat="item in items">
                    <a ng-click="selected.item = item">{{ item }}</a>
                </li>
            </ul>
            Selected: <b>{{ selected.item }}</b>
        </div>
        <div class="modal-footer">
            <button class="btn btn-primary" ng-click="ok()">OK</button>
            <button class="btn btn-warning" ng-click="cancel()">Cancel</button>
        </div>
    </script>
    <button class="btn" ng-click="open()">Open me!</button>nihao''{{items}}{{ selected }}
</div>

myModalContent
--------------

    <div class="modal-header">
        <h3>I'm a modal!</h3>
    </div>
    <div class="modal-body">
        <ul>
            <li ng-repeat="item in items">
                <a ng-click="selected.item = item">{{ item }}</a>
            </li>
        </ul>
        Selected: <b>{{ selected.item }}</b>
    </div>
    <div class="modal-footer">
        <button class="btn btn-primary" ng-click="ok()">OK</button>
        <button class="btn btn-warning" ng-click="cancel()">Cancel</button>
    </div>


ex2.html
--------
<button ng-click="ex()">用户列表</button>

<div class="gridStyle" ng-grid="gridOptions">你好</div>
        <div ng-controller="mtCtrl">
    <button  ng-click="mt()">新增物料列表</button>
        </div>


exCtrl.js
---------
/**
 * Created by Lannister on 2014/4/28.
 */
(function () {
    "use strict";
    angular.module('scm')
        .factory('User', function ($resource) {
            return $resource('/scm/api/sys/User')
        })
        .controller('exCtrl', function (User, $scope) {
            //TODO:自定义列:
            //title->标题
            //隐藏__v,password,_id,_v,quickLaunch,lock,
            //status->cellTemplate->checkbox
            //createAt,lastModifiedDate->时间过滤器

            $scope.gridOptions = { data: 'myData',
                    columnDefs: [
                        {field:'title', displayName:'姓名'},
                            {field:'email', displayName:'邮箱'},
                            {field:'status', displayName:'status', cellTemplate: "<div><input type='checkbox' ng-model='row.entity.status' disabled='disabled' /></div>"},
//                            {cellTemplate:'<div  ng-controller="ModalDemoCtrl"><button ng-click="open()">点我</button></div>'},
                            {field:'lastModifiedDate', displayName:'创建时间',cellFilter:'date:"yyyy-MM-dd  HH:mm:ss"'},
                            {field:'createAt', displayName:'修改时间',cellFilter:'date:"yyyy-MM-dd  HH:mm:ss"'}
                    ]
            };
            User.query({
                filter: {},
                limit: 30,
                age: 1,
                sort: {lastModifiedDate: 'desc'}
            }).$promise.then(function (res) {
                    $scope.myData = res;
                });
        })

//    angular.module('scm')
//        .factory('User', function ($resource) {
//            return $resource('/scm/api/sys/User');
//        })
//        .controller('exCtrl', function ($scope, User) {
//            $scope.gridOptions = { data: 'myData' };
//            var user = User.query({
//                filter: {},
//                limit: 30,
//                age: 1,
//                sort: {lastModifiedDate: 'desc'}
//            }, function () {
//                console.log(User);
//                $scope.myData = user;
//
//            });
//        });
})();

ModalDemoCtrl.js
/**
 * Created by Lannister on 2014/4/30.
 */
(function () {
    "use strict";
    angular.module('scm')
        .controller('ModalDemoCtrl', ['$scope', '$modal', '$log',
            function ($scope, $modal, $log) {
                $scope.items = ['item1', 'item2', 'item3'];
                console.log(1);
                $scope.open = function () {
                    var modalInstance = $modal.open({
                        templateUrl: 'views/myModalContent.html',
                        controller: 'ModalInstanceCtrl',
                        resolve: {
                            items: function () {
                                return $scope.items;
                            }
                        }
                    });
                    console.log(2);
                    modalInstance.opened.then(function () {//模态窗口打开之后执行的函数
                        console.log('modal is opened');
                    });
                    modalInstance.result.then(function (result) {
                        $scope.selected=result;
                        console.log(result.item);
                    }, function (reason) {
                        console.log(reason);//点击空白区域，总会输出backdrop click，点击取消，则会暑促cancel
                        $log.info('Modal dismissed at: ' + new Date());
                    });
                };
            }])
        .controller('ModalInstanceCtrl', ['$scope', '$modalInstance', 'items',
            function ($scope, $modalInstance, items) {
                $scope.items = items;
                $scope.selected = {
                    item: $scope.items[0]
                };
                console.log(items);
                console.log($scope.selected);
                $scope.ok = function () {
                    console.log($scope.selected);
                    $modalInstance.close($scope.selected);
                };
                $scope.cancel = function () {
                    $modalInstance.dismiss('cancel');
                };
            }]);
//        .controller('TestCtrl', ['$scope',
//            function ($scope) {
//
//            }])
})();
