---
layout:     post
comments: true
title:      Making an Image Classifier Part 2
date:       2017-08-18 13:23:10
summary:    Second part of my two part series on making an image classifier with a GUI.
categories: TensorFlow PyQt5 GUI Inception Images
---

If you're reading this, I assume you have completed part 1 and now have your
own, working, retrained Inception model. Now it's time to build a simplistic GUI
to avoid running the ```label_image.py``` script through your terminal and so
that you can send the program to your less tech literate friends to show off
without them having to know how to run a script through their terminal.

### Setting up your environment

To start with, you must have a window that has a QPushButton element in it and
two QLabel elements. The QLabel elements will allow you to display text and the
images that you would like to view, and classify. As for the QPushButton, this
will trigger an event that allows you to browse your files and pick an image to
classify. So withou further ado, here's the code:

```python

import sys
from PyQt5.QtGui import *
from PyQt5.QtCore import *
from PyQt5.QtWidgets import *

class Identifier(QMainWindow):
  def __init__(self):
    QMainWindow.__init__(self)
    self.initUI()
  def initUI(self):
    self.line = QLabel(self)
    self.line = QLabel(self)
    self.line.move(5, 550)
    self.line.resize(400, 40)
    self.line.show()

    browse = QPushButton("Browse...", self)
    browse.move(400, 555)
    browse.resize(100, 40)
    browse.clicked.connect(self.openImage)

    self.label = QLabel(self)
    self.label.resize(500, 500)
    self.label.show()

    self.setGeometry(300, 300, 500, 600)
    self.setWindowTitle("Image Identifier")
    self.setFixedSize(500, 600)
    self.show()

def main():
  app = QApplication(sys.argv)
  main = Identifier()
  main.show()
  sys.exit(app.exec_())

if __name__ == "__main__":
  main()
```
This piece of code is simple enough, the window is initialised, the elements are
placed in the correct positions and the window is made into a suitable size. One
thing to take into account, is when the browse button is pressed, it connects to
a subroutine called openImage. We will create this subroutine in just a second.
Outside of the class, all that is happening is the window is being run when the
user runs the class. SIMPLE!

Now what we want to do is create the openImage subroutine. This subroutine is
responsible for letting the user choose an image and feeding the image to
```label_image.py``` as well as loading the image to your window. The first
thing we need to do is be able to pick the image, this can be done through the
following line of code:
```python
def openImage(self):
  fName = QFileDialog.getOpenFileName(self, 'Open Image', '', 'Image Files (*.jpg *.jpeg)')
```
This allows the user to only open ```*.jpg``` and ```*.jpeg``` images from their
computer. The variable ```fName``` will be a list, however we only want the first
item so we then change ```fName``` to the first item in the list by writing this
line of code:
```python
fName = fName[0]
self.label.setPixmap(QPixmap(filePath).scaled(self.width(), self.height(), Qt.KeepAspectRatio))
```
The second line in this section loads the image to the top label and scales it so
that it adapts to the windows size as well as keeping the images aspect ratio.

### Editing label_image.py

Before we go any further, we must edit some parts of the ```label_image.py```
script in order to allow us to pass values to our UI script. This is simple
enough, first we must place all the code in the ```label_image.py``` script into
a subroutine which we will call ```label```. We also want to pass the file path
so we will mention that when defining the subroutine in the brackets. We will
then make the variable ```imagePath``` equal to ```filePath```. We will then get
rid of the final for loop in the program and replace it with this:
```python
human_string = label_lines[top_k[0]]
score = predictions[0][top_k[0]]
score *= 100
return (human_string, score)
```
This code takes the top prediction and converts its score to a percentage. It
then returns these values so that they can be used in our UI script. Your
```label_image.py``` script must now look like this:
```python
import os, sys

import tensorflow as tf
def label(filePath):
    os.environ['TF_CPP_MIN_LOG_LEVEL'] = '2'

    # change this as you see fit
    image_path = filePath

    # Read in the image_data
    image_data = tf.gfile.FastGFile(image_path, 'rb').read()

    # Loads label file, strips off carriage return
    label_lines = [line.rstrip() for line
                       in tf.gfile.GFile("tensor/retrained_labels.txt")]

    # Unpersists graph from file
    with tf.gfile.FastGFile("tensor/retrained_graph.pb", 'rb') as f:
        graph_def = tf.GraphDef()
        graph_def.ParseFromString(f.read())
        tf.import_graph_def(graph_def, name='')

    with tf.Session() as sess:
        # Feed the image_data as input to the graph and get first prediction
        softmax_tensor = sess.graph.get_tensor_by_name('final_result:0')

        predictions = sess.run(softmax_tensor, \
                 {'DecodeJpeg/contents:0': image_data})

        # Sort to show labels of first prediction in order of confidence
        top_k = predictions[0].argsort()[-len(predictions[0]):][::-1]

        human_string = label_lines[top_k[0]]
        score = predictions[0][top_k[0]]
        score *= 100
        return (human_string, score)
```

### Final Stretch

In order to retrieve the returned values in our UI script, we must first import
the ```label_image```. To do this, we must add this line to the top of the
program:
```python
from tensor.label_image import *
```
Then, in the ```openImage``` subroutine we must add these lines of code:

```python
human_string, score = label(filePath)
self.line.setText(human_string + " " + str(round(score, 2)) + "%")
```

The first line simply receives the two variables and then the next line puts the
variables into a string to be displayed in the bottom label. This subroutine
should now look like this:

```python
def openImage(self):
  fName = QFileDialog.getOpenFileName(self, 'Open Image', '', 'Image Files (*.jpg *.jpeg)')
  filePath = fName[0]
  human_string, score = label(filePath)
  self.line.setText(human_string + " " + str(round(score, 2)) + "%")
  self.label.setPixmap(QPixmap(filePath).scaled(self.width(), self.height(), Qt.KeepAspectRatio))
```

And that's it, you now have ultimate bragging rights. I hope you enjoyed this
tutorial series and as always, all the files you need are at [https://github.com/royabouhamad/TensorFlow-Image-Identifier](https://github.
com/royabouhamad/TensorFlow-Image-Identifier) and you can contact me through any
of my above listed cotact methods.
Thanks for reading!
