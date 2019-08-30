> #### PyQt5踩坑之路 ####

1.绑定信号槽（通俗：绑定按钮点击事件）
参考[http://https://stackoverflow.com/questions/54861232/pyqt-button-automatically-binds-to-on-clicked-function-without-connect-or-py](http://https://stackoverflow.com/questions/54861232/pyqt-button-automatically-binds-to-on-clicked-function-without-connect-or-py)
PyQt5的特性使得它可以自动绑定事件handler，前提是对方法名有要求。
C++:
```
@pyqtSlot()
void on_<object name>_<signal name>(<signal parameters>)
```
Python:
```
from PyQt5.QtCore import pyqtSlot
# 以QPushButton为例 QPushButton.setObjectName("testButton")
# 自动绑定的方法名为on_testButton_clicked(parameters)
@pyqtSlot()
def on_<object name>_<signal name>(<signal parameters>)
```
2.通过PyUIC将QTdesigner生成的.ui文件转成.py文件后如何使用
入口文件和PyUIC生成的.py如下，可以将__init__构造函数写在Ui_Form
中，函数中参数有所变化
```
# 入口Test.py文件
import sys
import PyQt5.sip
from PyQt5.QtWidgets import QWidget, QApplication, QMainWindow
from start import Ui_Form

class MainWin(QMainWindow, Ui_Form):
    def __init__(self):
        super(MainWin, self).__init__()
        self.setupUi(self)

if __name__ == "__main__":
    app = QApplication(sys.argv)
    win = MainWin()
    win.show()

    sys.exit(app.exec_())
```
```
# 由PyUIC转换的.py文件
from PyQt5 import QtCore, QtGui, QtWidgets
from PyQt5.QtCore import pyqtSlot

class Ui_Form(object):
    def setupUi(self, Form):
        Form.setObjectName("Form")
        Form.resize(400, 300)
        self.pushButton = QtWidgets.QPushButton(Form)
        self.pushButton.setGeometry(QtCore.QRect(160, 120, 71, 31))
        self.pushButton.setObjectName("pushButton")

        self.retranslateUi(Form)
        QtCore.QMetaObject.connectSlotsByName(Form)

    def retranslateUi(self, Form):
        _translate = QtCore.QCoreApplication.translate
        Form.setWindowTitle(_translate("Form", "Form"))
        self.pushButton.setText(_translate("Form", "启动程序"))

    # 启动事件，验证key
    @pyqtSlot()
    def on_pushButton_clicked(self):
        print("验证成功")
```