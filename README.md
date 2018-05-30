# 记录平常工作中的常用的小功能函数

> 有需要的同学可以自取 :relaxed:

## 将跨域图片转成base64

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

// 使用方式
imgToBase64(url).then(dataUrl => {
    const el = document.createElement('a');
    el.download = 'xx.png';
    el.setAttribute('href', dataUrl);
    el.click();
})
```