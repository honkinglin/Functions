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
function setCookie(key, value, days) {
    const exdate = new Date();
    exdate.setDate(exdate.getDate() + days);
    document.cookie = `${key}=${escape(value)};expires=${exdate.toGMTString()}`;
}

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
```

## DOM操作类

```javascript
function addClass(el, clsName) {
    if (!el.className.length) {
        el.className = clsName;
    } else if (el.className.indexOf(clsName) === -1) {
        el.className += ` ${clsName}`;
    }
}

function removeClass(el, clsName) {
    const reg = new RegExp('(\\s+' + clsName + '| ^' + clsName + '\\s+)', 'g');
    const singleClass = new RegExp('^' + clsName + '$', 'g');
    el.className = singleClass.test(el.className) ? "" : el.className.replace(reg, '');
}

function hasClass(el, clsName) {
    if (!el.className.length)
        return false;

    const clsArr = el.className.split(' ');
    if (clsArr.includes(clsName))
        return true;

    return false;
}
```