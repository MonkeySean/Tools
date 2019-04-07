# ubuntu16.04 SDK安装

- 步骤一 安装包下载zip包，sdk_r24

  [官网需翻墙](https://developer.android.com/studio/index.html)

  [无需翻墙](http://www.androiddevtools.cn/)

- 步骤二 安装软件

  把安装包解压到想安装的目录

- 步骤三 设置环境变量

  在需使用SDK的用户的home目录内，找到隐藏的.bashrc文件[^1]，命令行输入gedit ~/.bashrc,在最后加上

  export ANDROID_SDK_HOME=/home/sxy/Android/Sdk/android-sdk-linux

  export PATH=$PATH:${ANDROID_SDK_HOME}

  export PATH=$PATH:${ANDROID_SDK_HOME}/tools

  export PATH=$PATH:${ANDROID_SDK_HOME}/platform-tools

  然后保存，退出。输入 source ~/.bashrc使该文件参数更新

  [^1]: 或者修改/etc/profile也行，该文件是所有用户共享的环境变量

- 步骤四 添加32位库

  sudo apt-get install lib32z1 lib32ncurses5  lib32stdc++6  libc6-dev-i386

- 步骤五 检查是否安装好

  在终端命令行输入android，会打开Android SDK Manager，表示安装成功

  然后用打开的界面，下载

  ​      Android SDK Tools

  ​    Android SDK Platform-tools

  ​    Android SDK Build-tools (最高版本，最好都下载)  