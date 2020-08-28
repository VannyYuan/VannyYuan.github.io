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
    test.py
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


🖌 待续未完...