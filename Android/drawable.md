* getIntrinsicWidth, getInstrinsicHeight

### Bitmap

可直接引用原始图片, 通过xml方式, 可设置更多效果
* antialias 抗锯齿
* dither 抖动效果, 高质量图片在低质量屏幕也有很好的显示效果
* filter 过滤, 拉伸压缩时保持显示效果
* gravity 图片`<`容器, 此属性进行定位
* mipMap 纹理映射, 默认false
* tileMode 平铺模式 
    * disable 默认,关闭
    * repeat 简单的xy平铺
    * mirror 镜面平铺
    * clamp  图片四周的像素会扩散到周围区域
    

### Shape
* ring 有内环innerRadius, 圆环厚度thickness属性
* gradient和solid互斥, 只能存在一个
* gradient的angle必须是45°的倍数
* type 渐变的类别, linear, radial径向渐变, sweep扫描线渐变
* 只有在radial是, gradientRadius 渐变半径有效
* useLevel 一般为false, 当Drawable作为StateListDrawable使用时为true

### Layer

xml标签是layer-list

* top, right 之类就是drawable相对于原始view的偏移量, top对应的是往下偏移

### StateList --- selector

* constantSize 固有大小, 状态的切换会切换不同的Drawable, 不同Drawable有不同的固有大小, true表示固有大小取所有Drawable的固有大小最大值, false会随着状态改变,默认false
* dither 抖动
* variablePadding false表示padding是所有Drawable的padding的最大值, true会随着状态改变而改变. 默认false

 
### LevelListDrawable

对应于xml中的level-list标签

* 指定maxLevel和minLevel, 0~10000, 指定level来切换不同Drawable 
* view有setLevel切换背景, ImageView有setImageView切换前景

### Transition

对应transition标签, 实现两个Drawable之间淡入淡出的效果

* 给view设置bg
* view.getBackground
* transition.startTransition(msec)


### Inset

内嵌效果, 设置padding

### Scale

对应于scale标签

* scaleGravity 相当于shape中gravity, 效果就是定位, 主要是位置+是否改变大小(填充, 裁剪之类)
* scaleWidth 缩放比例
* scaleHeight
* Drawable默认level=0, 源码中对于0的level会不draw, 所以必须手动scale.setLevel(1)

### Clip

对应与clip标签, 根据当前level来裁剪另一个Drawable

* clipOrientation 裁剪方向, xy,
* gravity 与clipOrientation以前作用, 代表裁剪从哪开始(经常是放在容器顶部,从底部开始)
* level, xml中不能设置, 0表示完全裁剪, 10000表示不裁剪, 动态setLevel()
