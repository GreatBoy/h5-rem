---
title: 移动端h5适配
date: 2015-01-13 15:04:33
---


>未来肯定是移动端的天下，接下来谈谈h5移动端的布局问题，目前有三种
1、百分比布局 ----->有点兼容性好，缺点布局的时候需要计算比较麻烦，不过可以采取工具或脚本实现。
2、flex布局  ----->目前看来兼容还是有一定问题，尤其是低版本andriod
3、rem布局   ----->目前比较推崇的布局方案，感兴趣的可以看看[淘宝的flexible](http://www.cocoachina.com/webapp/20150617/12190.html)

<!-- more -->


### 我的布局方案

#### 一、定宽布局

*我先解释一下原理，由于以前的机器是320**XXX的分辨率，所以我采取的320布局，图片当然要做2倍图或者3倍图，就是说320px是我们的布局基础。例如iphone5是640*1136px，但是我们用js获取的实际宽度是320*568，也就是说屏幕相当于一个点，由两个像素，如果这个点由显示的像素越多，屏幕就约清晰。

例如一个新的设计图是640*1136的iphone5，其实机子的实际宽度是320*568。我们可以按照320宽度进行布局，也就是说所有的图片我们需要用宽度或者背景宽度来控制。这样即可以采用ihpone 5可以采用，2倍图标，iphone6 3倍图标。

这样我们其实是把宽度写死了，也就是说在机子上的宽度显示的永远是320px的。这里我们就要想法进行缩放去适配各种不同机子，也就是rem方式。rem其实是相对于根节点的font-size大小进行缩放，也就是默认情况下1rem=16px。那么我们的320px=20rem，也就是布局的时候我们写20rem。这时候我们需要把html的font-size计算成需要的大小，就可以适配机器了。

代码如下
```
<!DOCTYPE html>
<html>
    <head>
        <meta http-equiv="content-type" content="text/html; charset=UTF-8"/>
        <meta name="viewport" id="viewport" content="width=device-width,initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no">
        <meta name="format-detection" content="telephone=no"/>
        <title>GreatBoy</title>
        <style>
            /*-----css Reset end-----*/
            body,h1,h2,h3,h4,h5,h6,p,ul,ol,form{
                margin:0;
                padding:0px;
            }
            html{
                display: none;
            }
         
            .nav img{
                width:10rem;
            }

        </style>
    </head>
    <body>
        <div class="nav"><img src="http://p1.jmstatic.com/banner/75/mobile_app/24326_0.jpg"/></div>
        <div id="container">
            <div id="user">用户信息<span id="userinfo"></span></div>
        </div>
        <script>
        (function(win) {
            var doc = win.document,
                scale = 16,
                $body = doc.getElementsByTagName('body')[0],
                $html = doc.getElementsByTagName('html')[0],
                windowWidth = doc.documentElement && doc.documentElement.clientWidth || doc.body.clientWidth || win.innerWidth,
                windowHeight = document.documentElement && document.documentElement.clientHeight || documentElement.body.clientHeight || window.innerHeight,
                deviceAgent = navigator.userAgent.toLowerCase();
            if ( deviceAgent.match( /(iphone|ipod|ipad|android|windows\s*phone|symbianos)/ ) ) {
                try{
                    if ( window.localStorage && window.localStorage.getItem('scale_greatboy') ) {
                        scale = window.localStorage.getItem('scale_greatboy');
                    } else {
                        scale = parseFloat(windowWidth * 16 / 320);
                        if (windowHeight > windowWidth) {
                            window.localStorage && window.localStorage.setItem('scale_greatboy', scale);
                        }
                    }
                }catch(e){

                }
                if (deviceAgent.match(/android\s*2.3/) && deviceAgent.match(/micromessenger/)) {
                    scale = 16;
                }
                $html.style.fontSize = scale + 'px';
                $html.style.display = 'block';
            } else {
                window.onresize = function(){
                    windowWidth = doc.documentElement && doc.documentElement.clientWidth || doc.body.clientWidth || win.innerWidth;
                    scale = parseFloat(windowWidth * 16 / 320);
                    $html.style.fontSize = scale + 'px';
                };
                scale = parseFloat(windowWidth * 16 / 320);
                $html.style.fontSize = scale + 'px';
                $html.style.display = 'block';
            }
            $body.style.width = '20rem';
        })(window);
 </script>
    </body>
</html>
```


这种布局目前已经可以适配大部分手机，除非是不支持rem的手机，但是还会有问题，就是16px = 1rem，这样每次我们布局的时候量的宽度都是px的，还的转换成rem，这里其实可以采取很多方案，例如发布的时候编译，以及用css的一些插件来实现。当时我们采取的是发布的时候用node来写了一个插件做这个事情。fis的插件如



#### 二、定宽布局的改良

说起改良后的方法还的感谢猫眼的前端技术经理，有一次面试跟猫眼的前端技术经理聊rem布局的时候聊出来的，他给的建议是如果写成320=20rem，这种方式不好计算，那么不如直接写320px = 3.2rem，回头一想其实也是可以的，我们只需要在根节点的font-size做处理就好。   320=20rem其实是1rem=16px,那么如果是320px = 3.2rem，那么其实相当于1rem = 100px，也就是说需要把1rem=16px转换成1rem=100px而已。于似乎出现下面的代码：




``` javascript
<!DOCTYPE html>
<html>
    <head>
        <meta http-equiv="content-type" content="text/html; charset=UTF-8"/>
        <meta name="viewport" id="viewport" content="width=device-width,initial-scale=1.0, minimum-scale=1.0, maximum-scale=1.0, user-scalable=no">
        <meta name="format-detection" content="telephone=no"/>
        <title>GreatBoy</title>
        <style>
            /*-----css Reset end-----*/
            body,h1,h2,h3,h4,h5,h6,p,ul,ol,form{
                margin:0;
                padding:0px;
            }
            html{
/*                display: none;*/
            }
            body{
                font-size: 14px;
            }
            .nav img{
                width: 100%;
            }
        </style>
    </head>
    <body>
        <div class="nav"><img src="http://p1.jmstatic.com/banner/75/mobile_app/24326_0.jpg"/></div>
        <div id="container">
            <div id="user">用户信息<span id="userinfo"></span></div>
        </div>
        <script>
	        (function(win) {
	            var doc = win.document,
	                scale = 16,
	                $body = doc.getElementsByTagName('body')[0],
	                $html = doc.getElementsByTagName('html')[0],
	                windowWidth = doc.documentElement && doc.documentElement.clientWidth || doc.body && doc.body.clientWidth || win.innerWidth,
	                windowHeight = doc.documentElement && doc.documentElement.clientHeight || doc.body && doc.body.clientHeight || window.innerHeight,
	                deviceAgent = navigator.userAgent.toLowerCase();
	            if ( deviceAgent.match( /(iphone|ipod|ipad|android|windows\s*phone|symbianos)/ ) ) {
	                scale = parseFloat(windowWidth * 16 / (16*3.2));
	                if (deviceAgent.match(/android\s*2.3/) && deviceAgent.match(/micromessenger/)) {
	                    scale = 16;
	                }
	                $html.style.fontSize = scale + 'px';
	                $html.style.display = 'block';
	            } else {
	                window.onresize = function(){
	                    windowWidth = doc.documentElement && doc.documentElement.clientWidth || doc.body.clientWidth || win.innerWidth;
	                    scale = parseFloat(windowWidth * 16 / (16*3.2));
	                    $html.style.fontSize = scale + 'px';
	                };

	                scale = parseFloat(windowWidth * 16 / (16*3.2));
	                if(scale > 150){
	                    scale = 150;
	                }
	                $html.style.fontSize = scale + 'px';
	                $html.style.display = 'block';
	            }
	            $body.style.width = '3.2rem';
	            $body.style.margin = '0px auto';
	        })(window);
	 	</script>
    </body>
</html>

```


为了移动端布局，我也尝试了很多方法，目前这种方法比较好，可以适配大多数手机，但是缺点就是ipad上显示比较悲剧，因为是完全缩放的，所以我目前写死了缩放系数。也就是在pc上的缩放系数定死了。[github](https://github.com/GreatBoy/h5-rem)




