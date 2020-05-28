# callapp_PC
PC端唤醒app

**对于ie edge支持的不好,在edge浏览器中，无论app是否已经安装到电脑上面，都会触发callback回调,在ie中，当app没有安装的情况下,也依然不会触发callback回调,所以对于ie和edge有需求的，可以自己判断浏览器类型，对其降级处理**

PC中app唤醒的时候没有判断是否已经安装此app的方法,所以设计师要求的若已经安装了就唤醒,若没有安装就询问是否下载的需求难以实现

我实现的原理特别简单,就是浏览器在唤醒app的时候会有弹窗询问是否打开(未安装的情况下不会询问),出现这个弹窗询问的时候会导致浏览器中的焦点元素失去焦点,所以只要在唤起之前让一个表单元素先获取焦点,然后监听失去焦点事件,当能打开app的时候那么就会触发失去焦点事件,当没有安装app的时候,那么就不会触发失去焦点事件(即使用户在弹窗中勾上不再提醒导致弹窗不出现了也依然会满足上面的效果)

```js
function callapp_PC({ url, callback }) {
  var t = setTimeout(callback, 1000);
  var inp = document.createElement('input')
  inp.style.position = 'absolute'
  inp.style.clip = 'rect(0, 0, 0, 0)'
  function blur() {
    window.clearTimeout(t)
  }
  inp.addEventListener('blur', blur)
  document.body.appendChild(inp)
  inp.focus()
  setTimeout(function () {
    inp.removeEventListener('blur', blur)
    document.body.removeChild(inp)
  }, 1000);
  location.href = url
}
```

使用示例

```js
document.getElementById('openApp').onclick = function (e) {
  callapp_PC({
    url: "baijiacloud://urlpath=https%3A%2F%2Fb41568785.at.baijiayun.com",
    callback: function () {
      console.log('唤醒失败了,做一些唤醒失败后的操作吧')
    }
  })
}

```
