### Android.mk
* LOCAL_PATH := $(call my-dir)    //指定当前文件夹
* include $(CLEAR_VARS)   //指定让GNU MAKEFILE 清除除LOCAL_PATH以外的LOCAL_MODULE,LOCAL_SRC_FILES,LOCAL_STATIC_LIBRARIES等
* include $(BUILD_xxx)  //static, shared, executable, 此处代表一个编译模块的结束
* LOCAL_C_INCLUDES += $(LOCAL_PATH)/src //额外的c/c++编译头路径
* LOCAL_CC  //指定c编译器
* LOCAL_CERTIFICATE //签名认证 
* LOCAL_CFLAGS += -DLIBUTILS_NATIVE=1 //为c/c++编译器定义额外的标志,如宏定义
* LOCAL_LDFLAGS //传递额外的参数给连接器
* LOCAL_LDLIBS += -lcurses //为可执行程序或库的编译指定额外的库, 指定库以-lxxx格式
* LOCAL_MODULE //生成的模块名称
* LOCAL_PACKAGE_NAME //可执行的名
* LOCAL_SHARED_LIBRARIES //可连接的动态库
* LOCAL_SRC_FILES //编译源文件
* LOCAL_STATIC_LIBRARIES 可链接静态库
*
