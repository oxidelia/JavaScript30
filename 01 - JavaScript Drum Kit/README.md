## 实现效果
按键并响应不同声音，同时变换按键的样式，按键结束，页面恢复。
## 逻辑梳理
1.不同键对应各自独立的keycode，利用keycode，将键、音绑定。
	因此键、音元素要设置相同（对应）的属性，当监听到键盘事件时，通过相应的keycode找到对应键，为键添加样式、并播放对应键的声音。
2.通过为元素添加class类，实现按键效果。
3.页面随动画结束恢复原本样式，移除class类。利用ontransitionend事件处理函数监听transtionend事件，同时需要用transtion控制动画效果。

## 代码实现：
1、键盘监听事件
监听按键的发生，获得键的keycode

```
 window.addEventListener('keydown', playSound)
```

2、利用keycode找到对应键与音效
html：键和音效通过设置相同的自定义属性绑定起来

```
<div data-key="65" class="key">
      <kbd>A</kbd>
      <span class="sound">clap</span>
</div>
<audio data-key="65" src="sounds/clap.wav"></audio>
```

js：查找对应键、音效

```
    const audio = document.querySelector(`audio[data-key="${e.keyCode}"]`);
    const key = document.querySelector(`div[data-key="${e.keyCode}"]`);
```
3、为发生键盘事件的键添加新类、播放音频

```
key.classList.add('playing');
audio.play();
```
4、按键结束，取消样式效果
```
key.ontransitionend=()=>{
      key.classList.remove('playing')
    }
```
5、额外需要注意的

5.1 实现连续按键连续播放：
`audio.currentTime = 0`
设置每次播放的时间为音频起始点，确保按键音效的即时触发，不受音频文件的播放状态的影响。
5.2 当监听到页面内未设置音效的键被按下时，跳出语句，不执行后面的播放操作。
`if (!audio) return;`
5.3 针对key所对应元素身上发生的多个transition事件
教程解法是：在每一个key身上绑定transtionend监听事件，通过匹配transtionend事件的propertyName，当propertyName== 'transform'时才移除样式，这是因为只需关心transiton过渡时间最长的事件即可。
个人解法：在keydown事件处理函数中，直接通过`key.ontransitionend` 事件来处理过渡效果结束。
对比：
“使用 `key.ontransitionend` 事件处理是一时性的，只会在第一次 `transitionend` 事件发生时生效。如果同一个按键被多次按下，之后的 `transitionend` 事件可能不会触发，因为事件处理器只能被设置一次。”——chatgpt

## js语法
1、element = document.querySelector(selectors);
2、模板字符串
`string text ${expression} string text`
用法：const audio = document.querySelector(`audio[data-key="${e.keyCode}"]`);
3、监听transitionend事件的处理函数
```
target.ontransitionend = Function
```
## css语法
1.水平垂直居中：
flexbox
这个方法适用于将容器的子元素居中。确保你的盒子是 `.container` 的子元素。
父元素未占满视口时，设置`min-height: 100vh;`，确保它至少占满整个视口高度。
```
.container {
  display: flex;
  align-items: center;
  justify-content: center;
}
```
flex: 1保持子元素等比例缩放
相当于：
	flex-grow: 1;它们将等分任何额外的空间。
	flex-shrink: 1;它们将等分任何缺少的空间。
	flex-basis: 0%;表示所有项目具有相等的初始尺寸。

2.设置背景透明度
background：rgba（0，0，0，0~1）设置透明度
background-size: cover;保持图片纵横比、不变形
3.文本字符设置
text-transform: uppercase;文本全大写
letter-spacing：.1rem 设置文本字符间距
4.属性选择器
`audio[data-key="${e.keyCode}"`：选择`<audio>`中`data-key`属性为e.keyCode值的元素
5.缩放元素
transform: scale(x[, y]);
- `x` 表示水平方向的缩放比例。
- `y` 表示垂直方向的缩放比例。如果省略，将默认为与水平方向相同的值。
6.css动画过渡
```
transition: <property> <duration> <timing-function> <delay>;
```