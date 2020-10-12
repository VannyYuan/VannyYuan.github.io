---
title: Maya制作文件问题集合
tags: [MAYA,SUM]
category: Maya
---

&emsp;&emsp;由于工作中经常遇到Maya中奇奇怪怪的问题上，所以总结一下一些遇到的小问题。

### ✂ <font color=FireBrick>文件中多余的相机无法删除</font>
![](Maya制作文件问题集合/相机无法删除.png)

&emsp;&emsp;<font color=orange>原因：</font>相机重复出现可能是因为在导入或参考时出错了，需要把相机设置成非默认摄像机才可以删除。

&emsp;&emsp;<font color=orange>解决：</font>选中需要删除的相机执行。

```mel
// MEL
string $selection[] = `ls -selection -long`;
string $object;
for ( $object in $selection ) {
camera -e -startupCamera false $object;
lockNode -lock off $object;
delete $object;
}
```

```python
# python
cams = cmds.ls(sl=1)
for cam in cams:
cmds.camera(cam,startupCamera=0,edit=1)
cmds.lockNode(cam,lock=0)
cmds.delete(cam)
```

🖌 待续未完...
