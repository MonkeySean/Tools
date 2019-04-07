# win10 NDK安装

- 步骤一 安装包下载

  [ndk](https://developer.android.google.cn/ndk/downloads/)

- 步骤二 安装软件

  把安装包解压到想要安装的路径

- 步骤三 设置环境变量

  ​	打开环境变量编辑界面，在系统变量里，新建变量ANDROID_NDK_HOME,其值设置为NDK的安装目录，如D:\android\android-ndk-r16b

  ​	然后在Path变量中，增加%ANDROID_NDK_HOME%

- 步骤四 检查是否安装好

  ​	快捷键WIN+R，CMD，进入C盘，输入ndk-build，提示路径错误，这就对了。要是有jni工程，在该jni路径下输入ndk-build，则会编译生成APP。