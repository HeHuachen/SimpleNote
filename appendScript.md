# 动态添加`script`标签
> 这些问题发生在使用`jquery append script`的场景中，产生提示报错或警告结果
  ```javascript
     $("body").append("<script src='script.js'></script>");
  ```
  * 本地chrome浏览器使用`jquery append script`报错```XMLHttpRequest cannot load file:///XXX. Cross origin requests are only supported for protocol schemes: http, data, chrome, chrome-extension, https, chrome-extension-resource.```
  * 放到空间里测试，代码可以执行但是在`chrome`和`firefox`浏览器都会提示警告 ```Synchronous XMLHttpRequest on the main thread is deprecated because of its detrimental effects to the end user's experience. For more help, check https://xhr.spec.whatwg.org/.```
#### 原生测试
  因为此问题全是使用`jquery`产生的，考虑到此问题是否`jquery`内部`append`方法机制问题，所以测试用`javascript`重新编写
  ```javascript
    var create_js = document.createElement("script");
    create_js.setAttribute("src","script.js");
    document.body.appendChild(create_js);
  ```
  打开控制台问题完美解决，随即stackoverflow了一下，找到如下解释
    > All of jQuery's insertion methods use a domManip function internally to clean/process elements before and after they are inserted into the DOM. One of the things the domManip function does is pull out any script elements about to be inserted and run them through an "evalScript routine" rather than inject them with the rest of the DOM fragment. It inserts the scripts separately, evaluates them, and then removes them from the DOM.

    > I believe that one of the reasons jQuery does this is to avoid "Permission Denied" errors that can occur in Internet Explorer when inserting scripts under certain circumstances. It also avoids repeatedly inserting/evaluating the same script (which could potentially cause problems) if it is within a containing element that you are inserting and then moving around the DOM.
  
 