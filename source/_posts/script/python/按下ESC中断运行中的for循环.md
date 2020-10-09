---
title: 按下“ESC”中断运行中的for循环
tags: [SCRIPT,PYTHON,MAYA]
category: SCRIPT
---

&emsp;&emsp;假设有一个程序，运行过程中每隔一秒打印一次数字，当键盘按下“ESC”时，使该循环终止。

```python
def a_func():
    # ! 循环程序
    for i in range(10):
        time.sleep(1)
        print i
```

### 🐾 pynput
&emsp;&emsp;pynput.keyboard：包含控制和监控键盘的类。

&emsp;&emsp;调用pynput.mouse.Listener.stop，发起StopException异常，或者回调函数中返回False都会停止事件的监听。

```python
# -*- coding:utf-8 -*-

import time
from pynput import keyboard
from threading import Thread

class thread(Thread):
    def __init__(self):
        Thread.__init__( self )
        self.shut_down_signal = True

    def run(self):
        # ! 循环程序
        for i in range(5):
            if self.shut_down_signal:
                time.sleep(1)
                print i
            else:
                return

def on_press(key):
    # ! 重写on_press
    pass

def on_release(key):
    # ! 重写on_release
    if key ==  keyboard.Key.esc:
        print "Esc"
        # ! 修改线程中的shut_down_signal
        run_thread.shut_down_signal = False
        # ! 退出监听器
        return False

# ! 启动线程
run_thread = thread()
run_thread.start()
# ! 启动监听器
with keyboard.Listener(on_press=on_press,on_release=on_release) as listener:
    listener.join()
```

### 🚀 QWidget 的 keyPressEvent 和 keyReleaseEvent
&emsp;&emsp;在Qt窗口中，可以通过重写 keyPressEvent() 和 keyReleaseEvent() 这两个方法实现。keyPressEvent 是 QWidget 里面的函数，所以凡是继承自 QWidget 的类都可以通过实现这个函数来完成对按键事件的响应。

&emsp;&emsp;点击“Click to run”按钮开始for循环，按“Esc”退出。

```python
# -*- coding:utf-8 -*-

import time
from threading import Thread

from PySide2.QtCore import *
from PySide2.QtGui import *
from PySide2.QtWidgets import *

class thread(Thread):
    def __init__(self):
        Thread.__init__( self )
        self.shut_down_signal = True

    def run(self):
        # ! 循环程序
        for i in range(5):
            if self.shut_down_signal:
                time.sleep(1)
                print i
            else:
                return

class m_window(QMainWindow):
    def __init__(self):
        super(m_window, self).__init__()
        self.centralwidget = QWidget(self)
        # ! StrongFocus：可以通过单击来接受焦点，只要在窗口下，就能接收键盘事件
        self.setFocusPolicy(Qt.StrongFocus)
        self.lay = QHBoxLayout(self.centralwidget)
        self.m_btn = QPushButton('Click to run')
        self.lay.addWidget(self.m_btn)
        # ! 点击按钮启动线程
        self.m_btn.clicked.connect(self.start_thread)
    
    def start_thread(self):
        # ! 启动线程
        self.thread = thread()
        self.thread.start()

    def keyPressEvent(self, event):
        # ! 重写keyPressEvent
        pass

    def keyReleaseEvent(self, event):
        # ! 重写keyReleaseEvent
        # ! 当按下“esc”时，退出for循环
        if event.key() == Qt.Key_Escape:
            print "esc"
            self.thread.shut_down_signal = False


if __name__ == '__main__':
    mainWin = m_window()
    mainWin.show()
```

### 🎉 QProgressDialog
&emsp;&emsp;进度对话框用于向用户指示操作将花费多长时间，并表明应用程序正在运行。它还可以使用户有机会中止该操作。

```python
# -*- coding:utf-8 -*-

import time

from PySide2.QtCore import *
from PySide2.QtGui import *
from PySide2.QtWidgets import *

class m_window(QMainWindow):
    def __init__(self):
        super(m_window, self).__init__()
        self.centralwidget = QWidget(self)
        self.lay = QHBoxLayout(self.centralwidget)
        self.m_btn = QPushButton('Click to run')
        self.lay.addWidget(self.m_btn)
        # ! 点击按钮启动线程
        self.m_btn.clicked.connect(self.start_thread)
    
    def start_thread(self):
        # ! 创建进度条
        progress = QProgressDialog("Running...", "Abort", 0, 10, self);
        # ! 设置进度条模式
        progress.setWindowModality(Qt.WindowModal);
        for i in range(10):
            progress.setValue(i);
            if progress.wasCanceled():
                break
            time.sleep(1)
            print i
        return

if __name__ == '__main__':
    mainWin = m_window()
    mainWin.show()
```

&emsp;&emsp;我们可以通过QProgressDialog进度条随时打断正在运行的程序，而不用自己创建线程。