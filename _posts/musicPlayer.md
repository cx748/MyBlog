# 音乐播放器

因为现在播放器因为版权问题，会时常下架一些歌，一直换播放器也是比较麻烦的事情，所以想着是否能够把自己非常喜欢的歌下载下来，然后通过自己做的播放器播放。

## 模块设计

1. 列表播放器
2. 播放器视图
3. 歌词
4. 背景音频可视化
5. 调音工具(暂时还没完成，以后会更新)

### 模块功能

#### 播放列表功能

1. 根据歌名选择对应的音频
2. 当前歌曲播放完毕后会按列表顺序播放音频
3. 列表播放按钮随着歌曲状态改变
4. 音频状态切歌的时候要改变

### 播放器视图

1. 播放器的播放按钮能控制当前的音频
2. 播放器的播放按钮图标随着音频状态更新

### 歌词栏

1. 歌词要随着歌曲播放时间轮播

### 背景音频可视化

1. 将音频的频率在页面背景可视化展现出来

## 模块功能实现

### 播放列表

#### 生成播放列表

每一首歌应该都有`src`,`name`,`lyric`属性，为了减少 html 里的代码量，可以把这些抽象在一个对象数组`songs`里，数组的每一项代表每一首歌的属性。然后根据这个数组来添加 html 的 dom 节点增加播放列表的 item。列表里的每首歌后面会有一个播放按钮用来控制和显示当前该歌曲的播放状态。

```js
for (let i = 0; i < songs.length; i++) {
  let song = document.createElement("div");
  song.className = "song";
  song.dataset.src = songs[i].src;
  song.dataset.lyc = songs[i].lyric;
  song.innerHTML = `<span>${songs[i].name}</span> <span class="iconfont toggle" data-title="${songs[i].name}"">&#xe645;</span>`;
  playList.appendChild(song);
}
```

根据 songs 里的数据循环地创建\<div class="song">的 dom 节点，然后再给这个 dom 赋予自定义的属性和这个 dom 的子节点，然后把这个节点插入到他的父节点下。  
`dataset`：HTMLElement.dataset 属性允许无论是在读取模式还是写入模式访问 HTML 或者 Dom 中元素设置的所有自定义数据属性`(data-*)`集。  
\`${ }\`：其中反引号 ( ` )标识这是一个模板字面量，而${}是变量占位符，之间允许放入任意的 JS 表达式，会将运算结果当做是这个模板字面量的一部分。在上面的代码

```js
 song.innerHTML = `<span>${songs[i].name}</span> <span class="iconfont toggle"
```

会访问 songs[i]的 name，并将其插入到 song.innerHTML 字符串中。

### 播放列表中需要增加的事件监听

1. 监听列表每首歌的播放按钮的点击事件，用来控制单首歌的播放暂停。

```js
toggles.forEach((toggle) => {
  toggle.addEventListener("click", handlePressed);
}); //给播放按钮添加事件监听
```

`handlePressed`函数用来处理歌曲的播放状态和切歌动作。

```js
function handlePressed() {
  const srcNow = decodeURI(audioPlay.src); //获取当前播放/暂停下的歌曲src
  const nameNow = audioPlay.dataset.name; //获取当前播放/暂停下的歌曲sname
  const nameNext = this.dataset.title; //获取将要操作的歌曲name
  const audioToPlaySrc = songs.find((song) => song.name == nameNext).src; //获取将要操作的歌曲src
  if (nameNow != nameNext) {
    //根据name判断歌曲是否是当前歌曲，不是的话则切歌，切歌则会触发change事件，在change事件中改变样式
    audioPlay.src = audioToPlaySrc; //更改src
    audioPlay.dataset.name = nameNext; //更改data-name
    //console.log(audioPlay);
    audioPlay.play();
  } else {
    //如果是则暂停/播放
    if (audioPlay.paused) {
      audioPlay.play();
    } else {
      audioPlay.pause();
    }
  }
}
```

2. 监听正在播放音频的结束事件，用来控制按列表顺序播放。

```js
audioPlay.addEventListener("ended", sequentialPlay); //监听歌曲结束事件，处理歌曲的顺序播放

function sequentialPlay() {
  //实现顺序播放
  const index = songs.findIndex((song) => song.name == audioPlay.dataset.name);
  console.log(index);
  audioPlay.src = songs[index + 1].src;
  audioPlay.dataset.name = songs[index + 1].name;
  audioPlay.play();
}
```

### 播放器

#### 画出播放器

#### 监听播放器按钮的点击事件

```js
var x = 420,
  y = 2,
  w = 35,
  h = 26;

//播放按钮
canvas.addEventListener("click", function (e) {
  // 鼠标x轴 >= 矩阵x轴 && 鼠标x轴 <= 矩阵x轴 + 矩阵宽度
  // 鼠标y轴 >= 矩阵y轴 && 鼠标y轴 <= 矩阵y轴 + 矩阵高度
  if (
    e.layerX >= x &&
    e.layerX <= x + w &&
    e.layerY >= y &&
    e.layerY <= y + h
  ) {
    if (audioPlay.paused) {
      audioPlay.play();
    } else {
      audioPlay.pause();
    }
  }
});
```

x,y,w,h 属性确定按钮所在位置，然后通过监听鼠标点击这个位置的事件来控制当前的歌曲。

## 歌词

### 生成歌词

在网上下载的歌词文档格式是下面这种格式  
\[00:23.10]人群里突然觉得空  
[00:25.52]眼前飘过有你的种种
我们可以把每一句歌词改成一个对象，对象有两个属性 time，和 text 分别对应时间和歌词，然后将这些对象存入一个总的 lyrics 数组。

```js
function createLyrics(lrcContent) {
  if (lrcContent.length == 0) return;
  var lrcs = lrcContent.split("\n"); //把整个歌词拆分成一句歌词一句歌词的数组
  for (let i in lrcs) {
    lrcs[i] = lrcs[i].replace(/(^\s*) | (\s*$)/g, ""); //去除前后空格
    var timeOrigin = lrcs[i].substring(
      lrcs[i].indexOf("[") + 1,
      lrcs[i].indexOf("]")
    ); //取[]之间的内容：时间
    var timeProcessed = timeOrigin.split(":"); //分离 : 前后的文字
    if (!isNaN(parseInt(timeProcessed[0]))) {
      var time =
        parseFloat(timeProcessed[0]) * 60 + parseFloat(timeProcessed[1]); //将时间转化成秒
      var text = lrcs[i].substring(lrcs[i].indexOf("]") + 1); //将每句歌词对象插入歌词数组
      lyrics.push({
        time: time,
        text: text,
      });
    }
  }
}
```

有了歌词的对象数组后，我们可以根据歌的播放时间来决定轮播哪句歌词, 监听 audio 的 timeupdate 事件来更新歌词和样式。lrcContent 形参是原生歌词的文本字符串，将其放在 textarea 里面，但是 display 是 none。

```js
audioPlay.addEventListener("timeupdate", lyricsChange); //当歌曲的currentTime发生改变时，要处理相应dom的样式和歌词
function lyricsChange() {
  //更新歌词
  if (lyrics.length == 0) {
    //如果歌词为空，则创建歌词对象数组
    let lrcContent = document.getElementById(`${audioPlay.dataset.name}`)
      .innerHTML;
    createLyrics(lrcContent);
  }

  if (j == lyrics.length) return; //在页面显示歌词，j是歌词数组的索引
  let curTime = this.currentTime;
  console.log(curTime);
  console.log(j);
  console.log(lyrics[j].time);
  if (parseFloat(lyrics[j].time) <= curTime) {
    console.log(lyrics[j].text);
    lrcRow.innerHTML = lyrics[j].text;
    j++;
  }
}
```

每一首歌开始播放的时候，都应该初始化歌词数组，然后把新歌的歌词写到数组里

```js
audioPlay.addEventListener("durationchange", lyricInit); //监听切歌事件
function lyricInit() {
  //初始化歌词数组和索引
  lyrics = [];
  j = 0;
}
```

## 按钮状态更新

因为这些按钮的状态都是根据 audio 的播放/暂停来决定的，所以我们可以监听 audio 的开始播放和暂停事件来完成操作。

```js
audioPlay.addEventListener("pause", styleChange); //监听歌曲暂停事件，处理样式
audioPlay.addEventListener("play", styleChange); //监听歌曲播放事件，处理样式
function styleChange() {
  let icon;
  //更新当前播放按钮的样式
  if (audioPlay.paused) {
    icon = "&#xe645;";
    run("rgb(100,100,250)");
  } else {
    icon = "&#xe615;";
    run("rgb(100,260,170)");
  }
  const toggle = toggles.find(
    (toggle) => toggle.dataset.title == audioPlay.dataset.name //找到当前audio对应的toggle
  );
  toggle.innerHTML = icon;
  toggles
    .filter((toggleLeft) => toggleLeft !== toggle)
    .forEach((toggleLeft) => {
      toggleLeft.innerHTML = "&#xe645;"; //其他audio对应的toggle变成待播放
    });
}
```

## 背景音频可视化

调用 audioContext API 可以获得 audio 源的频率数据和波形数据。

```js
var audioCtx = new (window.AudioContext || window.webkitAudioContext)(); //创建一个audioCtx的接口
console.log(audioCtx);
audioPlay.crossOrigin = "anonymous"; // 设置允许跨域,audioContext连接对跨域有限制
var analyser = audioCtx.createAnalyser(); //创建一个分析器，分析器也是一个audioContent节点，用于显示音频时间和频率数据
source = audioCtx.createMediaElementSource(audioPlay); //创建源, 在这是正在播放的音乐
source.connect(analyser); //将源audioContext与分析器连接起来
var distotion = audioCtx.createWaveShaper(); //创建一个表示非线性失真的waveShaperNode，可以给音频添加失真效果。
analyser.connect(distotion); //分析器与失真器连接起来
distotion.connect(audioCtx.destination); //连接音频上下文的最终节点输出
//现在需要通过分析器analyser抓取音频数据，analyser将使用快速傅里叶变换在特定频域中捕获音频数据，具体的频域通过analyer.fftSize设置，默认是2048，并且fifSize的值必须是2的幂。
//analyser.fftSize = 20486;
analyser.fftSize = 256;
//新创建一个数组用来缓存抓取到的每一次频率数据，由于接下来的抓取数据方法返回的数据比较特别，标准的js数组满足不了，所以根据要抓取数据的内容，选择Float32Array还是Uint8Array。
var bufferLength = analyser.frequencyBinCount; //设置缓冲区的大小，frequencyBincount是ffSize的一半
var dataArray = new Uint8Array(bufferLength); //如果用的是analyser.getByteTimeDomainData的方法抓取波形数据 byte整形数据，占1个字节
//抓取频率数据我们需要Analyser.getFloatFrequrencyData() 和 analyser.getByteFrequencyData()方法
//抓取波形数据我们需要analyser.getFloatTimeDomainData() 和 analyer.getByteTimeDomainData()方法
//analyser.getByteTimeDomainData(dataArray); //此时的波形数据已经存到dataArray数组里了
//analyser.getByteFrequencyData(dataArray); //此时的频率数据已经存到dataArray数组里了
```

dataArray 经过分析器的处理，获得的是那一帧的波形或者频率数据，要实现可视化，我们需要在画布中对每一帧数据都可视化。

```js
function drawBgBar() {
  var drawVisual = requestAnimationFrame(drawBgBar); //通过requestAnimationFrame每秒执行60次drawBgBar函数。
  analyser.getByteFrequencyData(dataArray); //获得频率数据
  console.log(dataArray);
  canvasBgCtx.fillStyle = "black";
  canvasBgCtx.fillRect(0, 0, WIDTH, HEIGHT); //讲画布背景变黑

  var barWidth = WIDTH / bufferLength; //把画布的宽度除以数组长度获得每个数组应该占屏幕的位置
  var barHeight;
  var x = 0;
  for (var i = 0; i < bufferLength; i++) {
    barHeight = dataArray[i] / 2;

    var linGrad2 = ctx.createLinearGradient(
      x,  //柱状图的左上横轴坐标
      HEIGHT - barHeight / 2,//柱状图的左上纵轴坐标
      x + barWidth, //柱状图下方横坐标
      HEIGHT //柱状图下方纵坐标，就是屏幕最低点
    ); //柱状图的颜色是渐变的
    linGrad2.addColorStop("0", "#f00"); //渐变颜色的开始
    linGrad2.addColorStop("0.5", "#ff0");
    linGrad2.addColorStop("1.0", "#0f0");
    canvasBgCtx.fillStyle = linGrad2;// 渐变填充柱状图矩形
    canvasBgCtx.fillRect(x, HEIGHT - barHeight / 2, barWidth, barHeight);

    x += barWidth + 1;
  }
}
```
