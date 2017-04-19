### view的几个参数
* top	left	right	bottom 左上角和右下角的横纵坐标
*  x	y	transitionX	transitionY
*  x = left + transitionX
*  默认transition为0
*  都有get方法

 MotionEvent
* x, y 相对于当前view左上角的xy坐标
* rawX, rawY相对手机屏幕左上角的xy坐标


TouchSlop
* 系统能识别出的被认为是滑动的最小距离, 常量, 和设备相关.
* 源码所在: frameworks/base/core/res/res/values/config.xml中的`config_viewConfigurationTouchSlop`属性
* 获取: `ViewConfiguration.get(context).getScaledTouchSlop`

VelocityTracker速度追踪
* 追踪当前event的手指滑动速度
    ```
    VelocityTracker.obtain()
    velocityTracker.addMovement(event) 
    ```
* 获得速度
    ```
    velocityTracker.computeCurrentVelocity(msec)
    tracker.getXVilocity()
    ```
   **tip:100ms时间间隔移动的10像素, 则速度为10像素/每100ms**
* 释放
    ```
    tracker.clear()
    tracker.recycler()
    ```


GestureDetector
* onGestureListener
    * onDonw
    * onShowPress 按下尚未松开或拖动
    * onSingleTapUp 轻触后松开, 伴随ACTION_DOWN触发
    * onScroll 按下并拖动, 1个down+多个move
    * onLongPress 
    * onFling 快速滑动, down*1+move*n+up*1
* onDoubleTapListener
    * onDoubleTap 双击, 不和onSingleTapConfirmed共存
    * onSingleTapConfirmed 严格的单击, 触发后就不能再紧跟着另一个单击行为
    * onDoubleTapEvent 发生了双击, 会被回调多次



 view滑动三种方式:
*  scrollTo/scrollBy
*  动画给view施加平移效果
*  改变layoutparams使得view重新布局


Scroll重要参数
*  mScrollX = view左边缘 - view内容左边缘
*  mScrollY
*  mScrollX>0, 内容左移
*  mStartX, mCurrX, mFinalX


scollTo/scrollBy区别
*  by本质调用to	
*  by基于当前位置的相对滑动, 即每次滑动都会改变当前位置
*  to基于所传递参数的绝对滑动
*  两者都是改变view内容的位置, 而不能改变view在布局中的位置
*  改变view位置调用offsetLeftAndRight()

 Scroller
*  `startScroll()`内部仅进行赋值操作
*  `OverScroller`是对Scroller的扩展, 其中fling方法支持滑动到终点后并超出一段距离后返回, `springBack()`将指定的点平滑滚动到指定的终点上
*  构造需要context, interpolator, boolean flywheel(是否支持在滑动过程中,有新的`fling()`调用是否累加加速度)
*  computeScrollOffset根据`SCROLL_MODE`和`FLING_MODE`分支计算动画是否结束, 并实时记录坐标, 加速度, 时间等
*  `fling()` 存储startX, velocityX, minX, maxX

弹性滑动实现
1. Scroller配合computeScroll
*  startScroll
*  invalidate使view重绘
*  view的draw()会调用computeScroll()
*  computeScroll()会向Scroller要当前的scrollerX, scrollerY: `mScoller.computeScrollOffset()为true, 触发scrollTo`
*  触发scrollTo,滑动到新的位置
*  再执行postInvalidate()进行第二次重绘
*  重复compute, scrollTo实现弹性
2. animator配合scrollTo
* 声明一个属性动画, 不指定target
* 监听动画 `AnimatorUpdateListener`
* 获得动画每一帧的所在的百分比 `animator.getAnimatedFraction`
* 调用`scrollTo(delta*fraction, 0)`
3. 延时策略
* handler/view.psotDelayed/thread.sleep等延时发送消息
* 在while循环中不断滑动,发送消息

### view 事件分发
结论:
1. 对于根View, 事件产生后, 首先调用dispatch, 如果Intercept返回true, 就将事件交给此view处理, 即调用touch, 返回false, 则传递给子元素, 执行子元素的dispatch
1.2. 当view需要处理事件是, 如设置`onTouchListener`, 会先被回调, 根据`onTouch`返回值确定是否执行`onTouchEvent`, 而设置了`onClickListener`, 则在`onTouchEvent`内部会先调用. **优先级:onTouch>onClick>onTouchEvent**
1.3. view一旦拦截, 同一事件序列所有事件都会直接交给他处理, 并且`onIntercept`不会再被调用
1.4. view开始处理事件, 但未消耗`ACTION_DOWN`(`onTouchEvent`返回false), 则后续事件都不会交给他处理, 并交由父元素处理, 即调用父元素的`onTouchEvent`
1.5. view消耗了down, 其他未消耗, 此时这个点击事件会消失(仍可以接收到), 父元素的touch也不会被调用, 最终会传递给Activity处理
1.6. viewGroup默认不拦截任何事件, `onIntercept`默认返回false
7. view没有`onIntercept`, 直接会调用touch
8. view的`onTouchEvent`默认都会消耗事件(返回true), 除非`clickable`和`longClickable`同事为false

### viewGroup
#### Intercept
```	
        // Check for interception.
	    final boolean intercepted;
	    if (actionMasked == MotionEvent.ACTION_DOWN|| mFirstTouchTarget != null) {
	        final boolean disallowIntercept = (mGroupFlags & FLAG_DISALLOW_INTERCEPT) != 0;
			if (!disallowIntercept) {
				intercepted = onInterceptTouchEvent(ev);
				ev.setAction(action); // restore action in case it was change
			} else {	
            intercepted = false
		}
    	} else {
	    	// There are no touch targets and this action is not an initial down
		    // so this view group continues to intercept touches.
    	    intercepted = true;
    	}

```
* 事件为DOWN或`mFirstTouchTarget!=null`,才有拦截的机会
* mFirstTouchTarge会被赋值指向子元素, 当viewGroup不拦截事件,且子view处理事件成功
* 反过来,当viewGroup拦截了,`mFirstTouchTarget != null`就不成立,后续的MOVE,UP, 不会调用intercept,且同一序列的其他事件,同一系列事件都默认交给他处理	
* 特殊情况是FLAG_DISALLOW_INTERCEPT这个标志,是子view通过requestDisallowInterceptTouchEvent方法来设置的,
  * 接收到DOWN时会重置此标志位
  * 所以子view设置此标志后viewGroup只能拦截DOWN事件
* 总结:1.一旦view拦截某事件后,后续同一系列事件都交个他处理,并且intercept不会再被调用
* 总结:2.Intercept并不是每次事件都被调用,dispatch才是


#### dispatch
* 遍历viewGroup所有子元素,判断能否接收到事件(标准:是否在播放动画,事件坐标是否落在子元素区域内)
* 调用子元素dispatch返回true,会调用addTouchTarge(),之中对`mFirstTouchTarget`进行赋值,并跳出遍历
* `mFirstTouchTarget`是单链表结构,为null,viewGroup会默认拦截同一序列所有事件,会调用super.dispatchTouchEvent,即view
* 总结:view一旦开始处理事件,没有消费调DOWN,即onTouchEvent返回false,则同一事件序列的其他事件都不会交给他处理,且重新交给父元素处理

### view
* view无子元素,必须自己处理
* 判断onTouchListener != null, onTouch返回true,则onTouchEvent就不会被调用
* onTouchEvent中view处于不可用状态依然会消耗点击事件
* view设置代理,会执行TouchDelegate的onTouchEvent
* UP事件时,会触发performClick,若设置了onClick则内部会调用


总结:
1. 一个事件序列只能被同一个view拦截且消耗,且一旦拦截,后续事件的onIntercept就不会被调用
2. 当view处理事件,但不消耗DOWN时(onTouchEvent返回false),则事件会重新交给父元素处理
3. viewGroup默认不拦截
4. view无拦截方法,一旦传递给他就必然调用onTouchEvent



### 滑动冲突

* 判断滑动方向:夹角/距离差/速度差
* 外部拦截法:父容器先
    * 重写Intercept
    * DOWN,返回false,返回true后续的事件就无法向下分发
    * MOVE,需要就拦截
    * UP,必须false,true的话,会导致子view无法接受UP事件,也就无法触发onClick
* 内部拦截法:父不拦截任何事件,都交给子,子需要就消耗,否则就交给父进行处理
    * 重写子的dispatch
    * DOWN,requestDisallowInterceptTouchEvent(true)
    * MOVE,父需要request(false)
    * 父元素需要拦截除了DOWN以外的所有事件,这样,子元素调用request(false)父元素才能拦截
    * DOWN事件是不受FLAG_DISALLOW_INTERCEPT标记为控制,父容器一旦拦截了DOWN,所有其他事件就不能传递给子元素了

