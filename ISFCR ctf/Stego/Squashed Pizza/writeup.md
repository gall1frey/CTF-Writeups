# Squashed Pizza
Author: [Gallifrey](https://github.com/gall1grey)

# Requirements

- Python: opencv and numpy

# Challenge

```
An image file was provided, pixelopia.png
It said that the file was squashed, like a pizza.
```

# Solution

The file is literally, _squashed_. All you need to do is change its dimensions

So, we write a python code for that:

```
import cv2
from math import sqrt
import numpy as np

file = "./pixelopia.png"
img = cv2.imread(file , 0)
print(img.shape)
s = img.shape[0]*img.shape[1]
l = int(sqrt(s))
b = s-l
cv2.imshow('img' , img)
k = cv2.waitKey(0)
#if k == 27:
#    cv2.destroyWindow('img')
#resize_img = cv2.resize(img  , (499 , 261))
resize_img = np.reshape(img,(l,l)).astype('float32')
cv2.imshow('img' , resize_img)
x = cv2.waitKey(0)
if x == 27:
    cv2.destroyWindow('img')
```

The flag is:
```
CTF{84512}
```
