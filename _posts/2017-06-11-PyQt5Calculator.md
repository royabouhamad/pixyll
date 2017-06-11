---
layout:     post
comments: true
title:      Making a PyQt5 Calculator
date:       2017-06-11 19:11:30
summary:    Tutorial on building a desktop calculator in python.
categories: PyQt5 Tech Python Calculator Tutorial GUI Programming
---

# Making a Calculator in Python with PyQt5

I recently decided that I wanted to try and learn how to create GUI's using my programming language of choice, Python. To do this, I decided that I would use the PyQt5 library as TKinter seems to involve a lot more hassle to use and frankly, I don't have that much time to spare. Armed with my Python IDE and recently downloaded version of PyQt5, I got to work.

### **Setting up the environment**

The first thing to do when using PyQt5 is to import all the different modules as well as the sys module. This is done like so:

```python
import sys
from PyQt5.QtCore import *
from PyQt5.QtGui import *
from PyQt5.QtWidgets import *
```

We must then create a class that takes the QMainWindow argument as a parameter and initialise the MainWindow within a subroutine in the class.

```python
class Calc(QMainWindow):

    def __init__(self):
        QMainWindow.__init__(self)
        self.initUI()
```
In the ```__init__``` subroutine, there is also a call to another subroutine called ```initUI```. This subroutine contains all of the setup for the user interface, including:
* Defining the size of the window
* Adding all the push buttons
* Adding a line that acts as the calculator screen.

So, first we're going to create the calculator screen. This must be done inside the ```initUI``` function.

```python
def initUI(self):
    self.line = QLineEdit(self)
    self.line.move(5, 5)
    self.line.setReadOnly(True)
    self.line.setAlignment(Qt.AlignRight)
    font = self.line.font()
    font.setPointSize(40)
    self.line.setFont(font)
    self.line.resize(266, 70)
```

What this code does is it calls the ```QLineEdit``` function which is part of the PyQt5 library and then does a number of things:

* Moves the line to a new location in the window
* Makes sure that the line is read only so that users can't type in the calculator screen.
* Sets the alignment of the screen to the right so that all text starts on the right hand side of the screen and moves to the left hand side of the screen as new numbers are addedself.
* Changes the size of the text that the line displays.
* Resizes the line so that it takes up the top bar of the screen.

Next we must create all the push buttons.
```python
 zero = QPushButton("0", self)
 zero.move(5, 265)
 zero.resize(45, 40)

 one = QPushButton("1", self)
 one.move(5, 215)
 one.resize(45, 40)

 two = QPushButton("2", self)
 two.move(60, 215)
 two.resize(45, 40)

 three = QPushButton("3", self)
 three.move(115, 215)
 three.resize(45, 40)

 four = QPushButton("4", self)
 four.move(5, 165)
 four.resize(45, 40)

 five = QPushButton("5", self)
 five.move(60, 165)
 five.resize(45, 40)

 six = QPushButton("6", self)
 six.move(115, 165)
 six.resize(45, 40)

 seven = QPushButton("7", self)
 seven.move(5, 115)
 seven.resize(45, 40)

 eight = QPushButton("8", self)
 eight.move(60, 115)
 eight.resize(45, 40)

 nine = QPushButton("9", self)
 nine.move(115, 115)
 nine.resize(45, 40)

 switch = QPushButton("+/-", self)
 switch.move(60, 265)
 switch.resize(45, 40)
 switch.clicked.connect(self.Switch)

 point = QPushButton(".", self)
 point.move(115, 265)
 point.resize(45, 40)
 point.clicked.connect(self.Point)

 plus = QPushButton("+", self)
 plus.move(170, 265)
 plus.resize(45, 40)

 minus = QPushButton("-", self)
 minus.move(170, 215)
 minus.resize(45, 40)

 multiply = QPushButton("*", self)
 multiply.move(170, 165)
 multiply.resize(45, 40)

 divide = QPushButton("/", self)
 divide.move(170, 115)
 divide.resize(45, 40)

 equals = QPushButton("=", self)
 equals.move(225, 215)
 equals.resize(45, 90)
 equals.clicked.connect(self.Equal)

 squared = QPushButton("x²", self)
 squared.move(225, 165)
 squared.resize(45, 40)
 squared.clicked.connect(self.Squared)

 root = QPushButton("√", self)
 root.move(225, 115)
 root.resize(45, 40)
 root.clicked.connect(self.Root)

 ce = QPushButton("CE", self)
 ce.move(54, 75)
 ce.resize(112, 40)
 ce.clicked.connect(self.CE)

 c = QPushButton("C", self)
 c.move(164, 75)
 c.resize(112, 40)
 c.clicked.connect(self.C)
 ```

 Each of these buttons is made in the same fashion. The ```QPushButton``` function is initialised as a variable. It is then moved to the correct location on the screen, and then the button is resized. Some of the buttons shown above have also been connected to various subroutines.

 At the moment, the buttons look pretty boring however, one cool feature of PyQt is that you can use CSS stylings to style some objects. With this in mind, let's have a look at the next section of code.

 ```python
  nums = [zero, one, two, three, four, five, six, seven, eight, nine]

  operators = [ce, c, plus, minus, multiply, divide, equals]

  others = [switch, squared, root, point]

  for i in nums:
    i.setStyleSheet("color:blue;")
    i.clicked.connect(self.Num)

  for i in operators:
    i.setStyleSheet("color:red;")
  for i in operators[2:]:
    i.clicked.connect(self.operator)

  for i in others:
    i.setStyleSheet("color:red;")

 ```

 The code above creates three lists containing all the variables of the buttons initialised earlier and then loops through each list, adding CSS stylings to each and connecting the buttons to a subroutine if need be.

 Finally, we must set the window size and title, as well as make sure that the window is shown. This can be seen in the code below.

 ```python
self.setGeometry(300, 300, 273, 320)
self.setWindowTitle("Calculator")
self.setFixedSize(273, 320)
self.show()
 ```

 Now you may be trying to run your code to see what the calculator looks like, however your still missing one small piece of code. This code must be added outside and below the class.

```python
 def main():
    app = QApplication(sys.argv)
    main = Calc()
    main.show()
    sys.exit(app.exec_())

if __name__ == "__main__":
    main()
```

All this code does is starts up the window when you click run.

### **Calculator Functionality**

In this program we will be using a couple of variables as global variables throughout. Therefore, we must initialise these variables at the start of the program, outside of the class.

```python
num = 0.0
newNum = 0.0
sumIt = 0.0
sumAll = 0.0
operator = ""

opVar = False
```
The first function we are going to create is the ```Num``` function. This function allows us to enter numbers into the calculator and see them displayed on the screen.

```python
def Num(self):
    global num
    global newNum
    global opVar

    sender = self.sender()

    newNum = int(sender.text())
    setNum = str(newNum)

    if opVar == False:
        self.line.setText(self.line.text() + setNum)
    else:
        self.line.setText(setNum)
        opVar = False
```

First, we need to call all the variables as global variables. Then, we use the ```self.sender()``` command to recieve the number that the user has clicked on. We then set the variable ```setNum``` to the number entered but first make sure that the number has been converted to a string so that it can be displayed in the LineEdit. You may also notice the ```opVar```, this variable ensures that each number clicked on makes up part of the current number unless and operator has been pressed beforehand.

```python
def operator(self):
    global sumIt
    global num
    global opVar
    global operator

    sumIt += 1

    if sumIt > 1:
        self.Equal()

    num = self.line.text()
    sender = self.sender()
    operator = sender.text()

    opVar = True
```

This next function is connected to all the operators. The ```sumIt```variable checks if there is more than one operator already pressed and if there is, it will print the sumso that it is easier for users to see the total sum of their current calculation. The function then sets the variable ```num``` to whaterver number is currently in the LineEdit and sets the operator to whichever operator the user has just pressed. It also sets ```opVar``` to ```True``` so that the next numbers added are individual seen as a new number.


```python
def Point(self):

    if "." not in self.line.text():
        self.line.setText(self.line.text() + ".")
```

The ```point``` function checks if there is a point in the current number and if there is not a point, it will add the point to the current number.

```python
def Equal(self):
    global num
    global newNum
    global sumAll
    global operator
    global opVar
    global sumIt

    sumIt = 0

    newNum = self.line.text()

    if operator == "+":
        sumAll = float(num) + float(newNum)

    elif operator == "-":
        sumAll = float(num) - float(newNum)

    elif operator == "/":
        sumAll = float(num) / float(newNum)

    elif operator == "*":
        sumAll = float(num) * float(newNum)

    self.line.setText(str(sumAll))
    opVar = True
```

For this next function, we're going to call almost all of the global variables. We then set ```sumIt``` to 0 again, set newNum to the current number in the calculator display, and then carry out the relevant calculation. The result is then displayed on the calculator display and ```opVar``` is set to ```True``` once again.

```python
def Root(self):
    global num

    num = float(self.line.text())
    num = sqrt(num)
    self.line.setText(str(num))

def Squared(self):
    global num

    num = float(self.line.text())
    num = num**2
    self.line.setText(str(num))
```

The ```Squared``` and ```Root``` function are both pretty simple in their own right. They both take the variable ```num``` and then either squares or square roots the number. Then it displays the result on the calculator display. However, for the ```Root`` function python's inbuilt ```math``` module must be imported. To do this, insert the code below to the top of your program.

```python
from math import sqrt
```

This will allow you to use the ```sqrt``` function.

```python
def Switch(self):
    global num

    num = float(self.line.text())
    num = -num
    self.line.setText(str(num))
```

The ```Switch``` function is in place to make it easy for the user to change the sign of their current number. It does this by calling the ```num``` variable and then multiplying it by -1 before writing the answer back to the display.

```python
def CE(self):
    self.line.backspace()

def C(self):
    global num
    global newNum
    global sumAll
    global operator

    self.line.clear()

    num = 0.0
    newNum = 0.0
    sumAll = 0.0
    operator = ""
```

The ```CE``` function removes the last digit added to the number in the calculator display. This is done by using the ```.backspace()``` command which is yet another inbuilt PyQt5 function. The ```C``` function calls almost all the global variables, clears the calculator display and then sets all the variables back to their defaults so that a new calculation can begin.

And that's it! If you have any comments or feedback, you can contact me through the comments or on my email address. If you would like to improve the code or download it for use on your own computer, you can access it through my github repository at [https://github.com/royabouhamad/PyQt5-Calculator](https://github.com/royabouhamad/PyQt5-Calculator).
Thanks for reading!
