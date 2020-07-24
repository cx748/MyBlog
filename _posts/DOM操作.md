```js
// 查找节点
document.getElementById("id"); // 通过id查找
document.getElementByClassName("class") // 通过class查找
document.getElementByTagName("div") // 通过标签查找
//创建节点
document.createDocumentFragment(); // 内存文档碎片
document.createElement(); // 创建元素
document.createTextNode(); // 创建文本节点
//添加节点
var ele = document.getElementById("myDiv")
var oldELe = document.createElement("p")
var newEle = document.createElement("div")
ele.appendChild(oldEle)
// 删除节点
ele.removeChild(oldELe)
// 替换节点
ele.replaceChild(newEle, oldELe)
// 插入节点
ele.insertBefore(oldELe, newEle)
```