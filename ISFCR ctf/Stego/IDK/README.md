# Idk the name, sorry
Author: [Gallifrey](https://github.com/gall1frey)

# Challenge

```
There was a link to a html page that won't open.
```

# Solution

The page won't open. It, however, downloads.

upon inspection of the javascript inside it, I found that the line 
```this[fav_song[0]+fav_song[2]+fav_song[14]+fav_song[31]+fav_song[40]]();```
causes it to close. So I commented out that line.

Upon further inspection, I found this interesting:
```html
document[_0x4a36('0xa')](_0x4a36('0x8')), document['write'](_0x4a36('0x3') + _0x4a36('0xb')), document['write'](_0x4a36('0x9') + _0x4a36('0x7') + 'is\x20limited' + '\x0a'), document[_0x4a36('0xa')]('\x20\x20\x20\x20\x20\x20\x20\x20</' + 'title>\x0a'), document[_0x4a36('0xa')](_0x4a36('0x1') + '>\x0a'), document[_0x4a36('0xa')](_0x4a36('0x2') + _0x4a36('0x0') + _0x4a36('0x4')), document[_0x4a36('0xa')](_0x4a36('0xc') + _0x4a36('0x6')), document[_0x4a36('0xa')](_0x4a36('0x5') + '>');
```

upon decoding these lines one by one, I got the flag.

The flag is:
```
ctf{13273}
```
