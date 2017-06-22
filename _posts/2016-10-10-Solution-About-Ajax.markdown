---
layout: post
title:  "Ajax cross domain call"
date:   2016-10-10 23:00:00
categories: Algorithm
comments: false
---
* content
{:toc}

题目：请阐述在面对Ajax跨域问题的时候，你是如何处理的，同时对其原理进行阐述。
 <!--more-->

-----

**思考：**
在当今的互联网世界中，Ajax可以说是极其重要的一项技术，该技术的出现，可谓是大大的提高了页面应用程序开发人员的效率，与此同时也极大提升了用户再使用web应用程序的体验。但是在底层的实现机制上，因为安全的考虑，浏览器本身并不支持Ajax直接进行跨域访问。但是在实际的开发过程中，或多或少的，我们都会遇到需要跨域处理的情况。

这里我尽量尝试阐述清除几种实现跨域请求的方式，以及工作原理。和之前的文章一样，依然借鉴了网络上一些前辈的工作，本人也结合自己的实际经验，对其中一些处理方式进行解读。欢迎大家吐槽，拍砖和交流。

-----

jsonp的使用：

```
/*****
方式一：jQuery
jQuery中提供了封装的jsonp的请求,在使用的时候，我门可以像使用正常ajax请求一样。
只不过需要将dataType设置为"jsonp",然后添加一个jsonp参数，其值为"callback"，即可使用。
*****/

$("#something").click(function () {
    $.ajax({
        url: 'http://www.baidu.com/home/somejsonp',
        dataType: "jsonp",
        jsonp: "callback",
        success: function (data) {
            console.log(data)
        }
    })
})

/*****
方式二：手动使用script标签的src获取
如果不借助jQuery直接使用jsonp来手动实现跨域访问，可以使用以下方式
借助于script标签的src来发起请求，采用callback回调函数来接收具体的数据。
如果没有回调函数，则浏览器回直接将返回的数据作为js脚本执行，并出现报错。
如果接口返回的数据是{"city":"北京","ID":"1000"},则发起请求之后回直接打印出结果。
*****/
function CreateScript(src) {//动态创建script标签
    $("<script><//script>").attr("src", src).appendTo("body")
}

function callback(json) {//定义回调函数，处理返回数据
    console.log(json)
}

$("#something").click(function () {//发起请求
    CreateScript("http://www.baidu.com/home/somejsonp?callback=callback")
})

/*****
方式三：使用postMessage
postMessage是h5的一个新功能之一，这里可以使用iframe来完成跨域的数据交换。
*****/


//iframe内部脚本
var message = 'date';<br>if (parent.document.getElementById('iframe')) {
          //捕获iframe
          var iframe = parent.document.getElementById('iframe').contentWindow;
          //发送消息
          parent.postMessage(message, "*");
        }

//iframe外部脚本
window.addEventListener('message',function(e){
      },false);

/*****
方式四：利用最新的HTML5中提供的XMLHTTPREQUEST Level2（及XHR2）实现跨域访问。
IE10以下的浏览器不支持，具体的方式如下：
*****/

//在服务端响应的header新增如下属性即可实现。
header("Access-Control-Allow-Origin:*");
/*星号表示所有的域都可以接受，*/
header("Access-Control-Allow-Methods:GET,POST");


```

-----
深入，在jquery中其实本质的实现也是利用script和回调函数来实现


```
/*****

在最新的jQuery开发版源码中，第9663行---9740行，是对jsonp格式请求的具体处理，具体的源码如下：
*****/

// Detect, normalize options and install callbacks for jsonp requests
jQuery.ajaxPrefilter( "json jsonp", function( s, originalSettings, jqXHR ) {

	var callbackName, overwritten, responseContainer,
		jsonProp = s.jsonp !== false && ( rjsonp.test( s.url ) ?
			"url" :
			typeof s.data === "string" &&
				( s.contentType || "" )
					.indexOf( "application/x-www-form-urlencoded" ) === 0 &&
				rjsonp.test( s.data ) && "data"
		);

	// Handle iff the expected data type is "jsonp" or we have a parameter to set
	if ( jsonProp || s.dataTypes[ 0 ] === "jsonp" ) {

		// Get callback name, remembering preexisting value associated with it
		callbackName = s.jsonpCallback = jQuery.isFunction( s.jsonpCallback ) ?
			s.jsonpCallback() :
			s.jsonpCallback;

		// Insert callback into url or form data
		if ( jsonProp ) {
			s[ jsonProp ] = s[ jsonProp ].replace( rjsonp, "$1" + callbackName );
		} else if ( s.jsonp !== false ) {
			s.url += ( rquery.test( s.url ) ? "&" : "?" ) + s.jsonp + "=" + callbackName;
		}

		// Use data converter to retrieve json after script execution
		s.converters[ "script json" ] = function() {
			if ( !responseContainer ) {
				jQuery.error( callbackName + " was not called" );
			}
			return responseContainer[ 0 ];
		};

		// Force json dataType
		s.dataTypes[ 0 ] = "json";

		// Install callback
		overwritten = window[ callbackName ];
		window[ callbackName ] = function() {
			responseContainer = arguments;
		};

		// Clean-up function (fires after converters)
		jqXHR.always( function() {

			// If previous value didn't exist - remove it
			if ( overwritten === undefined ) {
				jQuery( window ).removeProp( callbackName );

			// Otherwise restore preexisting value
			} else {
				window[ callbackName ] = overwritten;
			}

			// Save back as free
			if ( s[ callbackName ] ) {

				// Make sure that re-using the options doesn't screw things around
				s.jsonpCallback = originalSettings.jsonpCallback;

				// Save the callback name for future use
				oldCallbacks.push( callbackName );
			}

			// Call if it was a function and we have a response
			if ( responseContainer && jQuery.isFunction( overwritten ) ) {
				overwritten( responseContainer[ 0 ] );
			}

			responseContainer = overwritten = undefined;
		} );

		// Delegate to script
		return "script";
	}
} );
```
