# 5. Promises API Reference

## 5.1. Promise#then

```

    promise.then(onFulfilled, onRejected);

```

**then 代码示例**

```

    var promise = new Promise(function(resolve, reject){
        resolve("传递给then的值");
    });
    promise.then(function (value) {
        console.log(value);
    }, function (error) {
        console.error(error);
    });

```

这段代码创建一个 promise 对象，定义了处理 onFulfilled 和 onRejected 的函数（handler），然后返回这个 promise 对象。

这个 promise 对象会在变为 resolve 或者 reject 的时候分别调用相应注册的回调函数。

- 当 handler 返回一个正常值的时候，这个值会传递给 promise 对象的 onFulfilled 方法。

- 定义的 handler 中产生异常的时候，这个值则会传递给 promise 对象的 onRejected 方法。

## 5.2. Promise#catch

```

    promise.catch(onRejected);

```

**catch 代码示例**

```

    var promise = new Promise(function(resolve, reject){
        resolve("传递给then的值");
    });
    promise.then(function (value) {
        console.log(value);
    }).catch(function (error) {
        console.error(error);
    });

```

这是一个等价于 `promise.then(undefined, onRejected)` 的语法糖。

## 5.3. Promise.resolve

```

    Promise.resolve(promise);
    Promise.resolve(thenable);
    Promise.resolve(object);
    Promise.resolve代码示例

```

```

    var taskName = "task 1"
    asyncTask(taskName).then(function (value) {
        console.log(value);
    }).catch(function (error) {
        console.error(error);
    });
    function asyncTask(name){
        return Promise.resolve(name).then(function(value){
            return "Done! "+ value;
    });
    }

```

根据接收到的参数不同，返回不同的 promise 对象。

虽然每种情况都会返回 promise 对象，但是大体来说主要分为下面3类。

**接收到 promise 对象参数的时候**  
返回的还是接收到的 promise 对象

**接收到 thenable 类型的对象的时候**  
返回一个新的 promise 对象，这个对象具有一个 then 方法

**接收的参数为其他类型的时候（包括 JavaScript 对或 null 等）**  
返回一个将该对象作为值的新 promise 对象

## 5.4. Promise.reject

    Promise.reject(object)

**Promise.reject 代码示例**

```

     var failureStub = sinon.stub(xhr, "request").returns(Promise.reject(new Error("bad!")));

```

返回一个使用接收到的值进行了 reject 的新的 promise 对象。

而传给 Promise.reject 的值也应该是一个 `Error` 类型的对象。

另外，和 Promise.resolve 不同的是，即使 Promise.reject 接收到的参数是一个 promise 对象，该函数也还是会返回一个全新的 promise 对象。

```

    var r = Promise.reject(new Error("error"));
    console.log(r === Promise.reject(r));// false


```

## 5.5. Promise.all

    Promise.all(promiseArray);

**Promise.all代码示例**

```

    var p1 = Promise.resolve(1),
        p2 = Promise.resolve(2),
        p3 = Promise.resolve(3);
    Promise.all([p1, p2, p3]).then(function (results) {
        console.log(results);  // [1, 2, 3]
    });

```

生成并返回一个新的 promise 对象。

参数传递 promise 数组中所有的 promise 对象都变为 resolve 的时候，该方法才会返回， 新创建的 promise 则会使用这些 promise 的值。

如果参数中的任何一个 promise 为 reject 的话，则整个 Promise.all 调用会立即终止，并返回一个 reject 的新的 promise 对象。

由于参数数组中的每个元素都是由 `Promise.resolve` 包装（wrap）的，所以Paomise.all可以处理不同类型的promose对象。

## 5.6. Promise.race

    Promise.race(promiseArray);

**Promise.race代码示例**

```
    var p1 = Promise.resolve(1),
        p2 = Promise.resolve(2),
        p3 = Promise.resolve(3);
    Promise.race([p1, p2, p3]).then(function (value) {
        console.log(value);  // 1
    });

```

生成并返回一个新的 promise 对象。

参数 promise 数组中的任何一个 promise 对象如果变为 resolve 或者 reject 的话， 该函数就会返回，并使用这个 promise 对象的值进行 resolve 或者 reject。