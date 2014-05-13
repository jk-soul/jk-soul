Concept
========

工作流模板
===========
固定规则，工作流实例根据规则运行。
请假审批流程，申请人递交申请后，部门主管审批-->人事部经理审批-->总经理审批。

工作流关联列表
---------------

流程触发条件(触发器)
-----------
* afterInsert 新增item之后，举个例子：请假申请之后流程自动启动
* afterUpdate 在更新之后，举个例子：某技术文档版本的发布需要走个流程。
* afterRemove 
* afterSave =afterInsert+ afterUpdate

工作流关联数据
---------------


工作流实例
================
例：请假审批流程，申请人递交申请后，afterInsert，流程启动创建一个工作流实例，该实例根据工作流模板规则运行。
* 流程启动者->请假的人
* 流程状态->开始，进行中，已完成...
* 流程启动项->申请人填的表单

流程启动项
-----------
就是item，包含流程启动时所需的信息。
例：采购计划提供了需要采购的物料，采购的计划数量、到货时间等基础数据。

工作流任务
-------------

描述

* /public/views/workflowTemplate放置的是各个工作流的任务表单
* /public/views/task放置的是常用的任务审批表单
控制器的路径为/src/workflow，常用的任务的控制器写在TaskCtrl中；流程特有的任务（比如询价审批任务的从询价员收集询价信息）的控制器写在对应的文件里面
* 询价计划审批流程中，需要从任务的表单中收集询价员的询价信息，完成表单和控制器

```javascript
.controller('PurchasePlanInquiryTaskCtrl', ['$scope', 'Task', '$stateParams', '$injector',
            function ($scope, Task, $stateParams, $injector) {
                Task.get({_id: $stateParams.taskId}).$promise
                    .then(function (res) {
                        $scope.task = res;
                        if (!$scope.task.result)$scope.task.result = {};
                        var service = $injector.get($scope.task.instance.initialItem.model);
                        return service.get({_id: $scope.task.instance.initialItem._id}).$promise;
                        /*                        $scope.task.result.inquiryLog = [
                         {
                         provider: '536afb6e99d07ae42592bb4b',
                         contact: '小敏aaa',
                         telephone: '小敏的电话',
                         unit: 2222,
                         freight: true,
                         payWay: '货到付款',
                         remark: '小敏的备注'
                         }
                         ];*/
//                        $scope.submit();
                    })
                    .then(function (res) {
                        $scope.doc = res;
                        console.log($scope.doc);
                    })
```
