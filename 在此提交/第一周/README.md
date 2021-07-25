# HTML
## 语法
* HTML是一种超文本标记语言，文件后缀名是.html或htm
* 标签分为两类
    1.围堵标签<head></head>
    2.自闭和标签<br/>
* 标签可以嵌套
* 在开始标签可以定义属性，属性值需要用引号引起来，单双引号都行
* 不区分大小写，建议小写
## 标签学习
### 文件标签
* html：html文件的根标签
* head：头标签，用于定义文件的一些属性，引入外部资源
* title：标题标签
* body：体标签
* <!DOCTYPE html>定义这个文档是html文件
### 文本标签

* ```<h1>-<h6>```
* ```<p>```
* ```<br/>:换行```
* ```<hr>:水平线```
* ```<b>:粗体```
* ```<i>:斜体```
### 图片标签
* ```<img src = ''/>```
* src:指定图片的位置
* 相对路径：
    ./表示当前目录
    ../表示上一级目录
### 列表标签
* 有序列表
```
<ol>
<li>列表项</li>
<li>列表项</li>
</ol>
```
* 无序列表
```
<ul>
<li>列表项</li>
<li>列表项</li>
</ul>
```
### 超链接标签
* ```<a href = ''></a>```
* href:指定打开资源的url
* target：指定打开资源的方式
* 可以与img标签一起使用
### div和span
### 语义化标签
为了增加程序的可读性
* ```<header>```
* ```<footer>```
### 表格标签
* ```<table></table>表格标签```
* ```<tr></tr>行标签```
* ```<th></th>表头标签```
* ```<td></td>单元格标签```
* ```<thead></thead>```
* ```<tbody></tbody>```
* ```<tfoot></tfoot>```
* 合并单元格：colspan,rowspan
### 表单标签
用于采集数据，与服务器进行数据交互
* <form action = '' method = ''></form>用于定义表单数据采集范围
    action:指定提交数据的url
    method:指定提交数据的方式
        一共7种：
            get：请求参数会在地址栏种显示
            post：不会显示
```
<form action="#" method="get">
    用户名：<input name ='username'>
    <br>
    密码：<input name = 'password'>
    <input type = 'submit' value="登录">
</form>
```
* 表单数据如果需要提交，必须要定义name属性
* input：通过type属性的值可以有不同样式
    * type 
        * text
        * password：密码输入框
        * radio：单选框
            * 单选框name需要一致
            * 要设置value值
            * checked设置默认值
            * placeholder可以设置提示文字
        * checkbox：复选框
        * file
        * date
        * datelocal
        * email
        * number

* select：下拉列表
    * option
* textarea:文本域

# CSS：页面美化和布局控制
## 内联样式
```
<div style="color: red">Hello</div>
```
## 内部样式
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>呵呵</title>
    <style>
        div{
            color: red;
        }
    </style>
</head>
<body>
<div>Hello</div>

</body>
</html>
```
## 外部样式
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>呵呵</title>
    <link rel="stylesheet" href="css/a.css">
</head>
<body>
<div>Hello</div>
</body>
</html>
<!-- a.css文件定义 -->
div{
    color: red;
}
```
## CSS语法
```
选择器{
    属性1：值1;
    ...
}
```
### 基础选择器
* id选择器
    * 语法：id{}
* 元素选择器
    * 语法：标签{}
* 类选择器
    * 语法：.class{}
    * class是标签的一个属性
# JavaScript：客户端脚本语言
可以增强用户和html页面的交互过程
## ECMAScript
### 与html的结合方式
```
<script src="css/j.js" charset="GBK"></script>
//-------------
<script>
        alert("Hello World");
    </script>
```
### 数据类型
* 原始数据类型
    * number:数字
    * String：字符串
    * boolean
    * null：空的占位符
    * undefined：未定义的变量会被赋值为undefined
* 引用类型

### 变量
JavaScript是一种弱类型的语言
语法：var 变量名 = 值；
* 变量的定义var关键字也可以不用
* 用了var定义的是局部变量
* 不用var关键字定义的变量是全局变量

### 运算符
* 一元运算符
    * ++
    * --
* 算数运算符
* 赋值运算符
* 比较运算符
    * === 全等于
* 逻辑运算符
* 三元运算符

## 对象
### Function
* 创建方法：
    1.
    ```
    function fun(a,b)
        {
            document.write(a+b);
        }
    ```
    2.
    ```
    var fun = function(a,b)
    {
         document.write(a+b);
    }
    ```
* length为形参个数
* arguments是方法的内置对象，封装了所有实际参数，相当于一个数组
```
<script>
        function fun()
        {
            var sum =0;
            for (var i = 0; i < arguments.length; i++) {
                sum+=arguments[i];
            }

            return sum;
        }
        var sum  =fun(1,2,3);
        document.write(sum);
    </script>
```
### Array数组对象
* 创建方式：
```
    1.var arr = new Array(元素列表);
    2.var arr = new Array(默认长度);
    3.var arr = [元素列表];
```
* 特点
    * JS中数组元素类型可变
    * 长度是可变的
### Date日期对象
* 创建方式：
```
var date = new Date();
```
### Math数学对象
* 创建：Math对象不用创建，直接Math.方法名();

### RegExp：正则表达式对象
定义字符串的组成规则
* 单个字符：[]
    * 如：[a],[ab]a或b,[a-zA-z0-9_]....
    * 特殊符号代表特殊含义的字符
        * \d:单个数字字符[0-9]
        * \w:单个单词字符[a-zA-z0-9_]
* 量词符号
    * ?:出现0次或1次
    * *:出现0次或多次
    * +:出现1次或多次
    * {m,n}:m <= 字符串长度 <= n;
* 开始：^
* 结束：$
* 创建方法：
    * 
    ``` 
    var reg = new RegExp("正则表达式")；
    ```
    * 
     ``` 
    var reg = \正则表达式\；
    ```
* 方法：对象.test(字符串)；用于校验
### Global
全局对象，不需要对象就可以调用方法
* encodeURI()编码
* decodeURI()解码
* encodeURIComponetr()
* decodeURIComponetr()
* parseInt()将字符串转化为数字
* eval()将字符串当作js脚本代码运行
* isNaN()判断是否为NaN
## BOM：Browser Object Model
* Navigator:浏览器对象
* Window：窗口对象
    * window对象不需要创建就可以使用，也不用window.方法名();
    * 方法：
        * alert()
        * confirm(),点击确定返回true，点击取消返回false
        * prompt(),输入框，返回用户输入的内容
        * open()打开新窗口
        * close()关闭窗口
    * 定时器
        * setTimeout()	在指定的毫秒数后调用函数或计算表达式。
            参数：
            * 方法体
            * 毫秒值
        * clearTimeout(定时器对象)取消setTimeout()设置的计时
        * setInterval()按周期执行代码
        * clearInterval(定时器对象)
    * 属性
        * 获取其他BOM对象
            *
* Location:地址栏
    * 获取
        window.location
    * 方法
        * reload()
        * href()
* History:历史记录对象
* Screen:显示器屏幕对象

## DOM:Document Object Model 文档对象模型
* 控制html文档的内容
* 代码：获取页面标签(元素)对象Element
    * document.getElementById("id")
* 操作Element对象
    * 修改属性值
        * 明确对象是什么
        * 查看文档寻找可以设置的属性值
    * 修改标签体内容

* Document
* Element
* Node
## 事件
某个组件被执行了某些操作，然后触发某些代码的执行
