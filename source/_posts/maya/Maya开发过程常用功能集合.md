---
title: Maya开发过程常用功能集合
tags: [MAYA,SUM]
category: Maya
---

#### 
&emsp;&emsp;由于我的工作需要在Maya上进行开发，所以想总结一下自己常用的功能，以免下次自己使用的时候忘记。

### ❌ <font color=FireBrick>Traceback 显示详细的报错信息</font>

&emsp;&emsp;使用traceback类可以捕捉异常并打印信息。由于在编写代码时，经常需要import多个类，但是如果在maya的脚本编辑器中直接运行错误的代码，不一定能返回详细的报错信息，无法得知是哪一个文件哪一行的错误。下面是一个小例子。
- **创建两个文件：test.py 和 errorfile.py**
```python
import os
import errorfile
def test_func():
    errorfile.error_func()
```
    errorfile.py
```python
import os
def error_func():
    os.makedirs(r"C:\test_folder")
```


- **在maya中的运行代码：**
    - 不使用traceback的代码
```python
import sys
sys.path.insert(0,r'D:\git_script')
import test
reload(test)
test.test_func()
```
        报错：
        <font color='red'>\# Error: WindowsError: file C:\Program Files\Autodesk\Maya2018\bin\python27.zip\os.py line 157: 183 # </font>
    - 使用traceback的代码
```python
try:
    import test
    reload(test)
    test.test_func()
except:
    import traceback
    traceback.print_exc()
```
        报错：
        <font color='red'>\# Traceback (most recent call last):
        \#   File "<maya console>", line 7, in <module>
        \#   File "D:\git_script\test.py", line 5, in test_func
        \#     faa.error_func()
        \#   File "D:\git_script\errorfile.py", line 4, in error_func
        \#     os.makedirs(r"C:\test_folder")
        \#   File "C:\Program Files\Autodesk\Maya2018\bin\python27.zip\os.py", line 157, in makedirs
        \#     mkdir(name, mode)
        \# WindowsError: [Error 183] : 'C:\\test_folder'
        </font>

&emsp;&emsp;从上面例子可以看到，使用traceback后显示的错误信息十分完整，对于找bug十分有用。👍

### 💡 <font color=FireBrick>修改参考文件的路径</font>
&emsp;&emsp;开发某些工具的过程中需要修改maya场景参考资产的路径，以下是cmds和pm获取reference节点以及修改路径的方法：
- **使用pm**
```python
pm.FileReference(rn_node).unresolvedPath()
pm.FileReference(rn_node).replaceWith(new_path) 
```
- **使用cmds**：
```python
cmds.referenceQuery(rn_node, filename=1)
cmds.file(new_path, loadReference=rn_node)
```

### ⏰ <font color=FireBrick>场景时间轴节点 sceneConfigurationScriptNode</font>
&emsp;&emsp;Maya在保存文件时会记录一个记录场景时间轴信息的节点“sceneConfigurationScriptNode”，该节点可以在表达式编辑器“Expression Editor”中找到。  
![](Maya开发过程常用功能集合/editor.png)

&emsp;&emsp;在属性编辑器“Attribute Editor”中，可以看到该节点的详细信息。  
![](Maya开发过程常用功能集合/script_editor.png)

&emsp;&emsp;可以通过cmds.objExists()查询节点是否存在。在对选择导出时，可以把该节点一同选择导出，使场景保留时间轴信息。
```python
cmds.objExists('sceneConfigurationScriptNode')
cmds.objectType('sceneConfigurationScriptNode')
```

### 💎 <font color=FireBrick>使用PySide2创建Maya置顶的窗口</font>
&emsp;&emsp;Maya2016版本以上会自带PySide2，我们可以使用PySide2在Maya中创建窗口。

&emsp;&emsp;M该内容参考来源 [智伤帝 - Python Qt 开发教程 扩展说明](https://blog.l0v0.com/%2Fposts%2F2e0af969.html)。
```python
import maya.OpenMayaUI as omui
try:
    from shiboken2 import wrapInstance
except ImportError:
    from shiboken import wrapInstance

# ! 这个函数可以实现将 Maya 的 UI 转换为 Qt 的 QWidget 组件
def mayaToQT(name):
    ptr = omui.MQtUtil.findControl(name)
    if ptr is None:
        ptr = omui.MQtUtil.findLayout(name)
    if ptr is None:
        ptr = omui.MQtUtil.findMenuItem(name)
    if ptr is not None:
        return wrapInstance(long(ptr), QWidget)

# ! 检查窗口多开
global XG_WIN
if 'XG_WIN' in globals():
    if cmds.window(XG_WIN, q=1, ex=1):
        cmds.evalDeferred("cmds.deleteUI(\"" + XG_WIN + "\")")

# ! 获取 Qt 窗口控件
tool_ui = p_xgmArchiveExportBatchUI(False)
# ! 创建 Maya 窗口
XG_WIN = cmds.window(title=tool_ui.windowTitle())
# ! 显示 Maya 窗口
cmds.showWindow(XG_WIN)
# ! Maya 窗口转换为 Qt 对象
ptr = mayaToQT(XG_WIN)
# ! Maya 窗口添加 Qt 布局
ptr.setLayout(QVBoxLayout())
ptr.layout().setContentsMargins(0, 0, 0, 0)
# ! 把 Qt 窗口控件添加到 Maya 窗口布局中
ptr.layout().addWidget(tool_ui)
ptr.setFixedSize(QSize(tool_ui.width(), tool_ui.height()))
```

🖌 待续未完...