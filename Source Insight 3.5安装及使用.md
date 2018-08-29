[TOC]

# Source Insight 3.5 安装及使用

## 安装

1. 百度搜source insight 3.5破解,获取资源，我是在这里获取的资源

   http://www.wmzhe.com/soft-31843.html

2. 解压，获得2个应用文件；1个是si的安装文件，1个是生成注册码的软件

3. 双击Si3572Setup，开始安装

4. 标准的安装软件程序，同意，下一步......要注意安装路径，建议路径全用英文名，我的安装路径

   D:\software install directory\work\source insight

![install_pic0](E:\work\mBlog\source insight 3.5\install_pic0.PNG)

5. 接着下一步......然后就到了给自己取名字的时候了，2行都要填，然后点OK

![install_pic1](E:\work\mBlog\source insight 3.5\install_pic1.PNG)

6. 然后就到了安装语言库的界面，我没装，直接Skip This跳过

![install_pic2](E:\work\mBlog\source insight 3.5\install_pic2.PNG)

7. 终于装好了，然而，立马出来个对话框，提示你没有正确的serial number，然后关闭软件重新打开，又是弹出对话框，软件关闭......就是生成了序列号也没法输啊！为了解决这点，看下一步

8. 点左下角菜单，运行，或按（win+R），输入regedit，确定，打开注册表编辑器。在树形图中根据自己操作系统位数，找到相应的路径

```
32位系统
HKEY_LOCAL_MACHINE\SOFTWARE\Source Dynamics\Source Insight\3.0\Install
64位系统
HKEY_LOCAL_MACHINE\SOFTWARE\Wow6432Node\Source Dynamics\Source Insight\3.0\Install
```

![install_pic3](E:\work\mBlog\source insight 3.5\install_pic3.PNG)

9. 我的是64位的，找到后选中install，右边显示文件中的信息

10. 找到步骤2中提到的注册码生成软件，运行生成注册码，复制

11. 在步骤9中，注册表编辑器，双击Serial Number（红色选中部分），将注册码粘贴替换之前的，点确定。然后就可以打开source insight啦。

## 解决中文乱码

1. 下载插件，我是在这里下载的。siplus就是的。

   https://blog.csdn.net/nyist327/article/details/39935379

2. 找到source insight 3.5安装路径，并解压到当前文件，如我的路径是

   D:\software install directory\work\source insight\Source Insight 3

   ![language0](E:\work\mBlog\source insight 3.5\language0.PNG)

   ![language1](E:\work\mBlog\source insight 3.5\language1.PNG)

3. 删掉sihook.dll，大功告成，打开source insight试试

   ![language2](E:\work\mBlog\source insight 3.5\language2.PNG)

## 头文件和源文件快捷键切换

1. 打开source insight
2. 打开工程，选择Base，OK

![switch_h_c0](E:\work\mBlog\source insight 3.5\switch_h_c0.PNG)

3. 打开Utils.em文件，在该文件的末尾加入如下内容，然后保存文件

```c++
/* 
  cpp和hpp文件互换(当前只支持同一目录下的文件互换) 
*/ 
macro switch_cpp_hpp() 
{ 
    hwnd = GetCurrentWnd() 
    hCurOpenBuf = GetCurrentBuf() 
    if (hCurOpenBuf == 0) 
        stop 
    curOpenFileName = GetBufName(hCurOpenBuf) 
    curOpenFileNameLen = strlen(curOpenFileName) 
  
    // 文件类型临时缓冲区 
    strFileExt = NewBuf("strFileExtBuf") 
    ClearBuf(strFileExt) 
      
    // 头文件类型 
    index_hpp_begin = 0 // 头文件开始索引 
    AppendBufLine(strFileExt, ".h") 
    AppendBufLine(strFileExt, ".hpp") 
    index_hpp_end = GetBufLineCount(strFileExt) // 头文件结束索引 
      
    // 源文件类型 
    index_cpp_begin = index_hpp_end // 源文件开始索引 
    AppendBufLine(strFileExt, ".c") 
    AppendBufLine(strFileExt, ".cpp") 
    AppendBufLine(strFileExt, ".cc") 
    AppendBufLine(strFileExt, ".cx") 
    AppendBufLine(strFileExt, ".cxx") 
    index_cpp_end = GetBufLineCount(strFileExt) // 源文件结束索引 
  
    isCppFile = 0 // 0：未知 1：头文件 2：源文件，默认未知扩展名 
    curOpenFileExt = "" // 当前打开文件的扩展名 
    index = index_hpp_begin 
    // 遍历头文件，判断是否当前打开文件是头文件类型 
    while(index < index_cpp_end) 
    { 
        curExt = GetBufLine(strFileExt, index) 
        curExtLen = strlen(curExt) 
        curOpenFileExt = strmid(curOpenFileName, curOpenFileNameLen-curExtLen, curOpenFileNameLen) // 当前打开文件的扩展名 
          
        // 调试 
        // AppendBufLine(debugBuf, curExt) 
        // AppendBufLine(debugBuf, curOpenFileExt) 
  
          
        if(curOpenFileExt == curExt) // 匹配成功 
        { 
            if (index < index_hpp_end) 
                isCppFile = 1 // 当前打开文件是头文件 
            else 
                isCppFile = 2 // 源文件 
            break 
        } 
        index = index + 1 
    }// while(index < index_cpp_end) 
      
    // 调试 
    // AppendBufLine(debugBuf, isCppFile) 
  
    index_replace_begin = index_hpp_begin 
    index_replace_end = index_hpp_end 
      
    if (isCppFile == 1) // 当前打开文件是头文件 
    { 
        index_replace_begin = index_cpp_begin 
        index_replace_end = index_cpp_end 
    } 
    else if(isCppFile == 2) // 当前打开文件是源文件 
    { 
        index_replace_begin = index_hpp_begin 
        index_replace_end = index_hpp_end 
      
        // 调试 
        // AppendBufLine(debugBuf, "cpp") 
    } 
    else // 未知类型 
    { 
        //CloseBuf(strFileExt) // 关闭缓冲区 
  
        //stop 
      
        index_replace_begin = 9999 
        index_replace_end = index_replace_begin // 下面循环不会执行 
    } 
      
    index = index_replace_begin 
    while(index < index_replace_end) 
    { 
        destExt = GetBufLine(strFileExt, index) 
        destFileName = strmid(curOpenFileName, 0, curOpenFileNameLen-strlen(curOpenFileExt)) // 不包括扩展名，绝对路径 
          
        // 尝试当前目标扩展名是否能够打开 
        destFilePath = cat(destFileName, destExt)    // 文件名（包括扩展名） 
          
        hCurOpenBuf = OpenBuf(destFilePath) 
        if(hCurOpenBuf != 0) 
        { 
            SetCurrentBuf(hCurOpenBuf) 
            break 
        } 
          
        // 尝试进行目录替换，看能否打开文件（如何设计：支持多个目录） 
        // ...(赞不支持) 
  
          
        index = index + 1 
    } 
    CloseBuf(strFileExt) // 关闭缓冲区 
    // 调试 
    // AppendBufLine(debugBuf, "finished") 
      
}
```

4. 关闭Base工程，关闭source insight，然后重新打开

5. 打开Option -> Key Assignments。在Command栏选中“Macro: switch_cpp_hpp”；点击Assign New Key按钮设置快捷键（如我喜欢用F4，就按F4），点击OK，就设定好了

![switch_h_c1](E:\work\mBlog\source insight 3.5\switch_h_c1.PNG)

## 创建工程

1. 打开source insight，Project->new project，设置source insight保存工程的位置。si的功能就是查看代码，编辑代码。这里的位置，相当于源代码的监视位置，只要源代码修改，这里会更新，相反这里代码更改，源码也会改变。

![create_prj0](E:\work\mBlog\source insight 3.5\create_prj0.PNG)

2. 接着就是设置源码的路径，配置文件可跟自己偏好单独设置或使用统一的。

![create_prj1](E:\work\mBlog\source insight 3.5\create_prj1.PNG)

3. 选中源代码，然后将源码加入到si的工程。可以傻瓜式add all，然后工程创建就完成了

![create_prj2](E:\work\mBlog\source insight 3.5\create_prj2.PNG)

![create_prj3](E:\work\mBlog\source insight 3.5\create_prj3.PNG)

![create_prj4](E:\work\mBlog\source insight 3.5\create_prj4.PNG)







## 















