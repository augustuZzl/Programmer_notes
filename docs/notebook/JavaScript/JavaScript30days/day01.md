第一天的挑战比较简单，点击键盘，给屏幕上对应的键改变样式，并播放声音。

先看自己的思路：

```javascript
// keyCode 键盘码
function play(keyCode){
	// 获取屏幕上对应的键
    const selectKey = document.querySelector("div[data-key='" + keyCode + "']");
    // 获取对应的播放器
    const audio = document.querySelector("audio[data-key='" + keyCode + "']");
    // 如果没有，返回
    if(!selectKey){
        return;
    }
    // 选中的键添加样式
    selectKey.classList.add("playing");
    // 播放音频
    audio.play();
};
// 键盘落下事件
document.addEventListener('keydown', function(e){
    play(e.keyCode);
});
// 键盘抬起事件
document.addEventListener('keyup', function(e){
    const selectKey = document.querySelector("div[data-key='" + e.keyCode + "']");
    // 移除对应的样式
    selectKey && selectKey.classList.remove("playing");
});
```

- querySelector() 用于查询匹配选择器的第一个元素，querySelectorAll() 匹配所有元素
- 使用选择器获取元素时，一直用这种复杂的拼接：querySelector("audio[data-key='" + keyCode + "']")，看了官方示例，才发现有这种写法：querySelector(\`audio[data-key="${keyCode}"]\`)。这是在 ES6 中新增的**模板字符串**。
- 元素的样式有这样一个属性：classList，可以使用 add()、remove() 添加或删除类样式
- 注意：调用对象的方法之前，要判断是否为空

官方示例：

```javascript
function removeTransition(e) {
    // 如果是不是 transform 变换，返回
    if (e.propertyName !== 'transform') return;
    // e.target 触发此事件的元素
    e.target.classList.remove('playing');
}

function playSound(e) {
    const audio = document.querySelector(`audio[data-key="${e.keyCode}"]`);
    const key = document.querySelector(`div[data-key="${e.keyCode}"]`);
    if (!audio) return;

    key.classList.add('playing');
    audio.currentTime = 0;
    audio.play();
}
// Array.from() 将类数组对象转为数组
const keys = Array.from(document.querySelectorAll('.key'));
// 每个按键添加 css 变换结束的事件
keys.forEach(key => key.addEventListener('transitionend', removeTransition));
window.addEventListener('keydown', playSound);
```

- 与官方示例不同的是，我的移除样式事件放在 keyup 中，只要不松开，就一直选中，而官方放在 transitionend 中，选中后自动释放，也许更符合实际吧！
- Array.from() 将类数组对象或者可遍历对象转为数组，基本的要求就是要具有 length 属性