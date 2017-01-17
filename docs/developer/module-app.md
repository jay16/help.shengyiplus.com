### 第三方链接界面中提交按钮点击跳转至系统浏览器

提交按钮若使用超链接 `a` 标签，点击事件通过 jquery 动态绑定，导致点击时同时执行了超链接默认的跳转属性。

错误网页代码如下：

```
<a id="linkbutton" href=“#" data-options="iconCls:'icon-search'">
    <span class="l-btn-left l-btn-icon-left">
        <span class="l-btn-text">查询</span>
        <span class="l-btn-icon icon-search">&nbsp;</span>
    </span>
</a>
```

```
<script-tag type="text/javascript">
$('#linkbutton').bind('click', function(){
     ...
 });
</script-tag>
```

建议调整两个属性值：

- `href` 设置为 `javascript:void(0);`, 这是跳转的目的链接。（`#` 也算锚点）
- `onClick` 设置为功能函数名称。

调整后的代码应该类似：

```
<a id="linkbutton" href="javascript:void(0);" onClick="refreshDataGrid()" data-options="iconCls:'icon-search'">
    <span class="l-btn-left l-btn-icon-left">
        <span class="l-btn-text">查询</span>
        <span class="l-btn-icon icon-search">&nbsp;</span>
    </span>
</a>
```

```
<script-tag type="text/javascript">
function refreshDataGrid() {
     ...
 });
</script-tag>
```
