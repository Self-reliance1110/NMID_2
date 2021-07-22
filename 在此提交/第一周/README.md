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
* <h1>-<h6>
* <p>
* <br/>:换行
* <hr>:水平线
* <b>:粗体
* <i>:斜体
### 图片标签
* <img src = ''/>
* src:指定图片的位置
* 相对路径：
    ./表示当前目录
    ../表示上一级目录
### 列表标签
* 有序列表
<ol>
<li>列表项</li>
<li>列表项</li>
</ol>

* 无序列表
<ul>
<li>列表项</li>
<li>列表项</li>
</ul>

### 超链接标签
* <a href = ''></a>
* href:指定打开资源的url
* target：指定打开资源的方式
* 可以与img标签一起使用
### div和span
### 语义化标签
为了增加程序的可读性
* <header>
* <footer>
### 表格标签
* <table></table>表格标签
* <tr></tr>行标签
* <th></th>表头标签
* <td></td>单元格标签
* <thead></thead>
* <tbody></tbody>
* <tfoot></tfoot>
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
