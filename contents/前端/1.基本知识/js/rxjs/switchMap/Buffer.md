## buffer操作符
- 将**过往的**值收集到一个数组中，并且**仅当**另一个 Observable 发出通知时**才发出**此数组
- 即将Observable发出的数据流先储存到缓存区，等到另一个Observable发出信号了，才将之前储存的数据流以数组的形式发送出来。
- 将之前存储的数据发送出来后，缓存区就被清空
```js
interval(1000).pipe( // 每隔1s发出一次值
    buffer(          // 将发出的值储存到缓存区
        interval(1000*5) // 每隔5s发送一次信号，将之前缓存的数据以数组形式发送出来
    )
).subscribe(data=>console.log(data));
// [0, 1, 2, 3, 4]
// [5, 6, 7, 8, 9]
// [10, 11, 12, 13, 14]
// ...
```