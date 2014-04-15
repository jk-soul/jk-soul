jk-soul
=======

这是我的第一个blog
-----------------
不知道该怎写
这样子？
------
还是这样子？
=======
了然
 <div class="sample_footer">
        &copy; 2004 Foo Corporation
    </div>
好酷！
----
这是一个 [an example](http://example.com/ "Title") 行内链接。

[这个链接](http://example.net/) 没有title属性。     
又懂了！不对啊 没换行  啥是title   换行成功。
如果想进一步了解我，请参阅我的 [关于我](/about/) 页。
这是一个引用型链接 [示例][id]。
这是一个引用型链接 [示例] [id]。
[id]: http://example.com/  "Optional Title Here"

问1
```javascript
 var arr = function () {
        var s = "text";
        var S = new String(s);
        S.len = 3;
    }
    console.log(S.len);
    };
    arr();
```    
问2
```javascript
    var arr = function () {
        var a = [1, 2, 3];
        var b = [];
        for (var i = 0, i < a.length, i++ )
        {
            b[i] = a[i];
        }
    }
    console.log(a===b);
    }
    ;
    arr();
    
 全局变量
 1、
----
  sc=11
    var arr=function(){
        sc=12;
        sg=13;
        return[sc,sg]
       }
    arr()
    console.log(sg);
 
 2、
----
 sc=11
    var arr=function(){
       var sc=12;
       var ari = function(){
        var sc=13;
           return sc;
       }
        console.log(ari());
        return sc
       }
       console.log(arr());
    console.log(sc);
    
    
 作用域、
  sc=11
    var arr=function(){
      console.log(sc)
        var ari =function(){
            var sc =12;
            console.log(sc);
        }
        ari();
        }
     arr();
     
     
赋值运算    
    var i = 0;
    var data = [];
    data[0]=1;
    console.log(data);
    console.log(data[i++]);
    console.log(i);
    data[i++] *= 2;
    console.log(data);
    
    
    data[i++]= data[i++] * 2;
    
    
   var geval=eval;
var x= "gift",y="gift"
    function f(){
    var x ="tt";
eval("x+='change';");
        return x;
    }
 function g(){
     var y="tt";
     eval("y+='change';");
     return y;
 }
    console.log(f(),x);
    console.log(g(),y);  

问3 
    function arr(x){
        x>0?x:-x ;
        return x;
        console.log(x);
    }
arr(5);


问4
   value=1
    var arr =function (){
        (typeof value == "string"?"'"+value+"'":value)
    }
arr();

问5
    var n;
    var arr = function (n) {
        switch (n) {
            case 1:
                return 2;
                break;
            case 2:
                return 4;
                break;
            case 3:
                return 6;
                break;
            default:
                return'hah';
                break;
        }
        ;
    };
        console.log(arr(8),n)  //n为为定义
        
        
        
        
switch
------
   var n;
    var bian = function (n) {
        switch (typeof n) {
            case 'number':
                return n.toString(16)
                break;
            case 'string':
                return '"' + n + '"'
                break;
            default :
                return String(n)
        }
    }
    console.log(bian(17))
    
do/while
--------
  var a=[1,2,3]
 function find(){
     var len= a.length,i=0
     if(len==0){
         console.log('kongde')
     }
     else{
         do {console.log(a[i])
         }while(++i<len)
     }
 }
    find()
    
    
FOR
----
   var o={next: 'nihao'};
    function ati(o) {
        for (; o.next; o = o.next)/*empty*/;
        console.log(o)
    }
 ati(o)
 
 关联数组
 --------
   var avi={p1:1,p2:2,p3:3}
var arr=function(){
    var add=""
    for(var i= 1;i<4;i++){
    add+=avi["p"+i]+'\n';}
    console.log(add)
}
    arr()
    
 索引
--------
var a=[1,2,3]
 for(p in a)
    console.log(p)
    
问1
----
   var a = {x: 1, y: 2, z: 3}
    var b = {x: 2};
    function arr(a,b) {
        for (p in a) {
            if (b.hasOwnProperty[p])continue;
            b[p] = a[p];
        }
        console.log(b)
    }
    ;
    arr(a,b);
    
    
实例
----
  var defaultSetting = {'china': 1, 'jiangsu': 1, 'suzhou': 1,'pingjiang':1,'xiandaihuanyuan':71};
    var userSetting = {'xiandaihuayuan':72};
    function arr(defaultSetting, userSetting) {
        for (key in defaultSetting) {
            console.log(key);
            if (userSetting[key])continue;
            userSetting[key] = defaultSetting[key];
        }
        console.log(userSetting)
    }
    ;
    arr(defaultSetting, userSetting);
