### JavaVM及JNIEnv
* 本质是指向函数表指针的指针,以及一个可以通过这个函数表间接访问对应的jni函数的成员函数
* JNIEnv是线程局部存储的,线程间无法共享, 需要共享JavaVM取得该线程下的Env(GetEnv)
* 在c/c++两者的声明不同, 头文件会提供不同的类型定义

### 线程
* 任何一个新创建的线程都需要AttachCurrentThread连接到JavaVM, 之后才能通过Env调用jni函数
* 连接过的线程必须通过JNI调用DetachCurrentThread


### jclass, jmethodID, jfieldID

访问一个对象的字段
