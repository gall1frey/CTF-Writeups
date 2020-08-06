# Composites
Author: [Gallifrey](https://github.com/gall1grey)

# Challenge
```
A text file was given, with lots of numbers in it.
A hint along the lines of "I'm not the prome candidate" was also given
```

# Solution

The file contains numbers. 
Based on this, and the emphasis on prime and composite numbers, I figured that the flag must be 
the one composite number inside the file.

writing a code for it:

```
import sympy 

f = open('prime_candidate.txt','r')
l = f.readlines()
for i in l:
	if not sympy.isprime(int(i)):
    print(i)
```

this gave me the output:

```
70179
```

The flag is:
```
CTF{70179}
```
