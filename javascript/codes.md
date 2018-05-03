# 代码片段

> 其他链接：[30 秒就能理解的 JavaScript 代码片段](http://www.css88.com/30-seconds-of-code/)

[TOC]

## 验证相关

### is

``` js
/**
 * 判断是否为app内
 * @returns {Boolean}
 */
function isApp() {
    var href = window.location.href;
    var re = href.indexOf('_app');
    if (navigator.userAgent.indexOf('miyabaobei_') > -1 || re !== -1) {
        return true;
    }
    return false;
}

/**
 * 判断微信浏览器
 * @returns {Boolean}
 */
function isWeiXin() {
    var ua = window.navigator.userAgent.toLowerCase();
    if (ua.match(/MicroMessenger/i) == 'micromessenger') {
        return true;
    } else {
        return false;
    }
}
```

### 验证css属性在浏览器中是否支持

``` js
var supportsPointerEvents = (function () {
    var dummy = document.createElement('_');
    if (!('pointerEvents' in dummy.style)) return false;
    dummy.style.pointerEvents = 'auto';
    dummy.style.pointerEvents = 'x';
    document.body.appendChild(dummy);
    var r = getComputedStyle(dummy).pointerEvents === 'auto';
    document.body.removeChild(dummy);
    return r;
})();
```

### 浏览器判断

``` js
function parseUA() {
    var u = navigator.userAgent;
    var u2 = navigator.userAgent.toLowerCase();
    return { //移动终端浏览器版本信息
        trident: u.indexOf('Trident') > -1,
        //IE内核
        presto: u.indexOf('Presto') > -1,
        //opera内核
        webKit: u.indexOf('AppleWebKit') > -1,
        //苹果、谷歌内核
        gecko: u.indexOf('Gecko') > -1 && u.indexOf('KHTML') == -1,
        //火狐内核
        mobile: !!u.match(/AppleWebKit.*Mobile.*/),
        //是否为移动终端
        ios: !!u.match(/\(i[^;]+;( U;)? CPU.+Mac OS X/),
        //ios终端
        android: u.indexOf('Android') > -1 || u.indexOf('Linux') > -1,
        //android终端或uc浏览器
        iPhone: u.indexOf('iPhone') > -1,
        //是否为iPhone或者QQHD浏览器
        iPad: u.indexOf('iPad') > -1,
        //是否iPad
        webApp: u.indexOf('Safari') == -1,
        //是否web应该程序，没有头部与底部
        iosv: u.substr(u.indexOf('iPhone OS') + 9, 3),
        weixin: u2.match(/MicroMessenger/i) == "micromessenger",
        ali: u.indexOf('AliApp') > -1,
    };
}
var ua = parseUA();

function identifyUA() {
    var userAgent = navigator.userAgent.toLowerCase();
    var platform = '';
    if (userAgent == null || userAgent == '') {
        platform = 'web';
    } else {
        if (userAgent.indexOf("android") != -1) {
            platform = 'android';
        } else if (userAgent.indexOf("ios") != -1 || userAgent.indexOf("iphone") != -1) {
            platform = 'ios';
        } else if (userAgent.indexOf("ipad") != -1) {
            platform = 'ios';
        } else if (userAgent.indexOf("windows phone") != -1) {
            platform = 'windowsphone';
        } else {
            platform = 'web';
        }
    }
    return platform;
}
```

### 身份证验证

``` js
function chechCHNCardId(sNo) {
    if (!this.regExpTest(sNo, /^[0-9]{17}[X0-9]$/)) {
        return false;
    }
    sNo = sNo.toString();

    var a, b, c;
    a = parseInt(sNo.substr(0, 1)) * 7 + parseInt(sNo.substr(1, 1)) * 9 + parseInt(sNo.substr(2, 1)) * 10;
    a = a + parseInt(sNo.substr(3, 1)) * 5 + parseInt(sNo.substr(4, 1)) * 8 + parseInt(sNo.substr(5, 1)) * 4;
    a = a + parseInt(sNo.substr(6, 1)) * 2 + parseInt(sNo.substr(7, 1)) * 1 + parseInt(sNo.substr(8, 1)) * 6;
    a = a + parseInt(sNo.substr(9, 1)) * 3 + parseInt(sNo.substr(10, 1)) * 7 + parseInt(sNo.substr(11, 1)) * 9;
    a = a + parseInt(sNo.substr(12, 1)) * 10 + parseInt(sNo.substr(13, 1)) * 5 + parseInt(sNo.substr(14, 1)) * 8;
    a = a + parseInt(sNo.substr(15, 1)) * 4 + parseInt(sNo.substr(16, 1)) * 2;
    b = a % 11;

    if (b == 2) {
        c = sNo.substr(17, 1).toUpperCase();
    } else {
        c = parseInt(sNo.substr(17, 1));
    }

    switch (b) {
        case 0:
            if (c != 1) {
                return false;
            }
            break;
        case 1:
            if (c != 0) {
                return false;
            }
            break;
        case 2:
            if (c != "X") {
                return false;
            }
            break;
        case 3:
            if (c != 9) {
                return false;
            }
            break;
        case 4:
            if (c != 8) {
                return false;
            }
            break;
        case 5:
            if (c != 7) {
                return false;
            }
            break;
        case 6:
            if (c != 6) {
                return false;
            }
            break;
        case 7:
            if (c != 5) {
                return false;
            }
            break;
        case 8:
            if (c != 4) {
                return false;
            }
            break;
        case 9:
            if (c != 3) {
                return false;
            }
            break;
        case 10:
            if (c != 2) {
                return false;
            };
    }
    return true;
}
```

## Polyfill

### test in

``` js
// test Event
var touch = "ontouchstart" in document ? 'touchstart' : 'mousedown';
```

### img error

``` html
<img src="xx.jpg" onerror="this.src='placeholder.jpg';" alt="">
```

### css前缀相关

``` js
// 获取到一个CSSStyleDeclaration对象，它是一个CSS属性键值对的集合。
var divStyle = document.createElement('div').style;

/**
 * 将css前缀转为驼峰规则，如：-webkit-box-pack -> WebkitBoxPack
 * @param {string} str css写法前缀
 */
function camelCase(str) {
    return (str + '').replace(/^-ms-/, 'ms-').replace(/-([a-z]|[0-9])/ig, function(all, letter) {
        return (letter + '').toUpperCase();
    })
}

/**
 * 将驼峰写法前缀转换为 -xx- 写法
 * @param {string} str 驼峰式写法
 */
function unCamelCase(str) {
    return str.replace(/([A-Z]|^ms)/g, "-$1").toLowerCase();
}

/**
 * 返回当前浏览器的前缀，如：chrome下返回 -webkit-  字符串
 */
function cssVender() {
    var ds = divStyle,
        cases = ['-webkit-', '-moz-', '-ms-', '-o-', ''],
        i = 0;
    do {
        if (camelCase(cases[i] + 'transform') in ds) {
            return cases[i];
        }
    } while (++i < cases.length);
    return '';
}

/**
 * 返回浏览器支持的带前缀属性，如：chrome下，box-pack -> -webkit-box-pack
 * @param {string} property 传入的css属性名
 */
function fixCSS(property) {
    var ds = divStyle,
        pre = cssVender();
    return (camelCase(property) in ds) && property || (camelCase(pre + property) in ds) && pre + property || property;
}

/**
 * 返回js特性的前缀，如：chrome下，box-pack -> WebkitBoxPack
 * @param {string} property 传入css属性名
 */
function isCSS(property) {
    var name = camelCase(fixCSS(property));
    return (name in divStyle) && name || '';
}
```

## 方法

### ajax

``` js
/* 封装ajax函数
 * @param {string}opt.type http连接的方式，包括POST和GET两种方式
 * @param {string}opt.url 发送请求的url
 * @param {boolean}opt.async 是否为异步请求，true为异步的，false为同步的
 * @param {object}opt.data 发送的参数，格式为对象类型
 * @param {function}opt.success ajax发送并接收成功调用的回调函数
 */
function ajax(opt) {
    opt = opt || {};
    opt.type = opt.type.toUpperCase() || 'POST';
    opt.url = opt.url || '';
    opt.async = opt.async || true;
    opt.data = opt.data || null;
    opt.success = opt.success || function () { };
    var xmlHttp = null;
    if (XMLHttpRequest) {
        xmlHttp = new XMLHttpRequest();
    } else {
        xmlHttp = new ActiveXObject('Microsoft.XMLHTTP');
    } 
    var params = [];
    for (var key in opt.data) {
        params.push(key + '=' + opt.data[key]);
    }
    var postData = params.join('&');
    if (opt.type.toUpperCase() === 'POST') {
        xmlHttp.open(opt.type, opt.url, opt.async);
        xmlHttp.setRequestHeader('Content-Type', 'application/x-www-form-urlencoded;charset=utf-8');
        xmlHttp.send(postData);
    } else if (opt.type.toUpperCase() === 'GET') {
        xmlHttp.open(opt.type, opt.url + '?' + postData, opt.async);
        xmlHttp.send(null);
    }
    xmlHttp.onreadystatechange = function () {
        if (xmlHttp.readyState == 4 && xmlHttp.status == 200) {
            opt.success(xmlHttp.responseText);
        }
    }
}
```

### 获取元素的宽高等

使用原生js，有两种方法获取元素宽高

``` js
var elem = document.getElementById('js-test');

// 方法1：
var width = elem.offsetWidth,
    height = elem.offsetHeight,
    left = elem.offsetLeft,
    top = elem.offsetTop;

// 方法2：
// IE中调用该方法的返回对象没有width， height这两个属性，因此做了简单的兼容处理
var rect = elem.getBoundingClientRect(),
    top = rect.top,
    left = rect.left,
    right = rect.right,
    bottom = rect.bottom,
    width = rect.width || (right - left),
    height = rect.height || (bottom - top);
```

注意，`obj.offsetTop`属性获取的值是绝对的，不会随页面滚动而发生变化。但`getBoundingClientRect()`获取的`top`和`left`属性值是相对于视窗的，会随浏览器滚动而发生变化，若实现`offsetTop`一样效果，可以做如下处理：

``` js
var elem = document.getElementById('js-btn');

window.addEventListener('scroll', function () {
    // 获取滚动距离，做了兼容处理
    var scrollTop = (((t = document.documentElement) || (t = document.body.parentNode)) && typeof t.scrollTop == 'number' ? t : document.body).scrollTop;
    var scrollLeft = (((t = document.documentElement) || (t = document.body.parentNode)) && typeof t.scrollLeft == 'number' ? t : document.body).scrollLeft;

    // 即时获取元素属性
    var rect = elem.getBoundingClientRect();

    console.log(elem.offsetTop);
    console.log(rect.top + scrollTop);
})
```

注意：`offsetTop`获取的值为整数，而`getBoundingClientRect().top`获取到的值有可能是小数，四舍五入后与`offsetTop`相同。

关于`getBoundingClientRect`的兼容性，查看[MDN - getBoundingClientRect](https://developer.mozilla.org/zh-CN/docs/Web/API/Element/getBoundingClientRect)

### 获取css样式

当我们在使用rem单位时，经常会碰到小数点的单位，而某些属性是不会拿到小数点单位的。测试如下：

``` js
function getStyle(obj, attr) {
    if (obj.currentStyle) { // ie6/7/8
        return obj.currentStyle[attr];
    } else { // 9+
        return getComputedStyle(obj, false)[attr];
    }
}

var testElem = document.getElementById('js-btn');
testElem.style.width = '98.5px';

console.log(getStyle(testElem, 'width')); // '98.5px'
console.log(testElem.offsetWidth); // 99
```

### 四舍五入

``` js
/**
* 四舍五入小数点
* @param {[number]} src [输入的数字]
* @param {[number]} pos [精确到后几位]
* @return {[number]}
*/
function fomatFloat(src, pos) {
    if (!arguments.length) return -1;
    if (!isNaN(src)) {
        pos = (src > 0 && src < 1) ? 2 : pos || 1;
        return Math.round(src * Math.pow(10, pos)) / Math.pow(10, pos);
    }
    return src
}

fomatFloat(3.1415927, 3) //3.142
```

### 点击空白处关闭某容器

``` js
$(document).on("click", function (e) {
    var target = $(e.target);
    // .closest()沿 DOM 树向上遍历，直到找到已应用选择器的一个匹配为止，返回包含零个或一个元素的 jQuery 对象。
    if (target.closest(".lenovoWord").length == 0) {
        $(".lenovoWord").hide();
    };
    e.stopPropagation();
});
```

### toApp

``` js
// old
function testApp(url) {
    var timeout, t = 1000, hasApp = true;
    setTimeout(function () {
        if (hasApp) {
            alert('安装了app');
        } else {
            alert('未安装app');
        }
        document.body.removeChild(ifr);
    }, 2000)

    var t1 = Date.now();
    var ifr = document.createElement("iframe");
    ifr.setAttribute('src', url);
    ifr.setAttribute('style', 'display:none');
    document.body.appendChild(ifr);
    timeout = setTimeout(function () {
        var t2 = Date.now();
        if (!t1 || t2 - t1 < t + 100) {
            hasApp = false;
        }
    }, t);
}

#example
<a href="javascript:testApp('地址地址地址')">团购</a>

// new
function openApp(appUrl, callback) {
    // 检查app是否打开
    function checkOpen(cb) {
        var _clickTime = +(new Date());

        function check(elsTime) {
            if (elsTime > 3000 || document.hidden || document.webkitHidden) {
                cb(1);
            } else {
                cb(0);
            }
        }
        //启动间隔20ms运行的定时器，并检测累计消耗时间是否超过3000ms，超过则结束
        var _count = 0,
            intHandle;
        intHandle = setInterval(function () {
            _count++;
            var elsTime = +(new Date()) - _clickTime;
            if (_count >= 100 || elsTime > 3000) {
                clearInterval(intHandle);
                check(elsTime);
            }
        }, 20);
    }

    //在iframe 中打开APP
    var ifr = document.createElement('iframe');
    ifr.src = appUrl;
    ifr.style.display = 'none';
    if (callback) {
        checkOpen(function (opened) {
            callback && callback(opened);
        });
    }

    document.body.appendChild(ifr);
    setTimeout(function () {
        document.body.removeChild(ifr);
    }, 2000);
}

// iosVersion
var ios_reg = userAgent.match(/os (\d+)_(\d+)_?(\d+)?/),
    ios_ver = ios_reg ? parseInt(ios_reg[1], 10) : 0;

// invoke
var td_url = 'http://www.mia.com/dowmApp.html',
    call_url = 'miyabaobei://';
$('body').on('click', 'a.mia-downApp', function () {
    if (ios_ver >= 9) { //ios9+
        setTimeout(function () { //0: 不加0的延迟会发生连续跳两次
            window.location.href = call_url;
            setTimeout(function () {
                window.location.href = td_url;
            }, 200)
        }, 0)
        return false
    } else {
        openApp(call_url, function (opened) { //opened = 0 || 1
            if (!opened) { //未打开app
                // window.location.href = td_url; #注释原因: self.opts.el的href已经有url 未打开app时会跳转这个url
            }
        })
    }
})


#example
<a class="mia-downApp" href="https://itunes.apple.com/cn/app/mi-ya-bao-bei-zhong-guo-zui/id973366293?mt=8"></a>
```

### offset

``` js
/**
* 获取页面滚动条的滚动距离
* @return {object} top 纵向滚动距离 left 横向滚动距离
*/
function scrollOffset() {
    var supportPageOffset = window.pageXOffset !== undefined;
    var isCSS1Compat = ((document.compatMode || "") === "CSS1Compat");
    return {
        top: supportPageOffset ? window.pageYOffset : isCSS1Compat ? document.documentElement.scrollTop : document.body.scrollTop,
        left: supportPageOffset ? window.pageXOffset : isCSS1Compat ? document.documentElement.scrollLeft : document.body.scrollLeft
    }
}

/**
 * 获取元素在当前视窗中的各种属性
 * @param {object} elem 传入元素 默认原生对象
 * @return {object} top 当前元素距顶部的绝对值 left 当前元素距左侧的绝对值
 */
function offset(elem) {
    elem = elem || {};
    var box = elem.getBoundingClientRect();
    // document.documentElement.clientTop document.documentElement.clientLeft 兼容ie7
    return {
        top: box.top - document.documentElement.clientTop + scrollOffset().top,
        left: box.left - document.documentElement.clientLeft + scrollOffset().left,
        right: box.right - document.documentElement.clientLeft + scrollOffset().left,
        bottom: box.bottom - document.documentElement.clientTop + scrollOffset().top
    }
}
```

### 生成随机数

``` js
/**
* 生成一个区间内的随机整数
* @param   {number} min 最小值（包含）
* @param   {number} max 最大值（包含）
*/
var rdm = function (min, max) {
    return Math.floor(Math.random() * (max - min + 1) + min);
}
```

### 比较ua版本号

``` js
var ua = 'miyabaobei_ios_5.5.0';

/**
* 当前版本与传入的版本号进行对比
* 如果当前版本 < 传入版本 返回 0
* 如果当前版本 >= 传入版本 返回 1
* 其他返回 -1
*/
function compareVersion(ver) {
    var regMiaUA = /miyabaobei_ios_(.*)/;
    var res = regMiaUA.exec(ua);
    if (res) {
        if (res[1] < ver) {
            return 0
        }
        return 1
    } else {
        console.warn('没有匹配到版本');
        return -1;
    }
}

console.log(compareVersion('5.5.3'));
console.log(compareVersion('5.4'));
console.log(compareVersion('5.5'));
```

### 浮点数取整

``` js
const x = 123.4545;
x >> 0; // 123
~~x; // 123
x | 0; // 123
Math.floor(x); // 123

// 注意：前三种方法只适用于32个位整数，对于负数的处理上和 Math.floor是不同的
Math.floor(-12.53); // -13
-12.53 | 0; // -12      
```

### 生成6位数字验证码

``` js
// 方法一
('000000' + Math.floor(Math.random() *  999999)).slice(-6);

// 方法二
Math.random().toString().slice(-6);

// 方法三
Math.random().toFixed(6).slice(-6);

// 方法四
'' + Math.floor(Math.random() * 999999);
```

### 16进制颜色代码生成

``` js
(function () {
    return '#' + ('00000' +
        (Math.random() * 0x1000000 << 0).toString(16)).slice(-6);
})();
```

### n维数组展开成一维数组

``` js
var foo = [1, [2, 3], ['4', 5, ['6', 7, [8]]], [9], 10];

// 方法一
// 限制：数组项不能出现`,`，同时数组项全部变成了字符数字
foo.toString().split(','); // ["1", "2", "3", "4", "5", "6", "7", "8", "9", "10"]

// 方法二
// 转换后数组项全部变成数字了
eval('[' + foo + ']'); // [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]

// 方法三，使用ES6展开操作符
// 写法太过麻烦，太过死板
[1, ...[2, 3], ...['4', 5, ...['6', 7, ...[8]]], ...[9], 10]; // [1, 2, 3, "4", 5, "6", 7, 8, 9, 10]

// 方法四
JSON.parse(`[${JSON.stringify(foo).replace(/\[|]/g, '')}]`); // [1, 2, 3, "4", 5, "6", 7, 8, 9, 10]

// 方法五
const flatten = (ary) => ary.reduce((a, b) => a.concat(Array.isArray(b) ? flatten(b) : b), []);
flatten(foo); // [1, 2, 3, "4", 5, "6", 7, 8, 9, 10]

// 方法六
function flatten(a) {
    return Array.isArray(a) ? [].concat(...a.map(flatten)) : a;
}
flatten(foo); // [1, 2, 3, "4", 5, "6", 7, 8, 9, 10]

// 注：更多方法请参考《How to flatten nested array in JavaScript?》
```

### 驼峰命名转下划线

``` js
'componentMapModelRegistry'.match(/^[a-z][a-z0-9]+|[A-Z][a-z0-9]*/g).join('_').toLowerCase();

// => component_map_model_registry
```

### 删除前后空白字符trim

``` js
// from https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/Global_Objects/String/Trim
String.prototype.trim = function () {
    return this.replace(/^[\s\uFEFF\xA0]+|[\s\uFEFF\xA0]+$/g, '')
}
```

### 获取url的参数

``` js
function GetRequest() {
    var url = location.search; //获取url中"?"符后的字串
    var o = {};
    if (url.indexOf("?") != -1) {
        var str = url.substr(1);
        strs = str.split("&");
        for (var i = 0; i < strs.length; i++) {
            o[strs[i].split("=")[0]] = (strs[i].split("=")[1]);
        }
    }
    return o;
}
// http://xxx/index.html?a=1&b=2
// => {a:1, b:2}

/**
 * 根据参数名称 获取参数值
 * @param {string} name 参数名称
 */
function getQueryString(name) {
    var reg = new RegExp("(^|&)" + name + "=([^&]*)(&|$)", "i");
    var r = window.location.search.substr(1).match(reg);
    if (r != null) return unescape(r[2]);
    return null;
}

// es6 实现传入 search
const query = (search = '') => ((querystring = '') => (q => (querystring.split('&').forEach(item => (kv => kv[0] && (q[kv[0]] = kv[1]))(item.split('='))), q))({}))(search.split('?')[1]);

// es5
// 对应ES5实现
var query = function (search) {
    if (search === void 0) {
        search = '';
    }
    return (function (querystring) {
        if (querystring === void 0) {
            querystring = '';
        }
        return (function (q) {
            return (querystring.split('&').forEach(function (item) {
                return (function (kv) {
                    return kv[0] && (q[kv[0]] = kv[1]);
                })(item.split('='));
            }), q);
        })({});
    })(search.split('?')[1]);
};

// query(window.location.search)
// query('?key1=value1&key2=value2'); // es6.html:14 {key1: "value1", key2: "value2"}
```

### 特殊字符转义

``` js
function htmlspecialchars(str) {
    var str = str.toString().replace(/&/g, "&amp;").replace(/</g, "&lt;").replace(/>/g, "&gt;").replace(/"/g, '&quot;');
    return str;
}

htmlspecialchars('&jfkds<>'); // "&amp;jfkds&lt;&gt;"
```

### 动态插入js

``` js
function injectScript(src) {
    var s, t;
    s = document.createElement('script');
    s.type = 'text/javascript';
    // s.async = true; // async
    s.src = src;
    t = document.getElementsByTagName('script')[0];
    t.parentNode.insertBefore(s, t);
}
```

### 格式化数量

``` js
// 方法一
function formatNum(num, n) {
    if (typeof num == "number") {
        num = String(num.toFixed(n || 0));
        var re = /(-?\d+)(\d{3})/;
        while (re.test(num)) num = num.replace(re, "$1,$2");
        return num;
    }
    return num;
}

formatNum(2313123, 3); // "2,313,123.000"

// 方法二
'2313123'.replace(/\B(?=(\d{3})+(?!\d))/g, ','); // "2,313,123"

// 方法三
function formatNum(str) {
    return str.split('').reverse().reduce((prev, next, index) => {
        return ((index % 3) ? next : (next + ',')) + prev
    });
}

formatNum('2313323'); // "2,313,323"
```

### 单行写一个评级组件

``` js
function getRate(rate) {
    return "★★★★★☆☆☆☆☆".slice(5 - rate, 10 - rate)
}
console.log(getRate(4)) // ★★★★☆
```

### 匿名函数自执行写法

``` js
( function() {}() );
( function() {} )();
[ function() {}() ];

~ function() {}();
! function() {}();
+ function() {}();
- function() {}();

delete function() {}();
typeof function() {}();
void function() {}();
new function() {}();
new function() {};

var f = function() {}();

1, function() {}();
1 ^ function() {}();
1 > function() {}();
```

### 两个整数交换数值

``` js
var a = 20, b = 30;
a ^= b;
b ^= a;
a ^= b;

a; // 30
b; // 20
```

### 用最短的代码实现一个长度为m(6)且值都n(8)的数组

``` js
Array(6).fill(8); // [8, 8, 8, 8, 8, 8]
```

### 将argruments对象转换成数组

``` js
var argArray = Array.prototype.slice.call(arguments);

// ES6：
var argArray = Array.from(arguments)

// or
var argArray = [...arguments];
```

### 最短的代码实现数组去重

``` js
[...new Set([1, "1", 2, 1, 1, 3])]; // [1, "1", 2, 3]
```

### JavaScript 错误处理的方式的正确姿势

``` js
try {
    something
} catch (e) {
    window.location.href = "http://stackoverflow.com/search?q=[js]+" + e.message;
}
```

### 在浏览器中根据url下载文件

``` js
function download(url) {
    var isChrome = navigator.userAgent.toLowerCase().indexOf('chrome') > -1;
    var isSafari = navigator.userAgent.toLowerCase().indexOf('safari') > -1;
    if (isChrome || isSafari) {
        var link = document.createElement('a');
        link.href = url;
        if (link.download !== undefined) {
            var fileName = url.substring(url.lastIndexOf('/') + 1, url.length);
            link.download = fileName;
        }
        if (document.createEvent) {
            var e = document.createEvent('MouseEvents');
            e.initEvent('click', true, true);
            link.dispatchEvent(e);
            return true;
        }
    }
    if (url.indexOf('?') === -1) {
        url += '?download';
    }
    window.open(url, '_self');
    return true;
}
```

### 快速生成UUID

``` js
function uuid() {
    var d = new Date().getTime();
    var uuid = 'xxxxxxxxxxxx-4xxx-yxxx-xxxxxxxxxxxx'.replace(/[xy]/g, function (c) {
        var r = (d + Math.random() * 16) % 16 | 0;
        d = Math.floor(d / 16);
        return (c == 'x' ? r : (r & 0x3 | 0x8)).toString(16);
    });
    return uuid;
};
uuid(); // "33f7f26656cb-499b-b73e-89a921a59ba6"
```

### JavaScript浮点数精度问题

``` js
function isEqual(n1, n2, epsilon) {
    epsilon = epsilon == undefined ? 10 : epsilon; // 默认精度为10
    return n1.toFixed(epsilon) === n2.toFixed(epsilon);
}
0.1 + 0.2; // 0.30000000000000004
isEqual(0.1 + 0.2, 0.3); // true
0.7 + 0.1 + 99.1 + 0.1; // 99.99999999999999
isEqual(0.7 + 0.1 + 99.1 + 0.1, 100); // true
```

### 格式化表单数据

``` js
function formatParam(obj) {
    var query = '',
        name, value, fullSubName, subName, subValue, innerObj, i;
    for (name in obj) {
        value = obj[name];
        if (value instanceof Array) {
            for (i = 0; i < value.length; ++i) {
                subValue = value[i];
                fullSubName = name + '[' + i + ']';
                innerObj = {};
                innerObj[fullSubName] = subValue;
                query += formatParam(innerObj) + '&';
            }
        } else if (value instanceof Object) {
            for (subName in value) {
                subValue = value[subName];
                fullSubName = name + '[' + subName + ']';
                innerObj = {};
                innerObj[fullSubName] = subValue;
                query += formatParam(innerObj) + '&';
            }
        } else if (value !== undefined && value !== null)
            query += encodeURIComponent(name) + '=' + encodeURIComponent(value) + '&';
    }
    return query.length ? query.substr(0, query.length - 1) : query;
}
var param = {
    name: 'jenemy',
    likes: [0, 1, 3],
    memberCard: [{
            title: '1',
            id: 1
        },
        {
            title: '2',
            id: 2
        }
    ]
}
formatParam(param); // "name=12&likes%5B0%5D=0&likes%5B1%5D=1&likes%5B2%5D=3&memberCard%5B0%5D%5Btitle%5D=1&memberCard%5B0%5D%5Bid%5D=1&memberCard%5B1%5D%5Btitle%5D=2&memberCard%5B1%5D%5Bid%5D=2"
```

### 创建指定长度非空数组

在JavaScript中可以通过new Array(3)的形式创建一个长度为3的空数组。在老的Chrome中其值为[undefined x 3]，在最新的Chrome中为[empty x 3]，即空单元数组。在老Chrome中，相当于显示使用[undefined, undefined, undefined]的方式创建长度为3的数组。

但是，两者在调用map()方法的结果是明显不同的

``` js
var a = new Array(3);
var b = [undefined, undefined, undefined];
a.map((v, i) => i); // [empty × 3]
b.map((v, i) => i); // [0, 1, 2]
```

多数情况我们期望创建的是包含undefined值的指定长度的空数组，可以通过下面这种方法来达到目的：

``` js
var a = Array.apply(null, { length: 3 });
a; // [undefined, undefined, undefined]
a.map((v, i) => i); // [0, 1, 2]
```

总之，尽量不要创建和使用空单元数组。

### 验证是否为负数的正则表达式

``` js
/^-\d+$/.test(str);
```

### 数字四舍五入

``` js
// v: 值，p: 精度
function (v, p) {
    p = Math.pow(10, p >>> 31 ? 0 : p | 0)
    v *= p;
    return (v + 0.5 + (v >> 31) | 0) / p
}
round(123.45353, 2); // 123.45
```

### 使用 ~x.indexOf('y')来简化 x.indexOf('y')>-1

``` js
var str = 'hello world';
if (str.indexOf('lo') > -1) {
    // ...
}

if (~str.indexOf('lo')) {
    // ...
}
```

### 统计字符串中相同字符出现的次数

``` js
var arr = 'photoshop';
var info = arr.split('').reduce((p, k) => (p[k]++ || (p[k] = 1), p), {});
console.log(info); // { p: 2, h: 2, o: 3, t: 1, s: 1 }
```

### 测试质数

``` js
function isPrime(n) {
    return !(/^.?$|^(..+?)\1+$/).test('1'.repeat(n))
}
```

### 截取指定字节数的字符串

``` js
/**
 * 截取指定字节的字符串
 * @param str 要截取的字符穿
 * @param len 要截取的长度，根据字节计算
 * @param suffix 截取前len个后，其余的字符的替换字符，一般用“…”
 * @returns {*}
 */
function cutString(str, len, suffix) {
    if (!str) return "";
    if (len <= 0) return "";
    if (!suffix) suffix = "";
    var templen = 0;
    for (var i = 0; i < str.length; i++) {
        if (str.charCodeAt(i) > 255) {
            templen += 2;
        } else {
            templen++
        }
        if (templen == len) {
            return str.substring(0, i + 1) + suffix;
        } else if (templen > len) {
            return str.substring(0, i) + suffix;
        }
    }
    return str;
}
```

### 获取时间格式的几个举例

``` js
// 方法一
function format1(x, y) {
    var z = {
        y: x.getFullYear(),
        M: x.getMonth() + 1,
        d: x.getDate(),
        h: x.getHours(),
        m: x.getMinutes(),
        s: x.getSeconds()
    };
    return y.replace(/(y+|M+|d+|h+|m+|s+)/g, function (v) {
        return ((v.length > 1 ? "0" : "") + eval('z.' + v.slice(-1))).slice(-(v.length > 2 ? v.length : 2))
    });
}

format1(new Date(), 'yy-M-d h:m:s'); // 17-10-14 22:14:41

// 方法二
Date.prototype.format = function (fmt) {
    var o = {
        "M+": this.getMonth() + 1, //月份 
        "d+": this.getDate(), //日 
        "h+": this.getHours(), //小时 
        "m+": this.getMinutes(), //分 
        "s+": this.getSeconds(), //秒 
        "q+": Math.floor((this.getMonth() + 3) / 3), //季度 
        "S": this.getMilliseconds() //毫秒 
    };
    if (/(y+)/.test(fmt)) {
        fmt = fmt.replace(RegExp.$1, (this.getFullYear() + "").substr(4 - RegExp.$1.length));
    }
    for (var k in o) {
        if (new RegExp("(" + k + ")").test(fmt)) {
            fmt = fmt.replace(RegExp.$1, (RegExp.$1.length == 1) ? (o[k]) : (("00" + o[k]).substr(("" + o[k]).length)));
        }
    }
    return fmt;
}

new Date().format('yy-M-d h:m:s'); // 17-10-14 22:18:17
```

### 文本相关

``` js
/**
 * 获取字符串字节长度
 * @param {String}
 * @returns {Boolean}
 */
function checkLength(v) {
    var realLength = 0;
    var len = v.length;
    for (var i = 0; i < len; i++) {
        var charCode = v.charCodeAt(i);
        if (charCode >= 0 && charCode <= 128) realLength += 1;
        else realLength += 2;
    }
    return realLength;
}

/**
 * 统计文字个数
 */
function wordCount(data) {
    var pattern = /[a-zA-Z0-9_\u0392-\u03c9]+|[\u4E00-\u9FFF\u3400-\u4dbf\uf900-\ufaff\u3040-\u309f\uac00-\ud7af]+/g;
    var m = data.match(pattern);
    var count = 0;
    if (m === null) return count;
    for (var i = 0; i < m.length; i++) {
        if (m[i].charCodeAt(0) >= 0x4E00) {
            count += m[i].length;
        } else {
            count += 1;
        }
    }
    return count;
}

var text = '贷款买房，也意味着你能给自己的资产加杠杆，能够撬动更多的钱，来孳生更多的财务性收入。';
wordCount(text); // 38
```

### 对象克隆、深拷贝

``` js
/**
 * 对象克隆 & 深拷贝
 * @param obj
 * @returns {{}}
 */
function cloneObj(obj) {
    var newO = {};
    if (obj instanceof Array) {
        newO = [];
    }
    for (var key in obj) {
        var val = obj[key];
        newO[key] = typeof val === 'object' ? cloneObj(val) : val;
    }
    return newO;
};
```

增强版：

``` js
/**
 * 对象克隆 & 深拷贝
 * @param obj
 * @returns {{}}
 */
function clone(obj) {
    // Handle the 3 simple types, and null or undefined
    if (null == obj || "object" != typeof obj) return obj;
    // Handle Date
    if (obj instanceof Date) {
        var copy = new Date();
        copy.setTime(obj.getTime());
        return copy;
    }
    // Handle Array
    if (obj instanceof Array) {
        var copy = [];
        for (var i = 0,
                len = obj.length; i < len; ++i) {
            copy[i] = clone(obj[i]);
        }
        return copy;
    }
    // Handle Object
    if (obj instanceof Object) {
        var copy = {};
        for (attr in obj) {
            if (obj.hasOwnProperty(attr)) copy[attr] = clone(obj[attr]);
        }
        return copy;
    }
    throw new Error("Unable to copy obj! Its type isn't supported.");
}
```

### js正则为url添加http标识

``` js
var html = 'http://www.google.com ';
html += '\rwww.google.com ';
html += '\rcode.google.com ';
html += '\rhttp://code.google.com/hosting/search?q=label%3aPython';
var regex = /(https?:\/\/)?(\w+\.?)+(\/[a-zA-Z0-9\?%=_\-\+\/]+)?/gi;
console.log('before replace:');
console.log(html);
html = html.replace(regex, function (match, capture) {
    if (capture) {
        return match
    } else {
        return 'http://' + match;
    }
});
console.log('after replace:');
console.log(html);

// 将图片链接开头的http(s)替换为//
function handleImgProtocol(src) {
    let reg = /^\/\/|(https?):\/\//gi;
    let _match = src.match(reg)
    if (_match) {
        src = src.replace(_match[0], '//')
    } else {
        src = '//' + src
    }
    return src
}
```

### URL有效性校验方法

``` js
/**
 * URL有效性校验
 * @param str_url
 * @returns {boolean}
 */
function isURL(str_url) {
    // 验证url
    var strRegex = "^((https|http|ftp|rtsp|mms)?://)" + "?(([0-9a-z_!~*'().&=+$%-]+: )?[0-9a-z_!~*'().&=+$%-]+@)?"
        // ftp的user@ + "(([0-9]{1,3}\.){3}[0-9]{1,3}" // IP形式的URL- 199.194.52.184
        + "|" // 允许IP和DOMAIN（域名）
        + "([0-9a-z_!~*'()-]+\.)*" // 域名- www.
        + "([0-9a-z][0-9a-z-]{0,61})?[0-9a-z]\." // 二级域名
        + "[a-z]{2,6})" // first level domain- .com or .museum
        + "(:[0-9]{1,4})?" // 端口- :80
        + "((/?)|" // a slash isn't required if there is no file name
        + "(/[0-9a-z_!~*'().;?:@&=+$,%#-]+)+/?)$";
    var re = new RegExp(strRegex);
    return re.test(str_url);
}
// 建议的正则
functionisURL(str) {
    return !!str.match(/(((^https?:(?:\/\/)?)(?:[-;:&=\+\$,\w]+@)?[A-Za-z0-9.-]+|(?:www.|[-;:&=\+\$,\w]+@)[A-Za-z0-9.-]+)((?:\/[\+~%\/.\w-_]*)?\??(?:[-\+=&;%@.\w_]*)#?(?:[\w]*))?)$/g);
}
```

### 自定义封装jsonp方法

``` js
/**
 * 自定义封装jsonp方法
 * @param options
 */
var jsonp = function (options) {
    options = options || {};
    if (!options.url || !options.callback) {
        throw new Error("参数不合法");
    }
    //创建 script 标签并加入到页面中
    var callbackName = ('jsonp_' + Math.random()).replace(".", "");
    var oHead = document.getElementsByTagName('head')[0];
    options.data[options.callback] = callbackName;
    var params = formatParams(options.data);
    var oS = document.createElement('script');
    oHead.appendChild(oS);
    //创建jsonp回调函数
    window[callbackName] = function (json) {
        oHead.removeChild(oS);
        clearTimeout(oS.timer);
        window[callbackName] = null;
        options.success && options.success(json);
    };
    //发送请求
    oS.src = options.url + '?' + params;
    //超时处理
    if (options.time) {
        oS.timer = setTimeout(function () {
            window[callbackName] = null;
            oHead.removeChild(oS);
            options.fail && options.fail({
                message: "超时"
            });
        },
        time);
    }
};
/**
 * 格式化参数
 * @param data
 * @returns {string}
 */
formatParams = function (data) {
    var arr = [];
    for (var name in data) {
        arr.push(encodeURIComponent(name) + '=' + encodeURIComponent(data[name]));
    }
    return arr.join('&');
}
```

### cookie操作

``` js
//写cookies
var setCookie = function (name, value, time) {
    var strsec = getsec(time);
    var exp = new Date();
    exp.setTime(exp.getTime() + strsec * 1);
    document.cookie = name + "=" + escape(value) + ";expires=" + exp.toGMTString();
}

//cookie操作辅助函数
var getsec = function (str) {
    var str1 = str.substring(1, str.length) * 1;
    var str2 = str.substring(0, 1);
    if (str2 == "s") {
        return str1 * 1000;
    } else if (str2 == "h") {
        return str1 * 60 * 60 * 1000;
    } else if (str2 == "d") {
        return str1 * 24 * 60 * 60 * 1000;
    }
}

//读取cookies
var getCookie = function (name) {
    var arr, reg = new RegExp("(^| )" + name + "=([^;]*)(;|$)");
    if (arr = document.cookie.match(reg)) return (arr[2]);
    else return null;
}

//删除cookies
var delCookie = function (name) {
    var exp = new Date();
    exp.setTime(exp.getTime() - 1);
    var cval = getCookie(name);
    if (cval != null) document.cookie = name + "=" + cval + ";expires=" + exp.toGMTString();
}
```

### 生成随机字符串 (可指定长度)

``` js
/**
 * 生成随机字符串(可指定长度)
 * @param len
 * @returns {string}
 */
var randomString = function (len) {
    len = len || 8;
    var $chars = 'ABCDEFGHJKMNPQRSTWXYZabcdefhijkmnprstwxyz2345678';
    /****默认去掉了容易混淆的字符oOLl,9gq,Vv,Uu,I1****/
    var maxPos = $chars.length;
    var pwd = '';
    for (var i = 0; i < len; i++) {
        pwd += $chars.charAt(Math.floor(Math.random() * maxPos));
    }
    return pwd;
}
```

### Rem移动端适配

``` js
var rem = {
    baseRem: 40,
    // 基准字号，按照iphone6应该为20，此处扩大2倍，便于计算
    baseWidth: 750,
    // 基准尺寸宽，此处是按照ihpone6的尺寸
    rootEle: document.getElementsByTagName("html")[0],
    initHandle: function () {
        this.setRemHandle();
        this.resizeHandle();
    },
    setRemHandle: function () {
        var clientWidth = document.documentElement.clientWidth || document.body.clientWidth;
        this.rootEle.style.fontSize = clientWidth * this.baseRem / this.baseWidth + "px";
    },
    resizeHandle: function () {
        var that = this;
        window.addEventListener("resize",
            function () {
                setTimeout(function () {
                        that.setRemHandle();
                    },
                    300);
            });
    }
};
rem.initHandle();
```

### 关于内核

- `webkit`内核的浏览器，比如`chrome`，`safari`的css3前缀为`-webkit-transform`
- `Gecko`内核的浏览器，如`firefox`支持的css3前缀为`-moz-transform`
- `Trident`内核的浏览器，如`IE`支持的css3前缀为`-ms-transform`
- `Presto`内核浏览器，如`Opera`支持的css3前缀为`-o-transform`
