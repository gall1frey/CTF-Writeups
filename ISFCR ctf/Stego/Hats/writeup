# Too Much Time
Author: [Gallifrey](https://github.com/gall1frey)

# Challenge

```
Don't really remember the text, but there was this picture.
```

# Solution

After ```exif``` didn't provide any real clues, I tried binwalk, and found that there was a ```.zlib``` file 
hidden in the picture.

```
$ binwalk hats.png 

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 2048 x 2544, 8-bit/color RGBA, non-interlaced
41            0x29            Zlib compressed data, compressed
```

on extracting it the image, we get two text files, one of which contains the flag.

The flag is:
```
flag{alright_you_have_taken_too_much_time}
```
