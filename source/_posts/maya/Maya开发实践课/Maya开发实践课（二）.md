---
title: Maya开发实践课（二）
tags: [SCRIPT, MAYA]
category: [Maya, Maya开发实践课]
---

## L15 走进 PyMEL 编程
### 什么是PyMel
![](Maya开发实践课（二）/L15_1.png)

### 了解PyNode
![](Maya开发实践课（二）/L15_2.png)

### pynode转化
```python
node = pm.PyNode('pSphere1')

# ! 查询方法
dir(node)

# ! 查询属性
dir(node.attr())
```