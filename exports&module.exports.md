> The `exports` variable is available within a module's file-level scope, and is
assigned the value of `module.exports` before the module is evaluated.
> 
> It allows a shortcut, so that `module.exports.f = ...` can be written more
succinctly as `exports.f = ...`. However, be aware that like any variable, if a
new value is assigned to `exports`, it is no longer bound to `module.exports`:
> 
> ```js
> module.exports.hello = true; // Exported from require of module
> exports = { hello: false };  // Not exported, only available in the module
> ```
> 
> When the `module.exports` property is being completely replaced by a new
object, it is common to also reassign `exports`, for example:
> 
<!-- eslint-disable func-name-matching -->
> ```js
> module.exports = exports = function Constructor() {
>   // ... etc.
> };
> ```
> 
> To illustrate the behavior, imagine this hypothetical implementation of
`require()`, which is quite similar to what is actually done by `require()`:
> 
> ```js
> function require(/* ... */) {
>   const module = { exports: {} };
>   ((module, exports) => {
>     // Module code here. In this example, define a function.
>     function someFunc() {}
>     exports = someFunc;
>     // At this point, exports is no longer a shortcut to module.exports, and
>     // this module will still export an empty default object.
>     module.exports = someFunc;
>     // At this point, the module will now export someFunc, instead of the
>     // default object.
>   })(module, module.exports);
>   return module.exports;
> }
> ```

**`exports`和`module.exports`的关系其实很简单，`require`返回的是`module.exports`对象，而`exports`是对`module.exports`对象的引用**

所以在模块编写中导出初始对象的属性方法时，他们所指向的对象是同一个对象，此时`exports`和`module.exports`的功能是没有区别的。

```js
exports.func = function () {
  // ...
}
// 等同于
module.exports.func = function () {
  // ...
}
```
但是当`exports`指向被改变时，他们之间就完全不一样了

```js
exports = 'string'
// 此时 exports 指引了新的内存地址，就已经和 module.exports 没有关系了
// 这样是无法导出的

module.exports = 'string'
// 但是 module.exports 同样的操作就完全没问题了
// 因为 require 返回的就是 module.exports 
// 所以 module.exports 可以导出任意数据类型
```
