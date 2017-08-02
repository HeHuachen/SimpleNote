# 动态添加`script`标签

> 这些问题发生在使用`jquery append script`的场景中，产生提示报错或警告结果

```javascript
   $("body").append("<script src='script.js'></script>");
```

* 本地chrome浏览器使用`jquery append script`报错  
__XMLHttpRequest cannot load file:///XXX. Cross origin requests are only supported for protocolschemes: http, data, chrome, chrome-extension, https, chrome-extension-resource.__

* 放到空间里测试，代码可以执行但是在`chrome`和`firefox`浏览器都会提示警告  
__Synchronous XMLHttpRequest on the main thread is deprecated because of its detrimentaleffects to the end user's experience. For more help, check https://xhr.spec.whatwg.org/.__

#### 原生测试

因为此问题全是使用`jquery`产生的，考虑到此问题是否`jquery`内部`append`方法机制问题，所以测试`javascript`重新编写

```javascript
  var create_js = document.createElement("script");
  create_js.setAttribute("src","script.js");
  document.body.appendChild(create_js);
```
打开控制台问题完美解决，随即stackoverflow了一下，找到如下解释

> All of jQuery's insertion methods use a domManip function internally to clean/process elements before and after they are inserted into the DOM. One of the things the domManip function does is pull out any script elements about to be inserted and run them through an "evalScript routine" rather than inject them with the rest of the DOM fragment. It inserts the scripts separately, evaluates them, and then removes them from the DOM.

> I believe that one of the reasons jQuery does this is to avoid "Permission Denied" errors that can occur in Internet Explorer when inserting scripts under certain circumstances. It also avoids repeatedly inserting/evaluating the same script (which could potentially cause problems) if it is within a containing element that you are inserting and then moving around the DOM.

所以在动态添加脚本应该尽量使用javascript而不是jquery

#### juqery实现

如果非要使用jquery实现的话，其实jquery有一个现成的方法`$.getScript`
```javascript
   jQuery.getScript(url,success(response,status))
   //url:将要请求的 URL 字符串。
   //success:规定请求成功后执行的回调函数
       //_response - 包含来自请求的结果数据
       //_status - 包含请求的状态（"success", "notmodified", "error", "timeout" 或 "parsererror"）
```
此方法的本质使用 AJAX GET 请求载入并执行 JavaScript 文件等价于
```javascript
    $.ajax({
        url: url,
        dataType: "script",
        success: success
    });
```
> 注意: jQuery 1.2 版本之前，getScript 只能调用同域 JS 文件。 1.2中，您可以跨域调用 JavaScript 文件。注意：Safari 2 或更早的版本不能在全局作用域中同步执行脚本。如果通过 getScript 加入脚本，请加入延时函数。
  
 