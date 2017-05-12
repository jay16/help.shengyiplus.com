``` javascript
SHENGYIPLUSVERSION = '0.1.0'
String.prototype.trim = function() {
  return this.replace(/(^\s*)|(\s*$)/g,'');
}

/*
 * Android <=> JS 交互接口
 */
window.SYPWithinAndroid = {
  version: function() {
    return 'android ' + SHENGYIPLUSVERSION;
  },
  /*
   * javascript 异常时通知原生代码，或提交服务器，或 popup 提示用户
   */
  jsException: function(e) {
    if(window.AndroidJSBridge && typeof(window.AndroidJSBridge.jsException) === "function") {
      window.AndroidJSBridge.jsException(e);
    }
  },
  /*
   * 该中间件版本过低时，APP 会给出提示
   */
  checkVersion: function() {
    if(window.AndroidJSBridge && typeof(window.AndroidJSBridge.checkVersion) === "function") {
      window.AndroidJSBridge.checkVersion(SHENGYIPLUSVERSION);
    }
  },
  /*
   * 浏览器『返回』
   */
  goBack: function() {
    if(window.AndroidJSBridge && typeof(window.AndroidJSBridge.goBackBehavior) === "function") {
      window.AndroidJSBridge.goBackBehavior();
    } else {
      alert("Error 未定义接口(Android): goBack");
    }
  },
  /*
   * 链接跳转
   *
   * bannerName : 标题栏使用原生代码开发，该参数显示为标题
   * link       : 主体为浏览器，加载该链接
   * objectId   : 业务对象ID (项目内部使用，其他可传 -1)
   */
  pageLink: function(bannerName, link, objectId) {
    if(window.AndroidJSBridge && typeof(window.AndroidJSBridge.pageLink) === "function") {
      window.AndroidJSBridge.pageLink(bannerName, link.trim(), objectId);
    } else {
      alert("Error 未定义接口(Android): pageLink");
    }
  },
  /*
   * 原生弹出框
   *
   * 网页内部的 `alert` 弹出框，标题为网页所在文件夹名称，用户体验不佳。
   *
   * title  : 标题名称（暂未使用，可传空）
   * message: 弹出框内容
   */
  showAlert: function(title, message) {
    if(window.AndroidJSBridge && typeof(window.AndroidJSBridge.showAlert) === "function") {
      window.AndroidJSBridge.showAlert(title, message);
    } else {
      alert("Error 未定义接口(Android): showAlert");
    }
  },
  /*
   * 原生弹出框，点击『确定』后跳转至其他链接
   *
   * 使用原生弹出框但继续使用网页内部的跳转方式时，跳转操作不会等弹出框的业务操作
   *
   * title       : 标题名称（暂未使用，可传空）
   * message     : 弹出框内容
   * redirectUrl : 待跳转的链接
   */
  showAlertAndRedirect: function(title, message, redirectUrl) {
    if(window.AndroidJSBridge && typeof(window.AndroidJSBridge.showAlertAndRedirect) === "function") {
      window.AndroidJSBridge.showAlertAndRedirect(title, message, redirectUrl, 'no');
    } else {
      alert("Error 未定义接口(Android): showAlertAndRedirect");
    }
  },
  /*
   * 原生弹出框，点击『确定』后跳转至其他链接，清空链接栈
   *
   * 有些业务流程不可以返回历史链接，比如『新建』，所以跳转至该链接时，清空栈，点击标题栏中的『返回』则关闭浏览器
   *
   * title       : 标题名称（暂未使用，可传空）
   * message     : 弹出框内容
   * redirectUrl : 待跳转的链接
   */
  showAlertAndRedirectWithCleanStack: function(title, message, redirectUrl) {
    if(window.AndroidJSBridge && typeof(window.AndroidJSBridge.showAlertAndRedirect) === "function") {
      window.AndroidJSBridge.showAlertAndRedirect(title, message, redirectUrl, 'yes');
    } else {
      alert("Error 未定义接口(Android): showAlertAndRedirect");
    }
  },
  /*
   * 控制原生标题栏的隐藏及显示
   *
   * 有些业务操作在弹出页面中已有标题栏，此时可以通过该函数控制原生标题栏的隐藏及显示
   *
   * state: 显示或隐藏（show/hidden）
   */
  toggleShowBanner: function(state) {
    if(window.AndroidJSBridge && typeof(window.AndroidJSBridge.toggleShowBanner) === "function") {
      window.AndroidJSBridge.toggleShowBanner(state);
    } else {
      alert("Error 未定义接口(Android):  toggleShowBanner");
    }
  },
  /*
   * 首页底部页签显示 badge 数字
   *
   * type: 类型（仅支持: total）
   * num : badge 数字
   */
  appBadgeNum: function(type, num) {
    if(window.AndroidJSBridge && typeof(window.AndroidJSBridge.appBadgeNum) === "function") {
      window.AndroidJSBridge.appBadgeNum(type, num);
    } else {
      alert("Error 未定义接口(Android): appBadgeNum");
    }
  },
  /*
   * 控制标题栏中标题内容
   *
   * title: 标题内容
   */
  setBannerTitle: function(title) {
    if(window.AndroidJSBridge && typeof(window.AndroidJSBridge.setBannerTitle) === "function") {
      window.AndroidJSBridge.setBannerTitle(title);
    } else {
      alert("Error 未定义接口(Android): setBannerTitle");
    }
  },
  /*
   * 添加左上角的菜单项
   *
   * menu_items: 菜单项内容（数组）
   *
   * [{
   *   title: '销售录入历史',
   *   link: 'offline://sale.input.historical.html'
   *  }];
   */
  addSubjectMenuItems: function(menuItems) {
    if(window.AndroidJSBridge && typeof(window.AndroidJSBridge.addSubjectMenuItems) === "function") {
      window.AndroidJSBridge.addSubjectMenuItems(JSON.stringify(menu_items));
    } else {
      alert("Error 未定义接口:  addSubjectMenuItems");
    }
  }
};

/*
 * iOS <=> JS 交互接口
 */
window.SYPWithinIOS = {
  version: function() {
    return 'ios ' + SHENGYIPLUSVERSION;
  },
  connectWebViewJavascriptBridge: function(callback) {
    if(window.WebViewJavascriptBridge) {
      callback(WebViewJavascriptBridge)
    }
    else {
      document.addEventListener('WebViewJavascriptBridgeReady', function() {
        callback(WebViewJavascriptBridge)
      }, false)
    }
  },
  jsException: function(e) {
    SYPWithinIOS.connectWebViewJavascriptBridge(function(bridge){
      bridge.callHandler('jsException', {ex: e}, function(response) {});
    })
  },
  checkVersion: function() {
    SYPWithinIOS.connectWebViewJavascriptBridge(function(bridge){
      bridge.callHandler('checkVersion', {version: SHENGYIPLUSVERSION}, function(response) {});
    })
  },
  goBack: function() {
    SYPWithinIOS.connectWebViewJavascriptBridge(function(bridge){
      bridge.callHandler('goBackBehavior', {}, function(response) {});
    })
  },
  pageLink: function(bannerName, link, objectId) {
    SYPWithinIOS.connectWebViewJavascriptBridge(function(bridge){
      bridge.callHandler('iosCallback', {'bannerName': bannerName, 'link': link, 'objectID': objectId}, function(response) {});
    })
  },
  showAlert: function(title, message) {
    SYPWithinIOS.connectWebViewJavascriptBridge(function(bridge){
      bridge.callHandler('showAlert', {'title': title, 'content': message}, function(response) {});
    })
  },
  showAlertAndRedirect: function(title, message, redirectUrl) {
    SYPWithinIOS.connectWebViewJavascriptBridge(function(bridge){
      bridge.callHandler('showAlertAndRedirect', {'title': title, 'content': message, 'redirectUrl': redirectUrl, 'cleanStack': 'no'}, function(response) {});
    })
  },
  showAlertAndRedirectWithCleanStack: function(title, message, redirectUrl) {
    SYPWithinIOS.connectWebViewJavascriptBridge(function(bridge){
      bridge.callHandler('showAlertAndRedirect', {'title': title, 'content': message, 'redirectUrl': redirectUrl, 'cleanStack': 'yes'}, function(response) {});
    })
  },
  toggleShowBanner: function(state) {
    SYPWithinIOS.connectWebViewJavascriptBridge(function(bridge){
      bridge.callHandler('toggleShowBanner', {'state': state}, function(response) {});
    })
  },
  appBadgeNum: function(type, num) {
    SYPWithinIOS.connectWebViewJavascriptBridge(function(bridge){
      bridge.callHandler('appBadgeNum', {'type': type, 'num': num}, function(response) {});
    })
  },
  setBannerTitle: function(title) {
    SYPWithinIOS.connectWebViewJavascriptBridge(function(bridge){
      bridge.callHandler('setBannerTitle', {'title': title}, function(response) {});
    })
  },
  addSubjectMenuItems: function(menuItems) {
    SYPWithinIOS.connectWebViewJavascriptBridge(function(bridge){
      bridge.callHandler('addSubjectMenuItems', {'menu_items': menuItems}, function(response) {});
    })
  }
};

/*
 * PC <=> JS 交互接口
 */
window.SYP = {
  version: function() {
    return 'default ' + SHENGYIPLUSVERSION;
  },
  checkVersion: function() {
    console.log('default ' + SHENGYIPLUSVERSION);
  },
  jsException: function(e) {
    console.log(typeof(e));
    console.log(e);
  },
  goBack: function() {
    window.history.back();
  },
  pageLink: function(bannerName, link, objectId) {
    window.location.href = htmlName;
  },
  showAlert: function(title, message) {
    alert(message);
  },
  showAlertAndRedirect: function(title, message, redirectUrl) {
    alert(message);
    window.location.href = redirectUrl.replace('offline://', '');
  },
  showAlertAndRedirectWithCleanStack: function(title, message, redirectUrl) {
    alert(message);
    SYP.pageLink(redirectUrl.replace('offline://', ''));
  },
  toggleShowBanner: function(state) {
    console.log(window.SYP.version());
    console.log({'toggleShowBanner state': state});
  },
  appBadgeNum: function(type, num) {
    console.log(window.SYP.version());
    console.log({'type': type, 'num': num});
  },
  setBannerTitle: function(title) {
    console.log(window.SYP.version());
    console.log({'title': title});
    $(document).attr("title", title);
  },
  addSubjectMenuItems: function(menuItems) {
    console.log(window.SYP.version());
    console.log({'menu_items': menuItems});
  }
};

var userAgent = navigator.userAgent,
    isAndroid = userAgent.indexOf('Android') > -1 || userAgent.indexOf('Adr') > -1,
    isiOS = !!userAgent.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/);
if(isiOS) {
  window.SYP = window.SYPWithinIOS;
} else if(isAndroid) {
  window.SYP = window.SYPWithinAndroid;
}

window.SYP.checkVersion();
window.onerror = function(e) {
  window.SYP.jsException(e);
}

```
