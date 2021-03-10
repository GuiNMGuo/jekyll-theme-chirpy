---
layout: post
title: vscode python环境配置
date: 2021-03-10 10:30:00 +0800
Author: 杨舒文	
categories: [软件安装, python] 
tags: [python,vscode]
comments: true
---

## 插件安装与配置

快捷键`Ctrl+Shift+X`打开扩展安装界面，安装插件

- ### Python

  安装完插件后，打开python文件会自动提示选择Python解析器。也可以通过`Ctrl+Shift+P`打开命令面板输入`Select Interpreter`来选择需要的解析器。

  #### 配置header模板

  - 文件-首选项-用户代码-python

    之后选择python后会生成python.json,将原来内容替换为一下内容：

  ```javascript
  {
      "HEADER":{
          "prefix": "header",
          "body": [
          "#!/usr/bin/env python",
          "# -*- encoding: utf-8 -*-",
          "'''",
          "@File    :   $TM_FILENAME",
          "@Time    :   $CURRENT_YEAR/$CURRENT_MONTH/$CURRENT_DATE $CURRENT_HOUR:$CURRENT_MINUTE:$CURRENT_SECOND",
          "@Author  :   Rao Guangxiang ",
          "@Version :   1.0",
          "@Contact :   raogx.vip@hotmail.com",
          "@License :   (C)Copyright 2017-2018, Liugroup-NLPR-CASIA",
          "@Desc    :   None",
          
          "'''",
          "",
          "# here put the import lib",
          "$0"
      ],
      }
      
  }
  ```

  在.PY文件上面输入header回车就会自动生成文件头。一般输入hea就会自动联想出来。

- ### Pylance

  强烈建议使用Pylance替代默认的Python语言服务器，它支持一些很棒的功能，比如：

  1. Docstrings
  2. 自动导入
  3. 类型检查
  4. Code Lens
  5. IntelliCode 兼容性

  #### 推荐配置

  1. **可以打开保存时和键入一行后立即进行代码格式化，设置选项为**
  
  ```json
  {
      "editor.formatOnSave": true,
      "editor.formatOnType": true
  }
  ```
  
  2. **设置静态代码linter为flake8**
  
     Flake8是由Python官方发布的一款辅助检测Python代码是否规范的工具，它包含PEP8编码风格检查，代码静态检查则是依托PyFlakes，相对于Pylint，其检查规则灵活，扩展性更强一些。
  
     同时最好关闭Pylint，json设置如下：
  
     ```json
     {
         "python.linting.flake8Enabled": true,
         "python.linting.pylintEnabled": false,
     }
     ```
  
  3. **添加垂直参考线**
  
     Python的代码风格要求代码一行不超过79字符，所以我们可以给代码编辑器设置一条垂直参考线，直观判断代码是否太长，并且可以自定义参考线的颜色：
  
     ```json
     { 
         "editor.rulers": [
             80,
         ],
         "workbench.colorCustomizations": {
             "editorRuler.foreground": "#ff4081"
         }
     }
     ```
  
  #### 常用操作
  
  1. **自动导入**
  
     ![](/assets/img/md_pictrue/3829088d6f2f466c77b2017.gif)
  
  2. **代码重构**
  
     光标放到在函数名、参数名上按`F2`重构变量名。
  
  3. **提取变量或方法**
  
     ![](/assets/img/md_pictrue/3829088ab1d07da4c44070d.png)
  
  4. **触发参数提示**
  
     在调用函数的时候，vscode会自动弹出参数提示，可以通过`ESC`关闭，关闭后可以通过快捷键`Ctrl+Shift+Space`唤出。
      但是这个快捷键可能会被输入法占用，可以修改绑定快捷键，通过快捷键`Ctrl+K Ctrl+S`打开快捷键设置窗口，搜索`editor.action.triggerParameterHints`，修改快捷键即可。
  
  5. **终端运行代码**
  
     按F5用debug模式运行，按ctrl+F5非debug模式运行。
  
     debug中python的launch.json配置："console": "internalConsole"
  
     用#%%也可调试代码