# CSS3新特性

1. **过渡属性** transition 要改变的属性，花费时间，效果曲线(默认ease)，延迟时间（默认0）
2. **动画属性** animation 动画名称，一个周期花费时间，运动曲线（默认ease），动画延迟（默认0）,播放次数（默认1）,是否反向播放动画（默认normal），是否暂停动画（默认running）

3. **圆角属性：**CSS-border-radius

​	border-radius 属性可以接受一到四个值。规则如下：

*四个值 - border-radius: 15px 50px 30px 5px;*（依次分别用于：左上角、右上角、右下角、左下角）：

*三个值 - border-radius: 15px 50px 30px;*（第一个值用于左上角，第二个值用于右上角和左下角，第三个用于右下角）：

*两个值 - border-radius: 15px 50px;*（第一个值用于左上角和右下角，第二个值用于右上角和左下角）：

*一个值 - border-radius: 15px;*（该值用于所有四个角，圆角都是一样的）：

4. **引入RGBA**

5. **背景色渐变**

   ```css
   #grad{
   	background-image: linear-gradient(to right, red, yellow)
   }
   ```

6. **盒子阴影**

   ```css
   div{
   	box-shadow: 10px 10px 5px #888
   }
   ```

7. **盒子新特性**

```css
box-sizing:border-box;
```

这是干嘛的捏，它可以将边框和内边距都算进盒子大小里，妈妈再也不用担心你不会算布局啦

8. **justify-content**

在容器中央对齐弹性项目

```css
div{
	display: flex;
	justify-content: center;
}
```

9. **overflow: scroll** 溢出的部分具有滚动条

```css
div{
	width: 150px;
	height: 150px;
	overflow: scroll;
}
```

10. **transform** （旋转）

```css
div{
	transform: rotate(7deg)
}
```

11. 准许文字换行

```css
p.test{
	word-wrap: break-word;
}
```

12. 弹性布局