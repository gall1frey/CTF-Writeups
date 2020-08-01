# Common Place
Author: [gallifrey](https://github.com/gall1frey)

# Challenge

```
asd7138: can you find the flag here?
tcm3137: no, i dont see it
jwh8163: i cant find it either
rfc5785: i found it
asd7138: what!? where?!
jwh8163: tell us!

Connect here:
http://jh2i.com:50007
```

# Solution

The clue here is rfc5785
It refers to the "well-known" protocol, something like robots.txt.
Entering the directory ```/.well-known/```, we get ```flag.txt```, which contains the flag

```
flag{rfc5785_defines_yet_another_common_place}
```
