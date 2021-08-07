---
layout:     post
title:      "WEB Storage基础"
subtitle:   "WEB Storage基础"
date:       2020-08-08 14:41
author:     "Henry"
header-img: "img/post-bg-miui6.jpg"
tags:
    - WEB Storage
    - WEB
---

### Web Storage:

- localStorage

```
localStorage的生命周期是永久性的。假如使用localStorage存储数据，即使关闭浏览器，也不会让数据消失，除非主动的去删除数据。
```

- SessionStorage

```
sessionStorage的生命周期是在浏览器关闭前。也就是说，在整个浏览器未关闭前，其数据一直存在。

1. 页面刷新不会消除数据
2. 只有在当前页面打开的链接，才可以访问sessionStorage的数据
3. 使用window.open打开页面和改变localtion.href方式都可以获取到sessionStorage内部的数据
```

- Web Storage的优点？

```
1. 存储空间大
2. 存储的内容不会发送到服务 器端
3. 更丰富的接口，使得数据存储更简单
4. 是独立的存储空间
```

- Web Storage的缺点？

```
1. 浏览器会为每个域分配独立的空间，脚本A无法访问B的内容。B域嵌套了A的脚本，依然可以访问B域的信息
2. 存储本地的信息，未加密的永远不会过期
```

> 参考文献：[WebStorage是什么？ - 简书](https://www.jianshu.com/p/26e1e9fa7089)


### Web Storage的使用

#### 检查是否支持 本文档以localStorage为示例，sessionStorage雷同

> 支持点语法

```
localStorage.a = localStorage.getItem('a');
```


```
<script type="application/javascript">
    function checkStorageSupport() {
    	if (window.sessionStorage) {
    		console.log('suppor sessionStorage!')
    	} else {
    		conosle.log('not suppor sessionStorage!')
    	}
    	
    	if (window.localStorage) {
    		console.log('suppor localStorage!')
    	} else {
    		conosle.log('not suppor localStorage!')
    	}
    			
    	window.onload = function() {
    		checkStorageSupport();
    	}
    }
</script>
```


方法名 | 描述
---|---
setItem(key, value) | 保存数据，以键值对的方式存储
getItem(key) | 获取数据，传入键，获取对应的value值
removeItem(key) | 删除单个数据，根据键
clear() | 删除所有数据
key(index) | 获取某个索引的key
length | 获取长度
remainSpace | 未知-大部分浏览器都不支持

#### 方法实践


```
<!DOCTYPE html>
<html>
	<head>
		<meta charset="UTF-8">
		<title>LocalStorage方法实践</title>
		<script type="text/javascript">
			/**
			 * 通过指定的id，获取document对象
			 * @param {String} id
			 */
			function $(id) {
				return document.getElementById(id);
			}
			
			/**
			 * 设定LocalStorage键值对
			 */
			function setItem() {
				let key = $('key').value;
				let value = $('value').value;
				
				localStorage.setItem(key, value);
				refreshStorage();
			}
			
			/**
			 * 获取指定键的value
			 */
			function getItem() {
				let key = $('key').value;
				
				let value = localStorage.getItem(key);
				alert(`${key}: ${value}`);
			}
			
			/**
			 * 删除指定键的value
			 */
			function removeItem() {
				let key = $('key').value;
				
				localStorage.removeItem(key);
				
				refreshStorage();
			}
			
			/**
			 * 清空localStorage的内容
			 */
			function _clear() {
				localStorage.clear();
				
				refreshStorage();
			}
			
			/**
			 * 通过索引 0 获取key
			 * @param {Number} index
			 */
			function getKey(index) {
				let key = localStorage.key(index);
				
				alert(`${index}: ${key}`);
			}
			
			/**
			 * 获取localStorage数组长度
			 */
			function length() {
				let length = localStorage.length;
				
				alert(`length: ${length}`);
			}
			
			/**
			 * LocalStorage刷新并打印在div显示
			 */
			function refreshStorage() {
				let res = ['localStorage.length：' + localStorage.length];
				
				for (let i = localStorage.length; i--;) {
					let key = localStorage.key(i);
					res.push(`${key}: ${localStorage.getItem(key)}`);
				}
				
				$('showStorage').innerHTML = res.join('<br/>');
			}
			
			/**
			 * window默认加载完成调用
			 */
			window.onload = function () {
				refreshStorage();
			}
		</script>
	</head>
	<body>
		<label for="">Key: <input type="text" id="key" /></label>&emsp;&emsp;
		<label for="">Value: <input type="text" id="value"/></label><br />
		
		<button onclick="setItem()">setItem</button>&emsp;
		<button onclick="getItem()">getItem</button>&emsp;
		<button onclick="removeItem()">removeItem</button>&emsp;
		<button onclick="_clear()">clear</button>&emsp;
		<button onclick="getKey(0)">key(0)</button>&emsp;
		<button onclick="length()">length</button>&emsp;
		<button>remainSpace</button>&emsp;
		
		<div id="showStorage"></div>
	</body>
</html>

```