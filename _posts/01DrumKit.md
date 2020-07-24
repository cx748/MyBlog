---
title: JavaScript30
date: 2020-06-06 10:24:33
tags:
---
# 1 JavaScript Drum Kit
>@[帕吉的小肚腩](https://github.com/cx748)
## 实现效果
模拟一个打鼓的界面，用户在按下 ASDFGHJKL 这几个键之后，与之对应的的按钮会随之变大，对应的鼓点声音也会响起来。

[在线效果](http://lifewithwork.cn/JS30/day1/drumkit.html)

## 关键要点

1. 键盘事件
2. 播放声音
3. 改变样式

## 步骤分解
1. **添加键盘事件监听** 在window上增加keydown事件
    ```javascript
    window.addEventListener('keydown', playSound);
    ```
2. **对应的事件处理playSound**：按下键盘后会播放声音
    1. 获取键码
    2. 根据键码找到对应的音频
        ```javascript
        const audio = document.querySelector(`audio[data-key = "${event.keyCode}"]`);
        ```
    3. 根据键码找到页面中对应的元素
        ```javascript
        const key = document.querySelector(`div[data-key = "${event.keyCode}"]`);
        ```
    4. 播放对应的音频并放大按钮
        ```javascript
        key.classList.add("playing") 
        audio.currentTime = 0; //每次播放后都将音频进度归零
        audio.play();
        ```
3. **为所有的`div.key` 添加transition事件监听**：每次按键对应的按钮会随之变大
    1. 获取所有样式为`key`的元素
    ```javascript
    const keys = Array.from(document.querySelectorAll('.key')) 
    ```
    2. 为每一个样式添加事件监听
    ```javascript
    function removeTransition(event) {
      if (event.propertyName !== 'transform') return; // 过滤其中一种事件
      event.target.classList.remove('playing'); // 移除高亮的样式
    }

    keys.forEach(key => key.addEventListener("transitionend", removeTransition);
    ```

## 解决难点
### 如何将按键与页面按钮连接起来
连接的帮手是``keydown``事件中的`keyCode`属性。keyCode属性的值与键盘对应的ASCII码值相等。在初始的页面中，div和audio都添加了一个`data-key`属性用于存储对应的键码。这样的话可以在触发键盘监听事件后既可获取`keyCode`的属性值。

### 如何使页面按钮恢复原状
可以利用transitioned这个事件，它在每次CSS transition结束后会触发，我们可以利用这个事件，在每次击鼓样式改变(变大，变颜色)完成之后清除对应的样式


## 这个小项目用的语法和事件
### 语法
 \`字符串 ${ 变量、属性名 } \`：模板字面量（Template literals）中用于表示模板字符串的标识。特点是字符串首尾用反引号（\`），内部的模板部分用 ${ } 括起来表示，具体请看[MDN文档]( https://developer.mozilla.org/zh-CN/docs/Web/JavaScript/Reference/template_strings)。简单例子如下：

    ````javascript
          var a = 1;
          var b = 2;
          //不用模板的写法
          console.log("三是" + (a + b) + "不是" + (2 * a + b)); //"三是3不是4"
          //使用模板字符串的写法
          console.log(`三是${a + b}不是${2 * a + b}`); //"三是3不是4"
    ````
### 事件
1. keydown：事件触发与键盘按键按下的时候，所有的按键都会触发keydown事件，无论这些按键是否产生字符值

2. transitionend：transitionend 事件会在 CSS transition 结束后触发


www.lifewithwork.cn
