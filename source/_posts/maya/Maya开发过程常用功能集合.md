---
title: Maya开发过程常用功能集合
tags: [MAYA,SUM]
category: Maya
---

#### &emsp;&emsp;由于我的工作需要在Maya上进行开发，所以想总结一下自己常用的功能，以免下次自己使用的时候忘记。

### ❌<font color=FireBrick>Traceback 显示详细的报错信息</font>

&emsp;&emsp;使用traceback类可以捕捉异常并打印信息。由于在编写代码时，经常需要import多个类，但是如果在maya的脚本编辑器中直接运行错误的代码，不一定能返回详细的报错信息，无法得知是哪一个文件哪一行的错误。下面是一个小例子。
- 创建两个文件：test.py和errorfile.py
```e
d1
sad
```
- 