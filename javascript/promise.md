async和await：

`async` 用于申明一个 `function` 是异步的，而 `await` 用于等待一个异步方法执行完成

async其实就是声明一个函数是异步函数，返回一个promise，其本质是一个new Promise的一个包装，因为他和new Promise有一个很像的地方就是

- 返回Promise
- 在没遇到返回值，或者没遇到await之前都是同步执行的





**`await`只能放在`async`函数内部使用**

`await` 用于一个异步操作之前，表示要“等待”这个异步操作的返回值。
`await` 也可以用于一个同步的值





- 如果它等到的不是一个 `Promise` 对象，那 await 表达式的运算结果就是它等到的东西。
-  如果它等到的是一个 `Promise` 对象，`await` 就会阻塞后面的代码，只会阻塞await所在的函数，等着 `Promise` 对象 `resolve`，然后得到 `resolve` 的值，作为 `await` 表达式的运算结果

这样你会发现一个问题，如果只用async不用await的话，根本没办法获得异步的解果，之前在promise中是通过传一个callback，而这里如果不用await的话，也只能传一个callback；但如果有了await的话，就没必要传callback，在callback里获取解果了，可以直接在await的后面接处理结果的代码。









## async和promise区别：

Promise 是用类似 Monad 的形式来处理 [成功|失败] 这样的数据。

```javascript
Promise 是用类似 Monad 的形式来处理 [成功|失败] 这样的数据。

promise = ajax('/users');
promise.then(onSuccess1, Onerror1).then(onSuccess2, Onerror2)
```

后来为了开发更方便，又多了catch,finally,all这些

但这样的写法感觉不够好，没有同步代码的feel，所以后来除了async和await，await一般只能在async函数中使用，除非环境支持顶级await

```javascript
let response;
try {
	response = await ajax("/users")
} catch(error) {
	onError(error)
	throw erreor //如果你不想onSuccess，可以直接退出
}
onSuccess(response)
```

所以一般认为async和await是promise的语法糖



但是await其实也不好看，有的人一般两者搭配使用

```javascript
const response = awiat ajax("/users").catch(onError)
onSuccess(respone)

如果成功，则respnse是一个成功的value，且不会执行catch，继而执行oSucccess；反之会执行catch，可以在onError中放throw直接退出
```

