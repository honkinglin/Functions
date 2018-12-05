# 记录平常工作中的常用的小功能函数

> 有需要的同学可以自取 :relaxed:

## 将图片转成base64

```javascript
function imgToBase64(url, type='image/png', quality=0.92) {
   return new Promise((resolve, reject) => {
       let dataUrl = '';
       let canvas = document.createElement('canvas');
       let img = new Image();
       img.crossOrigin = 'anonymous';
       img.src = url;
       img.onload = (e) => {
           canvas.width = e.path[0].width;
           canvas.height = e.path[0].height;
           canvas.getContext('2d').drawImage(img, 0, 0, e.path[0].width, e.path[0].height);
           dataUrl = canvas.toDataURL(type, quality);
           resolve(dataUrl);
       }
       img.onerror = (err) => reject(err);
   })
}
```

## cookie相关操作

```javascript
function getCookie(key) {
    if (document.cookie.length === 0) return '';
    let start = document.cookie.indexOf(`${key}=`);
    if (start === -1) return '';
    start = start + key.length + 1;
    let end = document.cookie.indexOf(';', start);
    if (end === -1) {
        end = document.cookie.length;
    }
    return unescape(document.cookie.substring(start, end));
}

function setCookie(key, value, days) {
    const exdate = new Date();
    exdate.setDate(exdate.getDate() + days);
    document.cookie = `${key}=${escape(value)};expires=${exdate.toGMTString()}`;
}
```

## DOM操作类

```javascript
function hasClass(el, clsName) {
    return (new RegExp("(^|\\s+)"+clsName+"(\\s+|$)",'i') ).test(el.className);
}

function addClass(el, clsName) {
    if (!el.className.length) {
        el.className = clsName;
    } else if (!el.className.split(' ').includes(clsName)) {
        el.className += ` ${clsName}`;
    }
}

function removeClass(el, clsName) {
    const reg = new RegExp('(\\s+' + clsName + '| ^' + clsName + '\\s+)', 'g');
    const singleClass = new RegExp('^' + clsName + '$', 'g');
    el.className = singleClass.test(el.className) ? "" : el.className.replace(reg, '');
}

function toggleClass(el, clsName) {
    const clsArr = el.className.split(' ');
    if (!clsArr.includes(clsName)) {
        addClass(el, clsName);
    } else {
        removeClass(el, clsName);
    }
}
```

## url操作类

```javascript
/**
 * 解析url参数
 * @example id=12345&a=b
 * @return Object {id:12345,a:b}
 */
function parseQuery(query) {
    const str = decodeURIComponent(query);
    let result = {},
        params = [],
        param = '';

    if (str) params = str.split('&');

    for (let i = 0;i < params.length;i++) {
        param = params[i].split('=');
        param[1] ? result[param[0]] = param[1] : '';
    }

    return result;
}

function getQuery(name) {
    const search = location.search.substr(1);
    const data = parseQuery(search);
    return data[name] || '';
}

function getHash(name) {
    const search = location.hash.substr(1);
    const data = parseQuery(search);
    return data[name] || '';
}

function getHomePage() {
    const link = location.origin + location.pathname.replace(/\w+\.html$/, '');
    const homePage = link.charAt(link.length - 1) == '/' ? link : link + '/';

    return homePage;
}
```

## 判断数据类型

```javascript
function getType(obj) {
    return {}.toString.call(obj).match(/\s([a-zA-Z]+)/)[1].toLowerCase();
}
```

## 判断浏览器类型

```javascript
/**
 * @function isBroswer
 * @return {name: '', version: '', }
 */
function isBroswer () {
    var _broswer = {};
    var sUserAgent = navigator.userAgent;
    var isOpera = sUserAgent.indexOf("Opera") > -1;
    if (isOpera) {
        //首先检测Opera是否进行了伪装
        if (navigator.appName == 'Opera') {
            //如果没有进行伪装，则直接后去版本号
            _broswer.version = parseFloat(navigator.appVersion);
        } else {
            var reOperaVersion = new RegExp("Opera (\\d+.\\d+)");
            //使用正则表达式的test方法测试并将版本号保存在RegExp.$1中
            reOperaVersion.test(sUserAgent);
            _broswer.version = parseFloat(RegExp['$1']);
        }
        _broswer.name = 'opera';
    }
    var isChrome = sUserAgent.indexOf("Chrome") > -1;
    if (isChrome) {
        var reChorme = new RegExp("Chrome/(\\d+\\.\\d+(?:\\.\\d+\\.\\d+))?");
        reChorme.test(sUserAgent);
        _broswer.version = parseFloat(RegExp['$1']);
        _broswer.name = 'chrome';
    }
    //排除Chrome信息，因为在Chrome的user-agent字符串中会出现Konqueror/Safari的关键字
    var isKHTML = (sUserAgent.indexOf("KHTML") > -1
        || sUserAgent.indexOf("Konqueror") > -1 || sUserAgent
            .indexOf("AppleWebKit") > -1)
        && !isChrome;
    //判断是否基于KHTML，如果时的话在继续判断属于何种KHTML浏览器
    if (isKHTML) {
        var isSafari = sUserAgent.indexOf("AppleWebKit") > -1;
        var isKonq = sUserAgent.indexOf("Konqueror") > -1;
        if (isSafari) {
            var reAppleWebKit = new RegExp("Version/(\\d+(?:\\.\\d*)?)");
            reAppleWebKit.test(sUserAgent);
            var fAppleWebKitVersion = parseFloat(RegExp["$1"]);
            _broswer.version = parseFloat(RegExp['$1']);
            _broswer.name = 'safari';
        } else if (isKonq) {
            var reKong = new RegExp(
                "Konqueror/(\\d+(?:\\.\\d+(?\\.\\d)?)?)");
            reKong.test(sUserAgent);
            _broswer.version = parseFloat(RegExp['$1']);
            _broswer.name = 'konqueror';
        }
    }
    // !isOpera 避免是由Opera伪装成的IE
    var isIE = sUserAgent.indexOf("compatible") > -1
        && sUserAgent.indexOf("MSIE") > -1 && !isOpera;
    if (isIE) {
        var reIE = new RegExp("MSIE (\\d+\\.\\d+);");
        reIE.test(sUserAgent);
        _broswer.version = parseFloat(RegExp['$1']);
        _broswer.name = 'msie';
    }
    // 排除Chrome 及 Konqueror/Safari 的伪装
    var isMoz = sUserAgent.indexOf("Gecko") > -1 && !isChrome && !isKHTML;
    if (isMoz) {
        var reMoz = new RegExp("rv:(\\d+\\.\\d+(?:\\.\\d+)?)");
        reMoz.test(sUserAgent);
        _broswer.version = parseFloat(RegExp['$1']);
        _broswer.name = 'mozilla';
    }
    return _broswer;
}
```

## 防抖和节流

```javascript
/**
 * @function debounce 防抖
 * @param func, delay
 */
export function debounce(func, delay) {
    let timer;
    return function() {
        clearTimeout(timer);
        timer = setTimeout(() => func.apply(this, arguments), delay);
    }
}

/**
 * @function throttle 节流
 * @param func, delay
 */
export function throttle(func, delay) {
    let last = 0;
    return function() {
        let curr = +new Date();
        if (curr - last > delay) {
            func.apply(this, arguments);
            last = curr;
        }
    }
}
```

## 数组对象去重

```javascript
/**
 * 根据指定key排除相同key下的对象
 * @function unique 节流
 * @param arr, key
 */

function unique(arr, key) {
    const hash = {};
    const result = arr.reduceRight((item, next) => {
        hash[next[key]] ? '' : hash[next[key]] = true && item.push(next);
        return item;
    }, []);
    return result;
}

```

## 获取滚动条实际宽度

```javascript
let scrollBarWidth;
// https://github.com/ElemeFE/element/blob/master/src/utils/scrollbar-width.js
export function getScrollWidth() {
    if (scrollBarWidth !== undefined) return scrollBarWidth;
    const outer = document.createElement('div');
    outer.className = 'scrollbar__wrap';
    outer.style.visibility = 'hidden';
    outer.style.width = '100px';
    outer.style.position = 'absolute';
    outer.style.top = '-9999px';
    document.body.appendChild(outer);


    const widthNoScroll = outer.offsetWidth;
    outer.style.overflow = 'scroll';

    const inner = document.createElement('div');
    inner.style.width = '100%';
    outer.appendChild(inner);

    const widthWithScroll = inner.offsetWidth;
    outer.parentNode.removeChild(outer);
    scrollBarWidth = widthNoScroll - widthWithScroll;

    return scrollBarWidth;
}
```
