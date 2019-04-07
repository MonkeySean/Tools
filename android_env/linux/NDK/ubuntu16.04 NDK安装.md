# ubuntu16.04 NDK安装

- 步骤一  安装包下载

[ndk](https://developer.android.google.cn/ndk/downloads/) 

- 步骤二  安装软件

  在想安装的目录直接运行sudo ./android-ndk-r10e-linux-x86_64.bin解压到当前目录

- 步骤三 设置环境变量

  在需使用NDK的用户的home目录内，找到隐藏的.bashrc文件[^1]，命令行输入gedit ~/.bashrc,在最后加上

  export ANDROID_NDK_HOME=/home/sxy/Android/Sdk/android-sdk-linux

  export PATH=$PATH:${ANDROID_NDK_HOME}

  然后保存，退出。输入 source ~/.bashrc使该文件参数更新

  [^1]: 或者修改/etc/profile也行，该文件是所有用户共享的环境变量

- 步骤四 检查是否安装好

  在命令行输入ndk-build,最好拿一个jni工程直接编译一下就OK了