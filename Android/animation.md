### View动画

set标签, 对应AnimationSet, 包含若干个动画, 内部也可嵌套其他AnimationSet

res/anim/

* interpolator 插值器, 影响动画速度
* shareInterpolator set中动画是否和set共享同一个插值器(set没有指定的话, 那么子动画就需要单独指定/或使用默认值)


* fillAfter 动画结束后view是否停留在结束位置, true停留
* TranslateAnimation
* ScaleAnimation
* RotateAnimation
* AlphaAnimation


加载动画
1. 加载xml:`AnimationUtils.loadAnimation(context, R.anim.xxx)`
2. 动态: `new AlphaAnimation()`
3. 监听:AnimationListener


### 帧动画

对应animation-list标签, 对应AnimationDrawable类

res/drawable/

启用动画: 类似与TransitionDrawable
* `view.setBgRes`
* `getBg`
* `anim.start()`


### LayoutAnimation

作用在viewGroup, 给其子元素加上出场动画, xml中layoutAnimation属性指定.
动态设置使用LayoutAnimationController

* animation 子元素的动画效果
* delay 动画delay = duration*delay, 第一个子元素延迟delay, 第二个delay*2
* animationOrder: normal, reverse, random


### Activity切换动画

Fragment切换动画, FragmentTransaction.setCustomAnimations(),tip:不能使用属性动画

### property
res/animator

* animatorSet
    * 标签对应set
    * ordering: together, 子动画同时; sequentially, 先后顺序
* ValueAnimator
    * animator标签
    * valueFrom 属性的起始值
    * valueTo 属性的结束值
    * startOffset 动画的延迟时间
    * repeatCount 动画重复次数, 默认0, -1无限循环
    * repeatMode 动画重复模式
    * valueType 表示propertyName所自定的属性的类型, 有intType和floatTyp两种;颜色不需要指定
* ObjectAnimator
    * objectAnimator标签 
    * propertyName 表示属性动画作用对象的属性的名称

加载
1. AnimatorInflater.loadAnimator(context, R.anim.xxx), setTarget, start
2. ObjectAnimator.ofFloat(target, propertyName, values...)

TimeInterpolator: 根据time流逝百分比,计算出当前属性值改变的百分比, 内置有LinearInterpolator(匀速), AccelerateDecelerateInterpolator(两头慢,中间快), DecelerateInterpolator(越来越慢)

TypeEvaluator: 类型估值算法, 根据当前属性改变的百分比来计算改变后的属性值, 内置有IntEvaluator, FloatEvaluator, ArgbEvaluator
* evaluate(float fraction, T startValue, T endValue)
* 估值小数(对应与Interpolator中的属性值改变百分比),开始值,结束值


监听器

* AnimatorUpdateListener, 对应AnimatorListenerAdapter
* AnimatorListener每播放一帧, 会被调用一次(系统默认10ms一帧, duration=300ms)


对任意属性做动画需满足:
1. view需有该属性的get, set方法
2. set方法对属性所做的改变都能反映出来, 比如UI的改变


实现方式:
1. wrapper类包装原始对象, 间接提供get, set方法
2. valueAnimator本身不作用于任何对象, 直接使用是没有任何动画效果的. 可对一个值做动画, 监听其动画过程, 在动画中修改对象的属性值. 本质是 AnimatorUpdateListener + TypeEvaluator, 每一帧获得当前动画的进度值`animator.getAnimatedValue`, 在获得进度比例 `animator.getAnimatedFraction`, 最后用Evaluator进行计算
