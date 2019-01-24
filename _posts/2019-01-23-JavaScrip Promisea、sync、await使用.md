---
layout: post
title:  JavaScrip Promise/async/await使用
date:   2019-01-23 15:44:00 +0800
categories: JavaScript
tag: 前端
---

* content
{:toc}


## 一、前端开发的几个痛点
- 1、动态网页交互的丰富性，很大部分来基于页面的事件驱动实现，而体验好的页面，避免不了需要异步执行功能。
- 2、`JavaScript`语言的单线程执行机制，使得异步调度难以实现。
- 3、事件驱动编码，可由回调函数机制实现，但复杂的回调逻辑可能出现嵌套回调代码的出现，使得代码可读性差，不易于维护。

## 二、模拟问题
### 1、功能需求
```
			/ 成功 -> 跳转登陆页面
用户登陆系统		
			\ 失败 -> 跳转失败页面
```
### 2、实现代码
#### 2.1、基于回调函数实现
##### 2.1.1、代码
```javascript
function login(params) {
	let name = params.name
	let pwd = params.pwd
	let success = params.success
	let fail = params.fail
	// 使用定时器，模拟登陆请求耗时
	setTimeout(() => {
		// 使用随机数模拟成功失败
		let res = Math.random() * 2
		if (res < 1) {
			console.log('登陆成功')
			success(name)
		} else {
			console.log('登陆失败')
			fail()
		}
	}, 1000)	
}

var params = {
	name: 'root', 
	pwd: 'root',
	success: (name) => {
		console.log(`欢迎 ${name}`)
	},
	fail: (err) => {
		console.log(`登陆信息有误: ${err}`)
	}
}

login(params)
```

##### 2.1.2、执行结果
登陆成功输出
```javascript
登陆成功
欢迎 root
```
登陆失败输出
```javascript
登陆失败
登陆信息有误: 密码错误
```
##### 2.1.3、代码问题
如果登陆成功或登陆失败的处理逻辑负责，可能需要编写其他回调函数，这样代码会变成一个比较深的树结构，代码可读性差。

#### 2.2、基于`Promise`实现
##### 2.2.1、知识点
Promise 的构造器接受一个函数作为参数，它会传递给这个回调函数两个变量 resolve 和 reject。在回调函数中做一些异步操作，成功之后调用 resolve，否则调用 reject。
##### 2.2.2、代码
```javascript
function login(params) {
	let name = params.name
	let pwd = params.pwd
	let promise = new Promise((resolve, rejected) => {
		// 使用定时器，模拟登陆请求耗时
		setTimeout(() => {
			// 使用随机数模拟成功失败
			let res = Math.random() * 2
			if (res < 1) {
				console.log('登陆成功')
				resolve(name)
			} else {
				console.log('登陆失败')
				rejected('密码错误')
			}
		}, 1000)
	})
	return promise
}

function submitLogin () {
	let params = {
		name: 'root', 
		pwd: 'root'
	}
	login(params).then(res => {
		console.log(`欢迎 ${name}`)		
	}).catch(err => {
		console.log(`登陆信息有误: ${err}`)
	})

}

submitLogin()
```
##### 2.2.3、执行结果
登陆成功输出
```javascript
登陆成功
欢迎 root
```
登陆失败输出
```javascript
登陆失败
登陆信息有误: 密码错误
```
##### 2.2.4、代码问题
同基于回调函数实现一样，可能存在嵌套回调函数问题。
解决了`JavaScript`单线程执行下，异步编码的问题。

#### 2.3、基于`async`/`await`实现
##### 2.3.1、知识点
###### ```async```
- 1、```async``` 用于修饰方法，表示该方法是同步执行
- 2、JavaScript会把```async```修饰的方法封装成一个Promise对象 

###### ```await```
- 1、```await```用于修饰表达式
- 2、每执行到```await```时，程序会暂停等待到```await```返回值，再往下执行
- 3、```await```必选再```async```修饰的方法中使用。在普通方法使用会报错
- 4、```await```修饰的Promise方法可能返回的是```rejected```，最好放在```try-catch```代码块中

##### 2.3.2、代码
```javascript
function login(params) {
	let name = params.name
	let pwd = params.pwd
	let promise = new Promise((resolve, rejected) => {
		// 使用定时器，模拟登陆请求耗时
		setTimeout(() => {
			// 使用随机数模拟成功失败
			let res = Math.random() * 2
			if (res < 1) {
				console.log('登陆成功')
				resolve(name)
			} else {
				console.log('登陆失败')
				rejected('密码错误')
			}
		}, 1000)
	})
	return promise
}

async function submitLogin () {
	let params = {
		name: 'root', 
		pwd: 'root'
	}
	try {
		let name = await login(params);
		console.log(`欢迎 ${name}`)
	} catch (err) {
		console.log(`登陆信息有误: ${err}`)	
	}	
}

submitLogin()
```

##### 2.3.3、执行结果
登陆成功输出
```javascript
登陆成功
欢迎 root
```
登陆失败输出
```javascript
登陆失败
登陆信息有误: 密码错误
```

##### 2.3.4、代码问题
相比前面两种实现，没有了可能出现嵌套回调函数的问题，代码可读性好。
暂时还没发现其他缺点



