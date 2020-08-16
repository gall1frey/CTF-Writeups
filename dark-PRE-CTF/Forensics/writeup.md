# 1by2
Author: [Gallifrey](https://github.com/gall1frey)

## Challenge

```
1 / 2
walking over the bins...
meta meta meta...
1by2.jpg
```

## Solution

Opening the image ```OneBy2.png```, we get a partial flag.

![](OneBy2.png)

The title suggests that the flag is divided into two parts.
So, we try all the usual stuff, file, exiftool, binwalk.

Binwalk yeilds something interesting:
```
$ binwalk OneBy2.png

DECIMAL       HEXADECIMAL     DESCRIPTION
--------------------------------------------------------------------------------
0             0x0             PNG image, 1000 x 300, 8-bit/color RGBA, non-interlaced
537           0x219           Zlib compressed data, best compression
7817          0x1E89          Zip archive data, at least v2.0 to extract, compressed size: 19987, uncompressed size: 20239, name: half1.png
27950         0x6D2E          End of Zip archive, footer length: 22
```

on extracting it, along with the initial image, we get another image, ```half1.png```

![](half1.png)

The exifdata of this image contains the other part of the flag:

```
$ exiftool half1.png 
ExifTool Version Number         : 12.03
File Name                       : half1.png
Directory                       : .
File Size                       : 20 kB
File Modification Date/Time     : 2020:08:06 01:05:46-04:00
File Access Date/Time           : 2020:08:15 10:02:51-04:00
File Inode Change Date/Time     : 2020:08:15 10:01:42-04:00
File Permissions                : rw-r--r--
File Type                       : PNG
File Type Extension             : png
MIME Type                       : image/png
Image Width                     : 920
Image Height                    : 800
Bit Depth                       : 4
Color Type                      : Palette
Compression                     : Deflate/Inflate
Filter                          : Adaptive
Interlace                       : Noninterlaced
Palette                         : (Binary data 30 bytes, use -b option to extract)
XMP Toolkit                     : Image::ExifTool 11.88
Creator                         : darkCTF{Half_
Image Size                      : 920x800
Megapixels                      : 0.736
```

The flag is:
```
darkCTF{Half_4nd_h4Lf}
```

# RGBA

## Challenge

```
r - red
g - green
b - blue
a - ???
chall.png
```

## Solution

![](chall.png)

Using stegsolve and extracting data from all the alpha bits in the image, we get our flag

The flag is:
```
darkCTF{th3_fl4g_1s_tran5par3nt}
```

# SS

## Challenge

```
beep beep
doot doot
beeep.wav
```

## Solution

I made the mistake of playing this file on full volume with headphones on. Don't do that.
Use the SSTV decoder app from robot36.
It converts the sound signal into an image, like old timey TVs.

![](sstv.png)

The flag is:
```
darkCTF{s10w_Sc4n_t313v!s!on}
```

# Wolfie's Desk

## Challenge

```
Wolfie saved some flag in his disk can you find it?

imp.rar
```

## Solution

Extracting the rar file gives us a .E01 image. I mounted this using OSFMount.
There are a few folders on there, but the one named steg sounds interesting. It means this is probably a steg chall.

On analysing all the images on the disk, we can narrow our scope down to green logo.png.
Using zsteg to extract data from it, we get out flag.

The flag is:
```
darkCTF{d15k_f0r3ns1c5_i5_d0p3!!}
```

# Fake Sound

## Challenge

```
You need to research about this a lot, but wait is it fake?

fake.wav
```

## Solution

Using deepsound to decode the audio file, we get a text file called ```flag2.txt```

```
Yehhhh! darkCTF{What_appears_is_not}
```

The flag is:
```
darkCTF{What_appears_is_not}
```
