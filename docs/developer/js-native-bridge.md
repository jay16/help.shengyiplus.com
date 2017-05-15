## 原生代码与 JS 交互接口

场景：

1. 应用首页各页签模块『仪表盘』『分析』『应用』中内嵌第三方服务链接或项目内部离线页面。
2. 第三方服务链接或项目内部离线页面通过 JS 接口调用应用内部原生控件，以改善用户操作体验

### 接口列表

[SYP 源代码](/docs/developer/syp_v1.js.md)

```
/*
 * javascript 异常时通知原生代码，或提交服务器，或 popup 提示用户
 */
jsException: function(e)

/*
 * 该中间件版本过低时，APP 会给出提示
 */
checkVersion: function()

/*
 * 浏览器『返回』
 */
goBack: function()

/*
 * 链接跳转
 *
 * bannerName : 标题栏使用原生代码开发，该参数显示为标题
 * link       : 主体为浏览器，加载该链接
 * objectId   : 业务对象ID (项目内部使用，其他可传 -1)
 */
pageLink: function(bannerName, link, objectId)

/*
 * 原生弹出框
 *
 * 网页内部的 `alert` 弹出框，标题为网页所在文件夹名称，用户体验不佳。
 *
 * title  : 标题名称（暂未使用，可传空）
 * message: 弹出框内容
 */
showAlert: function(title, message)

/*
 * 原生弹出框，点击『确定』后跳转至其他链接
 *
 * 使用原生弹出框但继续使用网页内部的跳转方式时，跳转操作不会等弹出框的业务操作
 *
 * title       : 标题名称（暂未使用，可传空）
 * message     : 弹出框内容
 * redirectUrl : 待跳转的链接
 */
showAlertAndRedirect: function(title, message, redirectUrl)

/*
 * 原生弹出框，点击『确定』后跳转至其他链接，清空链接栈
 *
 * 有些业务流程不可以返回历史链接，比如『新建』，所以跳转至该链接时，清空栈，点击标题栏中的『返回』则关闭浏览器
 *
 * title       : 标题名称（暂未使用，可传空）
 * message     : 弹出框内容
 * redirectUrl : 待跳转的链接
 */
showAlertAndRedirectWithCleanStack: function(title, message, redirectUrl)

/*
 * 控制原生标题栏的隐藏及显示
 *
 * 有些业务操作在弹出页面中已有标题栏，此时可以通过该函数控制原生标题栏的隐藏及显示
 *
 * state: 显示或隐藏（show/hidden）
 */
toggleShowBanner: function(state)

/*
 * 首页底部页签显示 badge 数字
 *
 * type: 类型（仅支持: total）
 * num : badge 数字
 */
appBadgeNum: function(type, num)

/*
 * 控制标题栏中标题内容
 *
 * title: 标题内容
 */
setBannerTitle: function(title)

/*
 * 添加左上角的菜单项
 *
 * menu_items: 菜单项内容（数组）
 *
 * [{
 *   title: '销售录入历史',
 *   link: 'offline://demo@sale.input.historical.html'
 *  }];
 */
addSubjectMenuItems: function(menuItems)
```

实例操作：

1. 动态设置标题栏标题内容

```
window.SYP.setBannerTitle("whatever you want to set");
```

2. 动态隐藏、显示标题栏

```
window.SYP.toggleShowBanner('hidden');

// do somthing...
// done

window.SYP.toggleShowBanner('show');
```

3. 新建实例成功后，弹出框提示用户『创建成功』，点击『确定』后，跳转至实例列表页面，同时清空链接栈，这样点击标题栏中的『返回』就会关闭主题面

```
window.SYP.showAlertAndRedirectWithCleanStack("温馨提示", "创建成功", "http://demo.com/instance/list.html");
```

### 第三方在线链接

1. 加载 `syp_v1.js`
2. 业务流程层级跳转

    ```
    <script type="text/javascript" src="syp_v1.js"></script>
    <a href="javascript:void(0);" onclick="nextStep();">下一步</a>

    <script type="text/javascript">
      function nextStep() {
        window.SYP.pageLink("网页标题", "http://demo.com/next-step.html", -1);
      }
    </script>
    ```

    必须按上述示例操作的原因：[应用内部浏览器跳转](/docs/developer/module-app.md)

### 离线页面规范

TODO



