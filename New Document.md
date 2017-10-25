## 论ajax与jsonp:
####1、ajax的概念
- AJAX=Asynchronous javascript And XML(AJAX=异步javascript和xml);
- AJAX是一种无需重新加载整个网页的情况下，能更新部分网页的技术。
####2、工作原理
- ajax就是js通过一个网站去加载数据，这个过程通常是用户不可见的。
- 传统的网页（不适用ajax）如果需要更新内容，必须重新加载整个网页。
####3、关于同步与异步
- 同步需要等待返回结果才能继续，异步不必等待，一般需要监听异步的结果。
- 同步是在一条直线上的队列，异步不在一个队列上，各走各的。
 
#####列如：  
1. 添加购物车问题。采取同步方式，每加入一项购物车，则需要等待页面重新加载后再执行其他操作。
1. 而使用异步方式，则只需监听，无需等待即可执行其他操作。相对而言，异步加载优势更大，ajax优势由此可见。
	
#####（1）创建XMLHttpRequest对象

		if(window.XMLHttpRequest){
			var xhr=new XMLHttpRequest();
		}else{
			var xhr=new ActionXObject("Microsoft.XMLHTTP");
		}

#####(2)打开服务器的链接

		open(method,url,asyn);
		参数：
		method:string,请求的类型get或post
		url：string，文件在服务器上的位置
		asyn：Boolean，true（异步）或false（同步）
		同步需要等待返回结果才能继续，异步不必等待

#####（3）发送给服务器

		xhr.send()将请求发送到服务器（get请求）
		xhr.send(string)仅用于post请求

#####（4）检测服务器的请求状态

1. onreadystatechange事件（相应就绪状态）
1. readyState和status属性存有XMLHttpRequest的状态
1. readyState改变时就会触发onreadystatechange事件
2. readyState**从0到4发生变化**:

   >0：请求未初始化

   >1：服务器连接已建立

   >2：请求已接收

   >3：请求处理中

   >4：请求已完成

  status等于200：“OK”

  等于404：未找到页面

**当readyStatus为4并且status为200时，表示相应已就绪。**

####4、XMLHttpRequest对象的重要性

#####如需获得来自服务器的响应，请使用XMLHttpRequest对象的：
1. responseText或responseXML属性
1. responseText获得字符串形式的响应数据
1. responseText获得XML形式的响应数据

注：如来自服务器的响应并非XML，用responseText属性返回字符串形式响应，可以这样使用：

	Div.innerHTML=xhr.responseText;
	
####5、关于ajax请求方式get和post的区别：

GET:更常用，更方便，性能好，明文发送数据，没有post安全；数据传输大小有限制，数据通过URL传递，但是URL有一定的长度限制。

POST:使用相对较少；性能只有get的1/3左右；比get稍微安全一点；没有数据大小限制；

####6、关于跨域

跨域可以简单的理解为从一个域名访问另一个域名，处于安全考虑，浏览器不允许这么做；

备注：img、script、iframe等元素的src属性可以直接跨域请求资源


1.同源：

	同源策略：
	    一种约定，它是浏览器最核心也最基本的安全功能，如果缺少了同源策略，则浏览器的正常功能可能都会受到影响。
	  可以说Web是构建在同源策略基础之上的，浏览器只是针对同源策略的一种实现
	
	源：协议、端口、域名
	同源：同协议、端口、域名
	
	协议：
	    http、https、file、ftp...
	    http://localhost/2017-10-25/5_%E4%B8%8A%E4%BC%A02.html
	    file:///C:/xampp/php/www/2017-10-25/6_%E8%B7%A8%E5%9F%9F.html
	
	端口:
	    专门提供某项服务的"窗口"
	    
	域名:IP的别名（方便记）
	    www.baidu.com
    	www.miaov.com

2.跨域（跨源）
   
	 不同协议、端口、域名


3.解决跨域：
   
	 通过 新版本的XMLHttpRequest  + 服务器开权限
        'Access-Control-Allow-Origin:*'

    
    服务器代理：
        服务器文件能访问别的域下的资源，如果服务器文件与自己的页面
        是同源，我就能访问别的域下的资源了。	

####7、jsonp

#####jsonp = JSON + Padding

        函数名 + 括号  括号中带有数据
        fn([])
        fn({})

        ajax的数据是没有函数名的

        img src="xxx"
        link href = "xx"
        script src=""

#####jsonp原理:

- 首先，jsonp数据格式是函数名+括号的
- 然后，全局需要定义一个与后端函数名一致的函数
- 最后，当需要数据的时候，创建一个script标签，进行请求，获取数据(按需加载)。

 <需要数据的时候执行一个函数，函数内有想要的数据，提前约定好这个函数名，后端会把数据传到函数中，实现按需加载。>  


	jsonp的实现核心就是利用script标签的跨域能力：动态的生成一个script标签，把json的url赋值给script的src属性
	
	然后再把这个script标签插入dom中。。。
	
	<script src='http://libs.baidu.com/jquery/2.0.0/jquery.min.js'></script>
	
	<script>
		
		var s=document.createElement('script');
		
		s.src='http://localhost:8080/a.json';
	
		document.body.appendChild(s);
	</script>
	
	我们创建了一个script标签，而标签内包裹的内容正是需要的json数据，但是报错；
	
	原因是因为json数据并不是合法的js语句，把上面的json数据放在一个回调函数中视最简单的方法：

	<script src='http://libs.baidu.com/jquery/2.0.0/jquery.min.js'></script>
	
	<script>
		
		function jsonpcallback(json){
		
			console.log(json);
		
		}

		var s=document.createElement('script');
		
		s.src='http://localhost:8080/a.json';
	
		document.body.appendChild(s);
	</script>

###ajax和jsonp的区别：

####相同点：
都是请求一个url

####不同点：
1.ajax的核心是通过XMLHttpRequest获取内容

2.jsonp的核心则是动态添加<script>标签来调用服务器提供js脚本

	
		

 

 


	

