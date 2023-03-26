# 有谱网站曲谱下载油猴脚本
```
// ==UserScript==
// @name         有谱
// @namespace    http://tampermonkey.net/
// @version      0.1
// @description  try to take over the world!
// @author       You
// @match        https://yoopu.me/view/*
// @icon         https://www.google.com/s2/favicons?sz=64&domain=bootcdn.cn
// @require      https://cdn.bootcdn.net/ajax/libs/html2canvas/1.4.1/html2canvas.min.js
// @grant        none
// ==/UserScript==

(function() {
    'use strict';
    const downloadBase64File = (base64Data, filename) => {
        const blob = base64ToBlob(base64Data);
        const url = URL.createObjectURL(blob);
        const link = document.createElement('a');
        link.download = filename;
        link.href = url;
        document.body.appendChild(link);
        link.click();
        document.body.removeChild(link);
        return Promise.resolve()
    }

    const base64ToBlob = (base64Data) => {
        const byteString = atob(base64Data.split(',')[1]);
        const mimeString = base64Data.split(',')[0].split(':')[1].split(';')[0];
        const ab = new ArrayBuffer(byteString.length);
        const ia = new Uint8Array(ab);
        for (let i = 0; i < byteString.length; i++) {
            ia[i] = byteString.charCodeAt(i);
        }
        return new Blob([ab], { type: mimeString });
    }
    const openNewWindow = (url) => {
        const img = new Image();
        img.src = url; //已经拼接好的base64 未拼接则为"image/jpeg;base64," + base64
        const newWin = window.open("", "_blank");
        newWin.document.height = "100%";
        newWin.document.width = "100%";
        newWin.document.write(img.outerHTML);
        newWin.document.close()
    }
    const htmlTocanvas = () => {
        let main = document.querySelector('.main')
        let bg = getComputedStyle(main).backgroundColor
        let dom = document.querySelector('.xhe-sheet') || document.querySelector(".at-surface")
        window.html2canvas(dom, {
            useCORS: true, // 允许加载跨域的图片
            allowTaint: false,
            tainttest: true, // 检测每张图片都已经加载完成
            logging: true,
            backgroundColor: bg
        }).then(canvas => {
            var getImg = canvas.toDataURL("image/png");
            downloadBase64File(getImg, document.title).then(() => {
                closeToast()
            })
        });
    }

    const createBtn = () => {
        const btn = document.createElement('button')
        btn.innerText = '下载图片'
        btn.style.position = 'fixed'
        btn.style.right = '20px'
        btn.style.bottom = '40px'
        btn.style.padding = '10px'
        btn.addEventListener('click', () => {
            htmlTocanvas()
        })
        document.body.appendChild(btn)
    }

    const showToast = (message) => {
        const toast = document.querySelector('#yp-toast')
        toast.querySelector('.message').innerText = message
        toast.classList.add('show')
    }

    const closeToast = () => {
        document.querySelector('#yp-toast').classList.remove('show')
    }
    const findBtn = () => {
        [...document.querySelectorAll('.button-container')].forEach(item => {
         if (item.innerText.includes('打印')) {
          const newBtn = item.cloneNode(true)

          newBtn.innerHTML = newBtn.innerHTML.replace('打印曲谱', '打印曲谱(免登陆)')
          newBtn.addEventListener('click', (event) => {
            showToast('下载中，会有所卡顿，请稍后')
              setTimeout(() => {
                  htmlTocanvas()
              })
          })
          item.parentNode.appendChild(newBtn)
         }
        })
    }
    window.onload = function () {
        findBtn()
//       createBtn()
    }
    // Your code here...
})();
```

## 至于脚本怎么用。
会用油猴可以，可以先百度，浏览器油猴插件怎么装。

## 该脚本会在页面新增一个下载按钮，装了便知
