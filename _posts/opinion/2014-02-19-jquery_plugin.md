---
layout: post
title: jQuery插件的写法(转载)
category: opinion
description: jQuery插件的写法(备忘)。
---

##									jQuery插件的写法
1、概述

先看看html代码

```html
	<ul id="catagory">
    <li><a href="#">jQuery</a></li> 
    <li><a href="#">Asp.net</a></li> 
    <li><a href="#">Sql Server</a></li> 
    <li><a href="#">CSS</a></li> 
	</ul> 
```	
	
比如我们要实现当光标移动到a标签上时，a标签向右移动一段距离，离开时a位置恢复。实现方法如下：

```javascript
	$(document).ready(function() { 
    	$("#catagory a").hover(function() { 
        	$(this).animate({ paddingLeft: "20px" }, { queue: false, duration: 500 }); 
    		}, function() { 
        		$(this).animate({ paddingLeft: "0" }, { queue: true, duration: 500 }); 
    	}); 
	}); 
```	
现在我们将这个方法进行扩展，写成jQuery插件的形式，以后也能在别的项目中使用，并且可以方便的更改一些属性值，现在我们来看看jQuery插件的写法。

2、jQuery插件的结构

下边这个结构应该是编写jQuery插件的一个比较好的结构，我将原作者的一些注释进行了相应的翻译。

//为避免冲突，将我们的方法用一个匿名方法包裹起来 
```javascript
(function($) { 
    //扩展这个方法到jquery 
    $.fn.extend({ 
 
        //插件名字 
        pluginname: function() { 
 
            //遍历匹配元素的集合 
            return this.each(function() { 
 
                //在这里编写相应代码进行处理  
 
            }); 
        } 
    }); 
 //传递jQuery到方法中，这样我们可以使用任何javascript中的变量来代替"$"       
})(jQuery);  
```	

接下来，我们给插件中加入一些可以改变的属性，这样用户可以根据自己的需要来做一些更改。同时，我们应提供相应的默认值。
```javascript
(function($){   
   
    $.fn.extend({    
           
    //将可选择的变量传递给方法 
        pluginname: function(options) {   
   
   
            //设置默认值并用逗号隔开 
            var defaults = {   
                padding: 20,   
                mouseOverColor : '#000000',   
                mouseOutColor : '#ffffff'   
            }   
                   
            var options =  $.extend(defaults, options);   
   
            return this.each(function() {   
                var o = options;   
                   
                //这里编写相应代码  
                //可以像下边这样获取变量值  
                alert(o.padding);   
               
            });   
        }   
    });   
       
})(jQuery);   
```	


3、实现jQuery插件

```javascript
(function ($) { 
    $.fn.extend({ 
        //插件名称 - paddingList 
        paddingList: function (options) { 
 
            //参数和默认值 
            var defaults = { 
                animatePadding: 10, 
                hoverColor: "Black" 
            }; 
 
            var options = $.extend(defaults, options); 
 
            return this.each(function () { 
                var o = options; 
 
                //将元素集合赋给变量 本例中是 ul对象  
                var obj = $(this); 
 
                //得到ul中的a对象 
                var items = $("li a", obj); 
 
                //添加hover()事件到a 
                items.hover(function () { 
                    $(this).css("color", o.hoverColor); 
                    //queue false表示不添加到动画队列中 
                    $(this).animate({ paddingLeft: o.animatePadding }, { queue: false, duration: 300 }); 
 
                }, function () { 
                    $(this).css("color", ""); 
                    $(this).animate({ paddingLeft: "0" }, { queue: true, duration: 300 }); 
                }); 
 
            }); 
        } 
    }); 
})(jQuery); 
```

最后，使用插件的方法如下：

```javascript
//使用插件 
$(document).ready(function() { 
    $("#catagory").paddingList({ animatePadding: 30, hoverColor: "Red" }); 
}); 
```

#####转载于：<http://developer.51cto.com/art/201312/420210.htm>




