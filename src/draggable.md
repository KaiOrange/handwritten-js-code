# 手写拖拽 #

## 描述 ##

写一个`draggable`函数，接收一个具有绝对定位的node节点作为参数，使得该节点可以拖拽。
不可使用HTML5 draggable API。

```HTML
<style>
  #app {
    width: 100px;
    height: 100px;
    background: red;
    position: absolute;
  }
</style>
<div id="app"></div>
<script>
  function draggable(node) {
    // 待实现
  }
  draggable(document.querySelector('#app'));
</script>
```

## 实现 ##

1. 监听节点的`mousedown`事件，记录鼠标在节点元素的偏移量，并标记当前节点可拖拽；
2. 监听`window`对象的`mousemove`事件，如果有节点标记为可拖拽，则设置该节点的位置；
3. 监听节点的`mouseup`事件，取消标记节点可拖拽，以保证`mousemove`事件不设置位置。

> 注：第二步是监听`window`对象的`mousemove`事件，因为鼠标是在整个窗口移动。

```JavaScript
function draggable(node) {
  if(!node) {
    return;
  } else if(typeof node === 'string') {
    node = querySelector(node);
  }

  let offsetX = 0;
  let offsetY = 0;
  let target = null;

  node.addEventListener('mousedown', function handleMousedown(e) {
    offsetX = e.offsetX;
    offsetY = e.offsetY;
    target = node;
  });

  window.addEventListener('mousemove', function handleMousemove(e) {
    if(target) {
      target.style.left = e.clientX - offsetX + "px";
      target.style.top = e.clientY - offsetY  + "px";
    }
  });

  node.addEventListener('mouseup', function handleMouseup() {
    target = null;
  });
} 
```

`offsetX`是鼠标相对当前元素的X坐标，`clientX`是鼠标相对于浏览器的X坐标，`e.clientX - offsetX`可以计算出移动后元素的left值。Y坐标同理。
