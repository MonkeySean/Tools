# source insight 4.0 使用说明

## 一 介绍

​	source insight 4.0是一个编辑器，最大的特色是通过contex window可以一级一级往更深层看代码，其次，symbol window会将文件中用到的各种符号，函数等等归类，非常方便查看。

​	要说麻烦一点的，就是需要手动建工程（建一个目录，用于存放工程文件），然后手动导入源文件。并且，若希望用到的函数或符号都能被识别出来（识别不了会显示黑色），则还需要在Option->Preferences->Symbol Lookups里面，去将引用的头文件添加进工程。

​	其次，原始的快捷键不方便，推荐改键。并且可以将功能扩展，添加头/源文件切换，批量注释等。

## 二 工程创建

​	 project -> new project -> 重命名project name -> 创建工程文件存储目录（我一般命名为SI）-> 在File Paths里面，选择导入要查看的源文件目录 -> OK

## 三 视窗调整

​	source insight几个必须的视窗要打开，如下：

- symbol window

  view -> symbol window

- contex window

  view -> panels -> contex window

  其中，contex window右键，可以设置toolbar是否显示，有时误操作关掉了可以从这里打开

- project window

  view -> panels -> project window

  这里有工程符号视图，文件目录树视图（我偏爱这个）

 ## 四 快捷键改键及新增自定义快捷功能

​	改键：Options -> Key Assignments -> 选择左边要该快捷键的项 -> Assign New Key -> 按你想要的键 -> 确定（若取消，需按ESC退出改键状态）

​	我常用的快捷键如下：

- Look up reference 查找引用	Ctrl + 左键

- jump to definition 跳转到定义     F2

- jump to prototype 跳转到声明     F1

- Navigation: Go To Previous Link  跳转至前一个链接     F7    一般是查找引用后可通过这个来操作

- Navigation: Go To Next Link   跳转至下一个链接           F8

- Navigation: Go Back 回退     F11

- Navigation: Go Forward  前进     F12

- Macro: MultiLineComment   多行注释 （扩展）        Ctrl+/

- Macro: switch_cpp_hpp 头源文件切换（扩展）  F4

  自定义快捷功能如下：

  ```
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

  ```
  macro MultiLineComment()
  
  {
  
      hwnd = GetCurrentWnd()
  
      selection = GetWndSel(hwnd)
  
      LnFirst =GetWndSelLnFirst(hwnd)      //取首行行号
  
      LnLast =GetWndSelLnLast(hwnd)      //取末行行号
  
      hbuf = GetCurrentBuf()
  
   
  
      if(GetBufLine(hbuf, 0) =="//magic-number:tph85666031"){
  
          stop
  
      }
  
   
  
      Ln = Lnfirst
  
      buf = GetBufLine(hbuf, Ln)
  
      len = strlen(buf)
  
   
  
      while(Ln <= Lnlast) {
  
          buf = GetBufLine(hbuf, Ln)  //取Ln对应的行
  
          if(buf ==""){                   //跳过空行
  
              Ln = Ln + 1
  
              continue
  
          }
  
   
  
          if(StrMid(buf, 0, 1) == "/"){       //需要取消注释,防止只有单字符的行
  
              if(StrMid(buf, 1, 2) == "/"){
  
                  PutBufLine(hbuf, Ln, StrMid(buf, 2, Strlen(buf)))
  
              }
  
          }
  
   
  
          if(StrMid(buf,0,1) !="/"){          //需要添加注释
  
              PutBufLine(hbuf, Ln, Cat("//", buf))
  
          }
  
          Ln = Ln + 1
  
      }
  
   
  
      SetWndSel(hwnd, selection)
  
  }
  ```






