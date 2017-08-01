# ajax 获取 json文件
> 项目中遇到使用`ajax`获取`json`的场景,但多次尝试始终获取不到,直接跳到`error`进程,一番搜素才发现问题十分简单,`json`格式不标准.
* `JSON`字符串里的非数字型键值没有双引号
* `JSON`中存在`\t`这样的制表符
* `JSON`中最后一条成员不可含有符号`,`
---
#### error
```json
{
  c1:"c1 content",
  c2:"c2 content",
}
```
#### true
```json
{
  "c1":"c1 content",
  "c2":"c2 content"
}
```
---

