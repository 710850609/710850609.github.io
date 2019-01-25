---
layout: post
title:  JavaScrip Promise/async/await使用
date:   2019-01-23 15:44:00 +0800
categories: JavaScript
tag: 前端
---

* content
{:toc}

## 一、文章背景
动态网页交互的丰富性，很大部分来基于页面的事件驱动实现，而体验好的页面，需要异步执行功能的支持

## 二、前端开发的2个痛点
- 1、JavaScript语言的单线程执行机制，使得异步调度难以用同步代码的写法实现。
- 2、事件驱动编码，可由回调函数机制实现，但复杂的回调逻辑可能出现嵌套回调代码的出现，使得代码可读性差，不易于维护。

## 三、模拟问题
用户访问系统，需要进行登陆（请求登陆接口），如果登陆成功，则跳转欢迎页面，如果登陆失败，则跳转失败页面，流程如下：
```
           / 成功 -> 跳转欢迎页面
用户登陆系统		
           \ 失败 -> 跳转失败页面
```

## 四、ES6前的实现
### 1、代码
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

### 2、执行结果
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
### 3、代码问题
- 1、如果登陆成功或登陆失败的处理逻辑负责，可能需要编写其他回调函数，这样代码会变成一个比较深的树结构，代码可读性差。
- 2、如果登陆有其他处理逻辑，会出现代码逻辑分块，（回调一块，非回调一块），这样的代码可读性也不好。

## 五、基于Promise实现
### 1、知识点
Promise 的构造器接受一个函数作为参数，它会传递给这个回调函数两个变量 resolve 和 reject。在回调函数中做一些异步操作，成功之后调用 resolve，否则调用 reject。

### 2、代码
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

### 3、执行结果
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

### 4、代码问题
- 1、同基于回调函数实现一样，可能存在嵌套回调函数问题。
- 2、解决了JavaScript单线程执行下，异步编码的问题。

## 六、基于Promise/async/await实现
### 1、知识点
#### async
- 1、async 用于修饰方法，表示该方法是同步执行
- 2、JavaScript会把async修饰的方法封装成一个Promise对象 

#### await
- 1、await用于修饰表达式
- 2、每执行到await时，程序会暂停等待到await返回值，再往下执行
- 3、await必选再async修饰的方法中使用。在普通方法使用会报错
- 4、await修饰的Promise方法可能返回的是rejected，最好放在try-catch代码块中

### 2、代码
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

### 3、执行结果
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

### 4、代码问题
- 1、相比前面两种实现，没有了可能出现嵌套回调函数的问题，代码可读性好。
- 2、异步执行代码可以变成类似同步代码编写，使整体逻辑更清晰。

## 七、总结
- 1、使用Promise解决JavaScript语言的单线程执行机制下，异步调度的问题。
- 2、使用async和await解决嵌套回调函数的写法，化异步为同步写法，使代码逻辑更清晰，可读性更好。




