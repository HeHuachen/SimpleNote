# ES6_Scope
#### 函数体内没有同名参数时可以用let声明
```javascript
let x = 1;
let foo = (y = () => x = 2) = {
  let x = 3;
  y();
  console.log(x);
};
foo(); //3
x; //2
```
#### 函数体内有同名参数时只能使用var声明
```javascript
let x = 1;
let foo = (x, y = () => x = 2) = {
  let x = 3;
  y();
  console.log(x);
};
foo(); //3
x; //2
```
所以我的理解是，ES6当函数声明初始化时，函数主体可以使用var重复声明，但不能使用let声明，所以是否就是说声明初始化过程中，参数传递到函数体中是通过var声明的，这样就可以解释上述运行结果了。
