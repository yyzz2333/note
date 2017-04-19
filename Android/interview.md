### 架构 从桑往下
* app
* framework: 提供api(四大组件,通知,view等)
* 系统运行库和Android运行环境
    * native libraries: C/C++库,被不同组件使用(Bionic,音频,Surface,Webkit,SGL图形引擎,3D openGL/ES,SQLite)
    * runtime: dalvik, art, core libraries
* HAL硬件抽象层: 由于GPL协议, 修改linux内核需开源, 实际上对驱动代码留了后门, 可以不接触硬件而访问(显示器驱动,声音,相机,GPS,GSM)
* kernel: linux2.6
    * 安全性,内存管理,进程管理,网络协议栈,驱动模型. 
    * 修改部分: Binder, 电源管理


### Activity
broadcast中无法启动Activity, 需要设置NEW_TASK的flag, 归结来说只有继承与contextWrapper的组件才有task, 谁启动就在谁的task中, 所以非contextWrapper子类都需setFlag, 新建一个任务栈启动

* SingleTop: 在栈顶就复用(适合从外界多次跳转的一个界面,或者接收通知启动的内容显示页面)
* SingleTask: 栈内保持只有一个实例. 同一应用会清空之上的activity, 不同应用启动会将后台task切到前台, 不存在则创建(适合作为程序入口点)
* SingleInstance: 适合与应用分离开的页面


### handler
* looper.prepare()
    * `new looper,queue` , 对threadLocal进行检查, 保证只有一个对应looper
    * 主线程不需要prepare, 是因为在ActivityThread中创建过
* ThreadLocal: java提供的用于保存同一个进程中不同线程数据的一种机制, 每个进程都有一个 `ThreadLocalMap` 成员变量, 内部采用 `WeakReference` 保存, 而key即为 `ThreadLocal` 内部的Hash值
* Looper.loop: 循环调用 `queue.next()` 获取消息, 无消息时阻塞, 采用了 `epoll` 的I/O多路复用机制, 取到一个消息的时候会返回
* message.target.dispatchMessage: 从loop中取到消息, 会调用Message内部的Handler引用并分派事件


### view绘制

1. view树绘制开始于ViewRootImpl的performTraversals(), 根据之前状态, 判断是否需要重新计算measure, layout, draw
![](https://github.com/hadyang/interview/blob/master/android/draw_1.jpg)

2. measure
![](https://github.com/hadyang/interview/blob/master/android/draw_2.jpg)

3. layout
![](https://github.com/hadyang/interview/blob/master/android/draw_3.jpg)

4. draw
![](https://github.com/hadyang/interview/blob/master/android/draw_4.jpg)
