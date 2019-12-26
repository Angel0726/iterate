---
title: 02 PyCharm 快捷键
toc: true
date: 2019-11-17
---

[PyCharm版本控制和数据库管理][PyCharm中的那些实用功能]

PyCharm学习技巧 Learning tips
/Pythoncharm/help/tip of the day:
A special variant of the Code Completion feature invoked by pressing Ctrl+Space twice allows you to complete the name of any class no matter if it was imported in the current file or not. If the class is not imported yet, the import statement is generated automatically.
You can quickly find all places where a particular class, method or variable is used in the whole project by positioning the caret at the symbol's name or at its usage in code and pressing Alt+Shift+F7 (Find Usages in the popup menu).
To navigate to the declaration of a class, method or variable used somewhere in the code, position the caret at the usage and press F12. You can also click the mouse on usages with the Ctrl key pressed to jump to declarations.
You can easily rename your local variables with automatic correction of all places where they are used.
To try it, place the caret at the variable you want to rename, and press Shift+F6 (Refactor | Rename). Type the new name in the popup window that appears, or select one of the suggested names, and press Enter.

...

切换
Use Alt+Up and Alt+Down keys to quickly move between methods in the editor.
Use Ctrl+Shift+F7 (Edit | Find | Highlight Usages in File) to quickly highlight usages of some variable in the current file.
选择
You can easily make column selection by dragging your mouse pointer while keeping the Alt key pressed.
补全
Working in the interactive consoles, you don't need to memorise the command line syntax or available functions. Instead, you can use the familiar code completion Ctrl+Space. Moreover, from within the lookup list, you can press Ctrl+Q to view the item's documentation.
显示
Use F3 and Shift+F3 keys to navigate through highlighted usages.
Press Escape to remove highlighting.
历史
Ctrl+Shift+Backspace (Navigate | Last Edit Location) brings you back to the last place where you made changes in the code.
Pressing Ctrl+Shift+Backspace a few times moves you deeper into your changes history.
Ctrl+E (View | Recent Files) brings a popup list of the recently visited files. Choose the desired file and press Enter to open it.
Use Alt+Shift+C to quickly review your recent changes to the project.
剪切板
Use the Ctrl+Shift+V shortcut to choose and insert recent clipboard contents into the text.
If nothing is selected in the editor, and you press Ctrl+C, then the whole line at caret is copied to the clipboard.
run/debug
By pressing Alt+Shift+F10 you can access the Run/Debug dropdown on the main toolbar, without the need to use your mouse.

在PyCharm安装目录 /opt/PyCharm-3.4.1/help目录下可以找到ReferenceCard.pdf快捷键英文版说明 or 打开PyCharm > help > default keymap ref

 

PyCharm3.0默认快捷键(翻译的)
PyCharm Default Keymap(mac中还不一样，ctrl可能用cmd或者cmd+ctrl代替)

1、编辑（Editing）

Ctrl + Space    基本的代码完成（类、方法、属性）
Ctrl + Alt + Space  快速导入任意类
Ctrl + Shift + Enter    语句完成
Ctrl + P    参数信息（在方法中调用参数）

Ctrl + Q    快速查看文档

F1   外部文档

Shift + F1    外部文档，进入web文档主页

Ctrl + Shift + Z --> Redo 重做

Ctrl + 悬浮/单击鼠标左键    简介/进入代码定义
Ctrl + F1    显示错误描述或警告信息
Alt + Insert    自动生成代码
Ctrl + O    重新方法
Ctrl + Alt + T    选中
Ctrl + /    行注释/取消行注释
Ctrl + Shift + /    块注释
Ctrl + W    选中增加的代码块
Ctrl + Shift + W    回到之前状态
Ctrl + Shift + ]/[     选定代码块结束、开始
Alt + Enter    快速修正
Ctrl + Alt + L     代码格式化
Ctrl + Alt + O    优化导入
Ctrl + Alt + I    自动缩进
Tab / Shift + Tab  缩进、不缩进当前行
Ctrl+X/Shift+Delete    剪切当前行或选定的代码块到剪贴板
Ctrl+C/Ctrl+Insert    复制当前行或选定的代码块到剪贴板
Ctrl+V/Shift+Insert    从剪贴板粘贴
Ctrl + Shift + V    从最近的缓冲区粘贴
Ctrl + D  复制选定的区域或行
Ctrl + Y    删除选定的行
Ctrl + Shift + J  添加智能线
Ctrl + Enter   智能线切割
Shift + Enter    另起一行
Ctrl + Shift + U  在选定的区域或代码块间切换
Ctrl + Delete   删除到字符结束
Ctrl + Backspace   删除到字符开始
Ctrl + Numpad+/-   展开/折叠代码块（当前位置的：函数，注释等）
Ctrl + shift + Numpad+/-   展开/折叠所有代码块
Ctrl + F4   关闭运行的选项卡
 2、查找/替换(Search/Replace)
F3   下一个
Shift + F3   前一个
Ctrl + R   替换
Ctrl + Shift + F  或者连续2次敲击shift   全局查找{可以在整个项目中查找某个字符串什么的，如查找某个函数名字符串看之前是怎么使用这个函数的}
Ctrl + Shift + R   全局替换
 3、运行(Running)
Alt + Shift + F10   运行模式配置
Alt + Shift + F9    调试模式配置
Shift + F10    运行
Shift + F9   调试
Ctrl + Shift + F10   运行编辑器配置
Ctrl + Alt + R   运行manage.py任务
 4、调试(Debugging)
F8   跳过
F7   进入
Shift + F8   退出
Alt + F9    运行游标
Alt + F8    验证表达式
Ctrl + Alt + F8   快速验证表达式
F9    恢复程序
Ctrl + F8   断点开关
Ctrl + Shift + F8   查看断点
 5、导航(Navigation)
Ctrl + N    跳转到类
Ctrl + Shift + N    跳转到符号

Alt + Right/Left    跳转到下一个、前一个编辑的选项卡（代码文件）（cmd+alt+right/left mac）

Alt + Up/Down跳转到上一个、下一个方法

 

F12    回到先前的工具窗口
Esc    从工具窗口回到编辑窗口
Shift + Esc   隐藏运行的、最近运行的窗口
Ctrl + Shift + F4   关闭主动运行的选项卡
Ctrl + G    查看当前行号、字符号
Ctrl + E   当前文件弹出，打开最近使用的文件列表
Ctrl+Alt+Left/Right   后退、前进

Ctrl+Shift+Backspace    导航到最近编辑区域 {差不多就是返回上次编辑的位置}

Alt + F1   查找当前文件或标识
Ctrl+B / Ctrl+Click    跳转到声明
Ctrl + Alt + B    跳转到实现
Ctrl + Shift + I查看快速定义
Ctrl + Shift + B跳转到类型声明

Ctrl + U跳转到父方法、父类

Ctrl + ]/[跳转到代码块结束、开始

Ctrl + F12弹出文件结构
Ctrl + H类型层次结构
Ctrl + Shift + H方法层次结构
Ctrl + Alt + H调用层次结构
F2 / Shift + F2下一条、前一条高亮的错误
F4 / Ctrl + Enter编辑资源、查看资源
Alt + Home显示导航条F11书签开关
Ctrl + Shift + F11书签助记开关
Ctrl + #[0-9]跳转到标识的书签
Shift + F11显示书签
 6、搜索相关(Usage Search)
Alt + F7/Ctrl + F7文件中查询用法
Ctrl + Shift + F7文件中用法高亮显示
Ctrl + Alt + F7显示用法
 7、重构(Refactoring)
F5复制F6剪切
Alt + Delete安全删除
Shift + F6重命名
Ctrl + F6更改签名
Ctrl + Alt + N内联
Ctrl + Alt + M提取方法
Ctrl + Alt + V提取属性
Ctrl + Alt + F提取字段
Ctrl + Alt + C提取常量
Ctrl + Alt + P提取参数
 8、控制VCS/Local History
Ctrl + K提交项目
Ctrl + T更新项目
Alt + Shift + C查看最近的变化
Alt + BackQuote(’)VCS快速弹出
 9、模版(Live Templates)
Ctrl + Alt + J当前行使用模版
Ctrl +Ｊ插入模版
 10、基本(General)
Alt + #[0-9]打开相应的工具窗口
Ctrl + Alt + Y同步
Ctrl + Shift + F12最大化编辑开关
Alt + Shift + F添加到最喜欢
Alt + Shift + I根据配置检查当前文件
Ctrl + BackQuote(’)快速切换当前计划
Ctrl + Alt + S　打开设置页
Ctrl + Shift + A查找编辑器里所有的动作

Ctrl + Tab在窗口间进行切换



# 相关

- [PyCharm快捷键、常用设置、配置管理](https://blog.csdn.net/pipisorry/article/details/39909057)
