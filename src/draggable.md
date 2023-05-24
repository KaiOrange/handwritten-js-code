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

  let diffX = 0;
  let diffY = 0;
  let target = null;

  node.addEventListener('mousedown', function handleMousedown(e) {
    diffX = e.clientX - node.offsetLeft;
    diffY = e.clientY - node.offsetTop;
    target = node;
  });

  window.addEventListener('mousemove', function handleMousemove(e) {
    if(target) {
      target.style.left = e.clientX - diffX + "px";
      target.style.top = e.clientY - diffY  + "px";
    }
  });

  node.addEventListener('mouseup', function handleMouseup() {
    target = null;
  });
} 
```

`clientX`是当前鼠标相对浏览器的X坐标，`offsetLeft`是当前元素最左边距离浏览器左测的距离，`e.clientX - node.offsetLeft`可以得到鼠标相对于node元素左上角的X轴距离，也就是鼠标在元素X轴上的偏移量。`e.clientX - diffX`为移动后鼠标相对浏览器X轴的距离减去鼠标在元素上的偏移量，也就是移动后元素的X坐标。Y坐标同理。
