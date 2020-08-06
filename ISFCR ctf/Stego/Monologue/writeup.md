# Monologue
Author: [Gallifrey](https://github.com/gall1grey)


# Challenge

```
A text file called monologue.txt was provided.
```

# Solution

On simply opening the file, you can't find the flag, it's not in the text.
I opened up my file in wordpad, and found that it had a lot of zero width chars.
for simplicity, I did a quick find and replace, and changed all those zero width chars to   ```~```

I extracted them with a script and fond the flag

Here's the script:

```
f = open('monologue.txt','r')

l = f.read()
flag = ''

c = 0

for i in l:
    if i == '~':
        c += 1
    else:
        flag += chr(c)
        c = 0
            
print(flag)
```

The flag is:
```
CTF{burningbright}
```
(It is not in the usual '''flag{text}''' format
