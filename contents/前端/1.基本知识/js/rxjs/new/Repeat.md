## repeat操作符
- 将数据源重复n次，n为你传入的数字类型参数。
```js
of(1, 2, 3).pipe(repeat(3)).subscribe(); // 1、2、3会输出3次
// 1
// 2
// 3
// 1
// 2
// 3
// 1
// 2
// 3
```