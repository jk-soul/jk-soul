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
  





          
API Documentation
------

id                      | 默认值                                   | 定义
------------------------|------------------------------------------|----------------------------------------------
aggregateTemplate       |```html <div ng-click="row.toggleExpand()" ng-style="{'left': row.offsetleft}"   class="ngAggregate"><span class="ngAggregateText">{{row.label CUSTOM_FILTERS}} ({{row.totalChildren()}} {{AggItemsLabel}})</span><div class="{{row.aggClass()}}">/div></div>```|  定义一个聚合模板时，自定义行。 
afterSelectionChange    |function (rowItem, event) {}               |当你想在选择之后验证东西时回调。
beforeSelectionChange   |function (rowItem, event) { return true; } |当你想在选择之前验证东西时回调，如果你想取消选择返回false，否则，返回true。如果你需要等待一个异步调用选择，你可以使用 rowitem。返回false后开始继续选择（事件）的方法。注：当SHIFT+网格中选择多个项目只会被调用一次，rowItem将是排队要选择项目的数组。
checkboxCellTemplate    |```html <div class="ngSelectionCell"><input tabindex="-1" class="ngSelectionCheckbox" type="checkbox" ng-checked="row.selected" /></div>```               |复选框单元格模板                         
checkboxHeaderTemplate  |```html <input class="ngSelectionHeader" type="checkbox" ng-show="multiSelect" ng-model="allSelected" ng-change="toggleSelectAll(allSelected)"/>```|复选框头模板
columnDefs              |  undefined                                |定义数组一样定义列，如果没有定义，列是自动生成的。
data                    |  []                                       |显示在网格中的数据，数组中的每一项都被映射到行中被显示出来。
enableCellEdit          |  false| 在全球范围内允许所有单元可编辑。使用 editableCellTemplate选项覆盖默认的文本输入。
enableCellSelection     |  false|  启用或禁用单元导航和选择。
enableColumnResize      |  false|  启用或禁用列的大小调整。
enableColumnReordering  |  false|  启用或禁用列的重新排序。
enableHighlighting      |  false|  启用或禁用单元内容选择。
enablePaging|  false    |  启用服务器端分页功能。
enableRowReordering     |  false|  启用拖放行重新排序，只能在HTML5兼容的浏览器中。
enableRowSelection      |  true |  为了在网格中能够有选择行。
enableSorting           |  true |  启用或禁用网格进行排序。
filterOptions           |  { filterText: '', useExternalFilter: false }|  filterText: 绑定到内置搜索框中的文本。         useExternalFilter:如果你想使用自己的过滤机制但要使用内置的搜索框,通过传递内部的过滤。
footerRowHeight         |  55   |  定义页脚的高度，以像素为单位。
groups                  |  []   |  由初始字段对数组进行分组。字段名的数组，而不是显示的名称。
groupsCollapsedByDefault|  true |  当分组启用时收取条目。
headerRowHeight         |  32   |  以像素为单位的标题行高度。
headerRowTemplate       |  undefined|  定义一个标题行的模板进行进一步自定义。
jqueryUIDraggable       |  false|  允许使用jqueryUI reaggable/投掷插件。如果启用需要jqueryUI工作。如果你想拖+下降，但用户坚持使用蹩脚的浏览器，这是非常有用的。
jqueryUITheme           |false  |允许使用jqueryUIThemes
keepLastSelected        |true   |在ini单一选择模式下防止为选择的项
maintainColumnRatios    |undefined|调整大小时保持列宽，使用*的或不确定的宽度时，默认为true，可以通过设置为false来ovverriden。
multiSelect             |true   |如果只想一次选一个项目，将其设置为false
pagingOptions           |{ pageSizes: [250, 500, 1000], pageSize: 250, totalServerItems: 0, currentPage: 1 }|页面大小：可用的页面大小的列表。页面大小：当前选定的页面尺寸。totalServerItems：服务器上的总项目。当前页：当前页面。
plugins                 |[]     |在ng-grid中功能插件数组中注册
rowHeight               |30     |网格行中的行高
rowTemplate             |```html  <div ng-style="{ 'cursor': row.cursor }" ng-repeat="col in renderedColumns" ng-class="col.colIndex()" class="ngCell {{col.cellClass}}"><div class="ngVerticalBar" ng-style="{height: rowHeight}" ng-class="{ ngVerticalBarVisible: !$last }">&nbsp;</div><div ng-cell></div></div>```|定义一个模板用于自定义输出
selectedItems           |[]     |所有网格中的所选项目，在单一模式下只有一个项目在数组中。
selectWithCheckboxOnly  |false  |当复选框被点击时，通过单击行禁用行选项。
showColumnMenu          |false  |启用菜单选择哪些列被显示和组合。如果两个showColumnMenu和showFilter都是false的菜单按钮将不会显示。
showFilter              |false  |启用列菜单中 filterbox 的显示。如果两个showColumnMenu和showFilter都是假的菜单按钮将不会显示。
showFooter              |false  |显示或隐藏页脚，页脚在默认情况下禁用。
showGroupPanel          |false  |显示悬浮窗进行拖放分组
sortInfo                |{ fields: [], directions: [] }|定义一个sortInfo对象指定一个默认的排序状态。您还可以观察这个变量来利用服务器端排序（见useExternalSorting）。语法是sortInfo：{字段：['fieldName1'，'fieldName2']，方向：['ASC'，'DESC']}。路线不区分大小写
showSelectionCheckbox   |false  |行选择复选框显示为第一列。
tabIndex                |0      |设置视区的选项索引
useExternalSorting      |false  |防止执行内部排序。该sortInfo对象将与分类信息进行更新，因此您可以处理排序（见sortInfo）
virtualizationThreshold |50     |行的门槛，迫使行虚拟化上。
selectRow               |function (rowIndex, state)|附加到供用户编程选择行的specifiec网格选项，忽略相关实体的功能
selectItem              |function (itemIndex, state)|功能:附加特定的表格选项给用户做编程选择，选择基于实体的数据阵列选项的索引中的行的。
selectAll               |function (state)|功能：附加到用户特定的网格选项以编程方式设置选定的值的所有行来传递的状态。
