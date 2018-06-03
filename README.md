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