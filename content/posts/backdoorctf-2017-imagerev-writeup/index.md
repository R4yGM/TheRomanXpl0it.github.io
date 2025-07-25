---
title: backdoorctf 2017 - ImageRev Writeup
date: '2017-09-24'
lastmod: '2019-04-07T13:46:27+02:00'
categories:
- writeup
- backdoorctf17
tags:
- reverse
authors:
- malweisse
---

Looking at the code in [encrypt.py](/backdoorctf17/encrypt.py) we can see that the encryption function works with a pixel at once.

Analyzing the return value it is a tuple of 8 hex strings.

So a pixel is mapped to a 64 bytes hex string.

Now we can print all different pixel types in the [encrypted.txt](/backdoorctf17/encrypted.txt) file using a bit of python.

```python
f = open("encrypted.txt")
m = {}

while True:
    s = f.read(64)
    if s == "": break
    m[s] = m.get(s, 0) +1

f.close()

print "                        ENCRYPTED PIXEL                               OCCURRENCES"
print
for e in m:
    print e + "        " + str(m[e])

print
```

The pixel `709e80c88487a2411e1ee4dfb9f22a861492d20c4765150c0c794abd70f8147c` is present 5826 times, so it must be the background.

I choose to associate the background pixels to black and all other pixels to white.

Using pillow we can reconstruct the image to see the flag:

```python
from PIL import Image

f = open("encrypted.txt")
b = ""

while True:
    s = f.read(64)
    if s == "": break
    if s == "709e80c88487a2411e1ee4dfb9f22a861492d20c4765150c0c794abd70f8147c":
        b += chr(0) + chr(0) + chr(0)
    b += chr(255) + chr(255) + chr(255)

f.close()

print len(b)/3

i = Image.frombytes("RGB", (351, 21), b)
i.save("out.png")
```

Note: the pixels are 7371, before setting the image size (351x21 is good) we tried some times with different sizes.
