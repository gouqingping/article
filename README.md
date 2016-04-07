前端工程的日益更新，导致很多工程师忽略掉IE，所以很多的工程师认为IE内核的浏览器是个大坑，非常不愿意去常与IE的工程，尤其是低版本IE的兼容性，一般新工程师都会非常排斥IE内核，而年长的工程师也会觉得兼容IE比较麻烦。

其实我也觉得非常麻烦，开始的时候都用

		_XXX:XXX; /* IE6支持 */
		*XXX:XXX; /* IE6、IE7支持 */
		*+XXX:XXX; /* IE7支持 */
		XXX:XXX\9; /* IE6、IE7、IE8支持 */
		XXX:XXX\0; /* IE8支持 */

		创建条件样式表，并在HTML中body里添加相应的class类名：

		<!–[if IE6]–>
		<!–[if IE7]–>
		<!–[if IE8]–>
		<!–[if IE9]–>
		<!–[if !IE]–>

类似于这样的方式来解决一些工程上面的一些问题，但是还是感觉很累，相当于每一块都要独立写一个CSS，另外的就是CSS3的部分内容还不会兼容，但是面对于现在人而言，着重着美感，这让前端攻城狮们非常头大，于是，我浏览各大网站看看他们是如何解决这一问题。

有一部分是禁止IE低版本显示，弹出一个弹出层，提示下载其他浏览器，也有一部分是直接给出一段提示让用其他浏览器打开……，对于我们而言用户才是上帝，所以，我们也不能排除有一部分是IE的脑残粉。

那么让我们看看他们是如何解决这些问题的：

Github ：

	<meta http-equiv="X-UA-Compatible" content="IE=edge">


腾讯云 ：

	<meta http-equiv="X-UA-Compatible" content="IE=edge">

	<!--[if IE 8 ]>
		<body class="ie8">
	<![endif]-->

	<!--[if lt IE 8]>
		<link href="//imgcache.qq.com/qcloud/mc/css/ie67sucks.min.css" rel="stylesheet" />
		<script src="//imgcache.qq.com/qcloud/mc/ie67sucks.min.js?v=20150331"></script>
	<![endif]-->

百度 ：

	<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">

npm ：
	
	<!--[if lt IE 9]>
    	<script src="//cdnjs.cloudflare.com/ajax/libs/html5shiv/3.7.2/html5shiv.min.js"></script>
	<![endif]-->

GitLab ：
	
	<!--[if lt IE 9]>
		<script src="http://html5shim.googlecode.com/svn/trunk/html5.js"></script>
	<![endif]-->

知乎 ：

	<meta http-equiv="X-UA-Compatible" content="IE=edge,chrome=1">
	<!--[if lt IE 9]>
	<script src="https://static.zhihu.com/static/components/respond/dest/respond.min.js"></script>
	<link href="https://static.zhihu.com/static/components/respond/cross-domain/respond-proxy.html" id="respond-proxy" rel="respond-proxy" />
	<script src="/static/components/respond/cross-domain/respond.proxy.js"></script>
	<![endif]-->


…………

大多部分都引用了 
<meta http-equiv="X-UA-Compatible" content="IE=edge">
<script src="http://html5shim.googlecode.com/svn/trunk/html5.js"></script>

那么说明这两个东西都可以解决IE的问题。

另外，我再给大家一串解决字体图标兼容IE的JavaScript代码

function(){

	var patterns = {
		text: /^['"]?(.+?)["']?$/,
		url: /^url\(["']?(.+?)['"]?\)$/
	};

	function clean(content) {
		if(content && content.length) {
			var text = content.match(patterns.text)[1],
				url = text.match(patterns.url);
			return url ? '<img src="' + url[1] + '" />': text;
		}
	}

	function inject(prop, elem, content) {
		if(prop != 'after') prop = 'before';
		if(content = clean(elem.currentStyle[prop])) {
			$(elem)[prop == 'before' ? 'prepend' : 'append'](
				$(document.createElement('span')).addClass(prop).html(content)
			);
		}
	}

	$.pseudo = function(elem) {
		inject('before', elem);
		inject('after', elem);
		elem.runtimeStyle.behavior = null;
	};
	
	if(document.createStyleSheet) {
		var o = document.createStyleSheet(null, 0);
		o.addRule('.dummy','display: static;');
		o.cssText = 'html, head, head *, body, *.before, *.after, *.before *, *.after * { behavior: none; } * { behavior: expression($.pseudo(this)); }';
	}

};


各浏览器CSS兼容代码：

Firefox

	@-moz-document url-prefix() { 
		.css { 
			color:lime; 
		} 
	}

	支持Firefox的还有几种写法：

	/* 支持所有firefox版本 */ 

		#selector[id=selector] { 
			color: white; 
		} 

	或者： 

	@-moz-document url-prefix() { 
		.css { 
			color: white; 
		} 
	} 

	/* 支持所有Gecko内核的浏览器 (包括Firefox) */

	 *>.css { 
	 	color: white; 
	 }

Webkit枘核(chrome and safari)

	@media screen and (-webkit-min-device-pixel-ratio:0) { 
		.css { 
			color: white; 
		} 
	}

	上面写法主要是针对Webkit内核的浏览器，如Google Chrome 和 Safari浏览器

Opera

	html:first-child>body .css {
		color:white;
	} 

	或者： 

	@media all and (min-width:0) { 
		.css {
			color: white;
		} 
	} 

	或者： 

	@media all and (-webkit-min-device-pixel-ratio:10000), not all and (-webkit-min-device-pixel-ratio:0) { 
		head~body .css { 
			color: white; 
		} 
	}


IE9

	:root .css {
		color: white9;
	}

	IE9+都能够兼容

IE8

	.css {
		color: white/;
	}

	 或者： 

	@media ?screen{ 
		.css {
			color: white;
		} 
	}

	IE8+都能够兼容


IE7

	*+html .css{
		color:white;
	} 

	或 

	*:first-child+html .css {
		color:white;
	}

	只兼容IE7

IE6

	.css {
		_property/**/:/**/white;
	} 

	或者： 

	.css {
		_property: white;
	} 

	或者：

	*html .css {
		color: white;
	}

	只兼容IE6





Update increasingly front-end engineering, resulting in many engineers ignored IE, so many engineers believe IE browser kernel is a pit, and often very reluctant to go to IE's engineering, in particular the low version of IE compatibility, most of the new engineers will very exclusive IE kernel, while older engineers will find compatible IE is too much trouble.

In fact, I find it very troublesome, beginning with all

	_XXX: XXX; / * IE6 support * /
	* XXX: XXX; / * IE6, IE7 support * /
	* + XXX: XXX; / * IE7 * Support /
	XXX: XXX \ 9; / * IE6, IE7, IE8 support * /
	XXX: XXX \ 0; / * IE8 support * /

Creating conditions stylesheet, and add the appropriate class name of the class in the HTML body inside:

	<-! [If IE6] ->
	<-! [If IE7] ->
	<-! [If IE8] ->
	<-! [If IE9] ->
	<-! [! If IE] ->

Like in such a way to solve some engineering problems above, but still feel very tired, each piece must be equivalent to an independent write a CSS, the other part is also not compatible with CSS3, but now people in the face , focusing on the beauty, which makes the front end of the siege lion heads are very large, so I browse the site to see how they solve this problem.

Part is prohibited IE version lower display, pop up a pop-up layer, prompted to download other browsers, but also in part directly give some tips to get open ...... for us, God is the users use other browsers, so we IE can not be excluded in part of the brain residual powder.

So let's see how they solve these problems:

Github:

	<Meta http-equiv = "X-UA-Compatible" content = "IE = edge">


Tencent says:

	<Meta http-equiv = "X-UA-Compatible" content = "IE = edge">

	<-! [If IE 8]>
	<Body class = "ie8">
	<! [Endif] ->

	<-! [If lt IE 8]>
	<Link href = "// imgcache.qq.com/qcloud/mc/css/ie67sucks.min.css" rel = "stylesheet" />
	<Script src = "// imgcache.qq.com/qcloud/mc/ie67sucks.min.js?v=20150331"> </ script>
	<! [Endif] ->

Baidu:

	<Meta http-equiv = "X-UA-Compatible" content = "IE = edge, chrome = 1">

npm:

	<-! [If lt IE 9]>
	    <Script src = "// cdnjs.cloudflare.com/ajax/libs/html5shiv/3.7.2/html5shiv.min.js"> </ script>
	<! [Endif] ->

GitLab:

	<-! [If lt IE 9]>
	<Script src = "http://html5shim.googlecode.com/svn/trunk/html5.js"> </ script>
	<! [Endif] ->

Know almost :

	<Meta http-equiv = "X-UA-Compatible" content = "IE = edge, chrome = 1">
	<-! [If lt IE 9]>
	<Script src = "https://static.zhihu.com/static/components/respond/dest/respond.min.js"> </ script>
	<Link href = "https://static.zhihu.com/static/components/respond/cross-domain/respond-proxy.html" id = "respond-proxy" rel = "respond-proxy" />
	<Script src = "/ static / components / respond / cross-domain / respond.proxy.js"> </ script>
	<! [Endif] ->


............

Most parts are quoted
<Meta http-equiv = "X-UA-Compatible" content = "IE = edge">
<Script src = "http://html5shim.googlecode.com/svn/trunk/html5.js"> </ script>

Then that these two things can be resolved the problem of IE.

In addition, I give you a bunch troubleshoot font icons compatible with IE's JavaScript code

function () {

	var patterns = {
		text: /^['"]?(.+?)["']?$/,
		url: /^url\(["']?(.+?)['"]?\)$/
	};

	function clean (content) {
		if (content && content.length) {
			var text = content.match (patterns.text) [1],
			url = text.match (patterns.url);
			? Return url '<img src = "' + url [1] + '" />': text;
		}
	}

	function inject (prop, elem, content) {
		if (! prop = 'after') prop = 'before';
		if (content = clean (elem.currentStyle [prop])) {
			$ (Elem) [? Prop == 'before' 'prepend': 'append'] (
			$ (Document.createElement ( 'span')). ​​AddClass (prop) .html (content));
		}
	}

	$ .pseudo = Function (elem) {
		inject ( 'before', elem);
		inject ( 'after', elem);
		elem.runtimeStyle.behavior = null;
	};

	if (document.createStyleSheet) {
		var o = document.createStyleSheet (null, 0);
		o.addRule ( '. dummy', 'display: static;');
		o.cssText = 'html, head, head *, body, * .before, * .after, * .before *, * .after * {behavior: none;} * {behavior: expression (. $ pseudo (this)) ;} ';
	}

};


Each browser compatible CSS Code:

Firefox

	@ -moz-Document url-prefix () {
		.css {
			color: lime;
		}
	}

	There are several support Firefox's wording:

	/ * Supports all versions of firefox * /

	#selector [id = selector] {
		color: white;
	}

	Or:

	@ -moz-Document url-prefix () {
		.css {
			color: white;
		}
	}

	/ * Supports all Gecko-browsers (including Firefox) * /

	*>. Css {
		color: white;
	}

Webkit core tenon (chrome and safari)

	@media screen and (-webkit-min-device-pixel-ratio: 0) {
		.css {
			color: white;
		}
	}

	The above written mainly for Webkit core browser, such as Google Chrome and Safari

	Opera

	html: first-child> body .css {
		color: white;
	}

	Or:

	@media all and (min-width: 0) {
		.css {
			color: white;
		}
	}

	Or:

	@media all and (-webkit-min-device-pixel-ratio: 10000), not all and (-webkit-min-device-pixel-ratio: 0) {
		head ~ body .css {
			color: white;
		}
	}


IE9

	: Root .css {
		color: white9;
	}

	They are compatible with IE9 +

IE8

	.css {
		color: white /;
	}

	Or:

	@media? screen {
		.css {
			color: white;
		}
	}

	They are compatible with IE8 +


IE7

	* + Html .css {
		color: white;
	}

	Or

	*: First-child + html .css {
		color: white;
	}

	Only compatible with IE7

IE6

	.css {
		_property / ** /: / ** / white;
	}

	Or:

	.css {
		_property: white;
	}

	Or:

	* Html .css {
		color: white;
	}

	Only compatible with IE6
