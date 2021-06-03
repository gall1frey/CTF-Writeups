# Cyber Apocalypse CTF 2021 Writeups
Author: [Gallifrey](https://github.com/gall1frey)

### Contents
1. 2nd MAY: [&aaa](#aaa)
2. 3rd MAY: [Waiting Game](#waiting-game)
3. 4th MAY: [Countdown Baseball](#countdown-baseball)
4. 5th MAY: [neat-rsa](#neat-rsa)
5. 6th MAY: [Horst](#horst)
6. 8th MAY: [Wordbook](#wordbook)
7. 9th MAY: [Introductrory ICICLE](#introductory-icicle)
8. 13th MAY: [Two Loves](#two-loves)
9. 14th MAY: [Broken Bear](#broken-bear)
10. 15th MAY: [Neat RSA 2](#neat-rsa-2)
11. 16th MAY: [An ICICLE to remember](#an-icicle-to-remember)
12. 17th MAY: [Boxed In](#boxed-in)
13. 18th MAY: [Guess the Password](#guess-the-password)

## &aaa<a name="aaa"></a>
```
Description
Pwn my server. Connect with nc oreos.ctfchallenge.ga 7331.

Attachments
https://imaginary.ml/r/3727-aaa

Author
Eth007

Points
30
```
### Solution
Spam the input with 'AAAAAAAAAAAAAAAAAA'. This rewrites the variable checking whether you are the admin.

The flag is:
```
ictf{buff3r_0verfl0w_1s_d4ng3r0us}
```

## Waiting Game<a name="waiting-game"></a>
```
Description
Life's a waiting game... sometimes you have to wait for a while to get ahead

Attachments
https://waiting-game.max49.repl.co/

Author
Max49

Points
30
```
### Solution
Going to the link, we find a page that says ```Welcome! You'll get the flag eventually! You might just have to wait a some time (or a long time)!```.
On inspecting the web page, we come across a meta tag in the header that says ```<meta http-equiv="refresh" content="100000000000000; url = /0x666c6167.html">```.
This is a redirect that will occur after almost a century. Let's speed up this process by simply visiting the link in the url.

The flag is:
```
ictf{a_r3a11y_n1ce_wa1t1ng_gam3}
```

## Countdown Baseball<a name="countdown-baseball"></a>
```
Description
Waiting for the 10 seconds its going to take people to solve this.

Attachments
https://imaginary.ml/r/A1F0-countdown-baseball.txt

Author
FIREPONY57

Points
75
```
### Solution

We're given a file with a HUGE binary string, ```001100000011000000110001001100100011000100110000001100000011000000110...``` On decoding it, we get a base 3 number. Decoding the base 3 string to ASCII we get a base 4 number. Repeating this process all the way to base 10 gets us the flag.

A script to automate this:
```python
from Crypto.Util.number import long_to_bytes

f = open('countdown-baseball.txt','r')
data = f.read().strip()
f.close()

def chunkify(l,n):
    return [l[i:i+n] for i in range(0, len(l), n)]

def max_pow(base):
    i = 0
    while pow(base,i) <= 255:
        i += 1
    return i

base = 2
test = data

while base < 10:
    test = chunkify(test,max_pow(base))
    d = ''
    #print(base+1)
    for i in test:
        d += chr(int(i,base))
    #print(d)
    base += 1
    test = d
    #print(test, len(test), long_to_bytes(test),len(long_to_bytes(test)), sep = '\n')

l = list()
i = 0

while i < len(test):
    j = 0; m = '0'
    while int(m) < 32 or int(m) > 126:
        m += test[i+j]
        j += 1
    l.append(chr(int(m)))
    i += j

print(''.join(l))
```

The flag is:
```
ictf{b@s3s_g@10r3}
```

## neat-rsa<a name="neat-rsa"></a>
```
Description
RSA is pretty neat, especially when a source is provided.

Attachments
https://imaginary.ml/r/C2F4-main.py https://imaginary.ml/r/A9AA-rsa.txt

Author
ainz

Points
65
```
### Solution
```python
p, q, r = [getPrime(512) for _ in range(3)]
n1, n2 = p * q, q * r
```
The source code provided tells us that 2 ns were generated, and their GCD is q.
and a four bit random number elusive exists, but we don't know what that is.

We have:
```
e = 65537
n1 = 129851722357947445345870990963660632711447373178135819868899984182659557798630904818907679897656671090445195493024305192175998357725180728308641342052877827178063748226956003293995628809809946890377677500727765422671309139577956460806060356061643066260620532143971581138444887013251083401740953016800526561369
n2 + n1 * elusive = 354020441161271454238824233113483251424739165273603253089281992413904299790750998993004221545361940017125675049620866260946107295019570616958455252820784279420662013197185267268535478305987123286908618637917837561207744990593948143827775524857529903243255631176220324605682439947302448209103973570727852816857845902079
ct = 99860436465836391865653329628063911057946675204553849493647896562280037248239340521968032740474787220879813274343214941793443913638426853461368251451691055571860562982592326807064465612371566427699272833229440737810391158841432366489390151103656479013891591734401496841642436054950303923680083243969093719406
```
Since elusive is very small compared to n1 and n2, we can safely assume ```elusive = (n2 + n1 * elusive) // n1```

Also, ```q = gcd(n1, n2)```
From there, we can find p and q.
```
q = int(gcd(n1,n2))
p = int(n1//q)
```
From p and q we can compute phi. ```phi = (p-1)*(q-1)```

Computing d by ```d = modinverse(e,phi)```

And finally decrypting the ciphertext by ```pt = pow(ct,d,n1)```

A script to make our job easier:
```python
#Solution to neat-rsa
from math import gcd
from Crypto.Util.number import long_to_bytes
#Given:
n1 = 129851722357947445345870990963660632711447373178135819868899984182659557798630904818907679897656671090445195493024305192175998357725180728308641342052877827178063748226956003293995628809809946890377677500727765422671309139577956460806060356061643066260620532143971581138444887013251083401740953016800526561369
#n2 + n1 * elusive =
x = 354020441161271454238824233113483251424739165273603253089281992413904299790750998993004221545361940017125675049620866260946107295019570616958455252820784279420662013197185267268535478305987123286908618637917837561207744990593948143827775524857529903243255631176220324605682439947302448209103973570727852816857845902079

ct = 99860436465836391865653329628063911057946675204553849493647896562280037248239340521968032740474787220879813274343214941793443913638426853461368251451691055571860562982592326807064465612371566427699272833229440737810391158841432366489390151103656479013891591734401496841642436054950303923680083243969093719406

e = 65537

elusive = x//n1
n2 = x - (n1*elusive)

#n2 = q*r and n1 = p*q. Therefore, GCD is q.
#finding GCD with modified euclid's algorithm
q = int(gcd(n1,n2))
#then find p from n1
p = int(n1//q)
phi = int((p-1)*(q-1))
#We have e, p, q and phi.
#Do mod inverse to get d.
def modInverse(a, m):
    m0 = m
    y = 0
    x = 1
    if (m == 1):
        return 0
    while (a > 1):
        q = a // m
        t = m
        m = a % m
        a = t
        t = y
        y = x - q * y
        x = t
    if (x < 0):
        x = x + m0
    return x
d = int(modInverse(e,phi))
#Get plaintext
pt = long_to_bytes(pow(ct,d,n1)).decode()
print(pt)
```

The flag is:
```
ictf{why_c@nt_1_ev3r_c0m3_up_with_c0ol3r_flag5_:rooNobooli:}
```

## Horst<a name="horst"></a>
```
Description
I found this interesting cipher structure. Could you check if it's secure?

Attachments
https://imaginary.ml/r/5188-horst.py
https://imaginary.ml/r/A0D2-output.txt
Author
Robin_Jadoul

Points
75
```
### Solution
Apparently it was a Fiestel Cipher, but I didn't know that till after I got the flag. It was, hoewever, fairly simple to reverse the crypto algorithm.

On inspecting the python script, I found that the main operation included dividing the flag into two halves, XORing the left half with the SHA256 digest of the right half, then concatenating both parts in the reverse order, i.e. right half before the left half.

This operation was performed 25 times.

A python script to reverse this:
```python
import hashlib

ROUNDS = 25
BLOCKSIZE = 32

def xor(a, b):
    assert len(a) == len(b)
    return bytes(x ^ y for x, y in zip(a, b))

def F(x):
    return hashlib.sha256(x).digest()

def round(x):
    assert len(x) == 2 * BLOCKSIZE
    L = x[:BLOCKSIZE]
    R = x[BLOCKSIZE:]
    return xor(F(L),R) + L

def decrypt(x):
    for _ in range(ROUNDS):
        x = round(x)
    return x

if __name__ == "__main__":
    f = open("output.txt", "r")
    data = f.read().strip()
    flag = bytes.fromhex(data)
    print(decrypt(flag).decode())
```

The flag is:
```
ictf{imagine_not_using_a_key_for_your_feistel_cipher_67e63cc381}
```

## Wordbook<a name="wordbook"></a>
```
Description
"I'm going to learn some new words..." *opens book

Attachments
nc oreos.imaginary.ml 10069

Author
FIREPONY57

Points
50
```
### Solution
On connecting to the server, we get an encrypted flag. This changes each time you connect. Also, you are given an option to encrypt a character.

Looks like it is a substitution cipher, except letters are substituted by random unicode characters.

We simply send to the server each printable character and note the reply. We then substitute that in the encryted flag to decrypt it.

The python script for it:
```python
from pwn import remote
import string
d = dict()

r = remote('oreos.imaginary.ml',10069)

res = r.recvuntil('\n')
print(res.decode())
flag = r.recvuntil('\n').decode().strip()
print(flag)
alf_num = string.printable
d = dict()
for i in range(len(alf_num)):
    r.recvuntil('? ').decode()
    print(alf_num[i])
    r.send(alf_num[i]+'\n')
    r.recvuntil('\n').decode().strip()
    key = r.recvuntil('\n').decode().strip()
    d[key] = alf_num[i]

f = ''

for i in flag:
    f += d[i]

print(f)
```

The flag is:
```
ictf{d1ct10n@ri3s_m@k3_y0u_sm@r73r}
```

## Introductory ICICLE<a name="introductory-icicle"></a>
```
Description
Introducing ICICLE (Imaginary Ctf Instruction Collection for Learning assEmbly)! It's a programming challenge, so I didn't want to use an existing language and give some people an unfair advantage, so I made my own! See the spec and implement an interpreter to run the provided file, and get the flag.

Attachments
https://imaginary.ml/r/8DFE-chall1.s, https://imaginary.ml/r/1560-spec_v1.md

Author
puzzler7

Points
100
```
### Solution
There are many ways to go about this challenge, and I didn't want to write an entire interpreter. So I simply used find and replace to get rid of useless statements and change the syntax so it matches python.

The registers were now different variables, and instructions like mult, xor and intstr were user defined functions that I wrote based on the markdown file provided.

The final product looked like this
```python
from pwn import xor

def mod(a,b):
    return a%b

def mult(a,b):
    if type(a) == int and type(b) == int:
        return a*b
    elif type(a) == int:
        return b*a
    else:
        return a*b

def intstr(a):
    h = format(a,'x')
    c = ''.join(chr(int(h[i:i+2],16)) for i in range(0,len(h),2))
    return c

r1 = 208711228920503662662639293024172016224701481804426304526365809516810887455991148719975
r1 = intstr(r1)
r2 = 7696217
r2 = intstr(r2)
r2 = r2[::-1]
r1 = xor(r1, r2)
r2 = "dluohs"
r2 = r2[::-1]
r1 = xor(r1, r2)
r2 = "really"
r1 = xor(r1, r2)
r2 = 435744764535
r2 = intstr(r2)
r2 = r2[::-1]
r1 = xor(r1, r2)
r2 = 6645876
r2 = intstr(r2)
r2 = r2[::-1]
r1 = xor(r1, r2)
r2 = 138296649583178892197523049
r2 = intstr(r2)
r2 = r2[::-1]
r1 = xor(r1, r2)
r2 = 28254586044378729
r2 = intstr(r2)
r2 = r2[::-1]
r1 = xor(r1, r2)
r2 = "fo"
r2 = r2[::-1]
r1 = xor(r1, r2)
r2 = "gniod"
r2 = r2[::-1]
r1 = xor(r1, r2)
r2 = 1936287860
r2 = intstr(r2)
r2 = r2[::-1]
r1 = xor(r1, r2)
r2 = 157746064591442268284274
r2 = intstr(r2)
r2 = r2[::-1]
r1 = xor(r1, r2)
r2 = 1936287828
r2 = intstr(r2)
r2 = r2[::-1]
r1 = xor(r1, r2)
r2 = "is"
r1 = xor(r1, r2)
r2 = 7628901
r2 = intstr(r2)
r1 = xor(r1, r2)
r2 = "first"
r1 = xor(r1, r2)
r2 = 28265
r2 = intstr(r2)
r2 = r2[::-1]
r1 = xor(r1, r2)
r2 = "a"
r2 = r2[::-1]
r1 = xor(r1, r2)
r2 = 32481164390658862
r2 = intstr(r2)
r1 = xor(r1, r2)
print(r1)
r2 = 100
r2 = mod(r2, 7)
r3 = mult(r2, 5)
r3 = intstr(r3)
print(r3)
```
Running this script got me the flag. However, this challenge can be solved by writing an interpreter as well. That had to be done in a future challenge.

The flag is:
```
ictf{th3_w@rm3st_ICICLE_y0u've_s33n}
```

## Two Loves<a name="two-loves"></a>
```
Description
The Bard, too, saw hidden messages

Attachments
https://imaginary.ml/r/38C8-TwoLoves.txt

Author
StealthyDev

Points
75
```
### Solution
This challenge included whitespace steganography. There were a lot of non ASCII characters. Viewing all the non printable characters showed that there were mainly two kinds of whitespace characters: ```\u2005``` and ```\u2006```. The pattern in which they repeated looked interesting.

On extracting only those characters from the entire text and substituting ```\u2005``` for ```0``` and ```\u2006``` for ```1``` gets us the flag in binary.

The script used:
```python
f = open('TwoLoves.txt','r')
text = f.read()
f.close()
b = ''
for i in range(len(text)):
	if text[i] == '\u2005':
		b += '0'
	elif text[i] == '\u2006':
		b += '1'

def chunkify(l,n):
    return [l[i:i+n] for i in range(0, len(l), n)]

flag = ''
for i in chunkify(b,8):
	if i != ' ':
		flag += chr(int(i,2))

print(flag)
```

The flag is:
```
ictf{7w0_Sp4cE5_m4y8E_m0rE}
```


## Broken Bear<a name="broken-bear"></a>
```
Description
My bear is broken. Can you fix it? (If you have nitro, please submit your flag through the website.)

Attachments
https://imaginary.ml/r/B8C2-broken_bear.png

Author
cleverbear57

Points
50
```
### Solution
This was a simple forensics challenge where the magic bytes of the given png file were wrong.

Viewing the hexdump of the image file, we see:
```
0000000 1010 1010 1010 1010 0000 0d00 4849 5244
0000010 0000 6009 0000 4006 0208 0000 7200 eecb
```
The ```1010 1010 1010 1010``` part looks wrong. Replacing it with the actual magic bytes of a png file (See wikipedia, list of magic bytes), ```5089 474e 0a0d 0a1a```, we fix the image. The flag is visible on the top left corner of the image.
[](./fixed.png)

The flag is:
```
ictf{fixed_the_broken_bear!_:rooYay:}
```


## Neat RSA 2<a name="neat-rsa-2"></a>
```
Description
Slash the flag into two and you get even neater RSA.

Attachments
https://imaginary.ml/r/3711-main.py https://imaginary.ml/r/435D-rsa.txt

Author
ainz

Points
80
```
### Solution
This challenge is similar to the first RSA. However, there is one difference: The flag is divided into two parts. The first part is encrypted using the public key ```(n1,e)``` and the second is encrypted using the public key ```(n2,e)```.

The principle is the same as in [neat-rsa](neat-rsa). We find n1 and n2, then their GCD gives us q.

n1/q = p and n2/q = r

Here's where it differs from the first neat-rsa challenge. Since there are two public keys, there are two private keys.
```
phi1 = (p-1)*(q-1)      |   phi2 = (q-1)*(r-1)
d1 = modinverse(e,phi1) |   d2 = modinverse(e,phi2)
pt1 = pow(ct1,d1,n1)    |   pt2 = pow(ct2,d2,n2)
```
Solution script in python:
```python
#Solution to neat-rsa
from math import gcd
from Crypto.Util.number import long_to_bytes
#Given:
n1 = 98878585413420111471778656207638123975045211110656635970353347804869821302128842579958132410164515984259619169929609945243536843670867542016574747233040679074618910120171874883171653789706004625585438942339993393132921750420039791951387054706994507931530438002290410571520916054779248915404282444582688776277
#n2 + n1 * elusive =
x = 23595050757049056653463781748908038714868820902347735866124222218402456765220848777959603906702182606609476482684400919492347621023868708056210926343300138193461206211069005904795305622301213504748755998260018960789025669532682736280541355051103939458097663406797458686161938508453797698114083305795145127834762011086

ct1 = 64026441903198883744369748134116292929417128928415919615193619744877326668763636655522459322303056967804077972304432318124858743857665689618028707132671414242191635317047114847231019029930720381544652491326732165539455889323697800005143167353360693062303891506614029050256512544296411765216352481621764140551
ct2 = 23003322546612657966116591807431974059707146433471965126279420960433941874599757234276535458189194904060793206616208668805427453620513463780755100601367309101781656977722986585104491777071710597649117358596783311256495539269551802726889755947474620768927354082697233758955888707848918894102335373608237447388

e = 65537

elusive = x//n1
n2 = x - (n1*elusive)

#n2 = q*r and n1 = p*q. Therefore, GCD is q.
#finding GCD with modified euclid's algorithm
q = int(gcd(n1,n2))
#then find p from n1
p = int(n1//q)
phi1 = int((p-1)*(q-1))
#We have e, p, q and phi.
#Do mod inverse to get d.
d1 = pow(e,-1,phi1)
#Get plaintext
pt = long_to_bytes(pow(ct1,d1,n1)).decode()
print(pt,end = '')

q = int(gcd(n1,n2))
#then find p from n1
p = int(n2//q)
phi2 = int((p-1)*(q-1))
d2 = pow(e,-1,phi2)
print(long_to_bytes(pow(ct2,d2,n2)).decode())
```

The flag is:
```
ictf{rsa_c@n_be_scary_but_th3_0nly_th1ng_th3y_f3@r_15_y0u_3qu1pped_w1th_th3_Z3_5MT_50lv3r}
```


## An ICICLE to remember<a name="an-icicle-to-remember"></a>
```
Description
The last interpreter was just simple arithmetic, but we all know that a language isn't interesting if you can't loop! Add in some control flow, and while you're at it, add in some memory, too. Check the updated spec, changes are bolded or marked with (new!). Once again, run the file to get the flag.

Attachments
https://imaginary.ml/r/60C8-spec_v2.md, https://imaginary.ml/r/E0E3-chall2.s

Author
puzzler7

Points
100
```
### Solution
This challenge is similar to the previous ICICLE challenge, except it's harder since this time, memory and loops are added. So this time I wrote a simple interpreter in Python.
```python
from pwn import xor
#FUCK IT, REGEX TIMEEEEE
import re

file_name = input("NAME OF PROGRAM FILE: ").strip()

#initializing registers, inst pointers and memory
r = list(0 for i in range(16))
rip = 0
i = 0
end_flag = False
mem = list(0 for i in range(pow(2,16)))

#All operations as functions

def add(a,b):
    if type(a) == int and type(b) == int:
        return a+b
    return str(a)+str(b)

def sub(a,b):
    return a-b

def mult(a,b):
    if type(a) == int and type(b) == int:
        return a*b
    elif type(a) == int:
        return b*a
    else:
        return a*b

def div(a,b):
    return a//b

def mod(a,b):
    return a%b

def xor_(a,b):
    return xor(a,b)

def and_(a,b):
    return a & b

def or_(a,b):
    return a | b

def rev(a):
    if type(a) == int:
        return int(str(a)[::-1])
    else:
        return a[::-1]

def strint(s):
    s = s.encode()
    h = ''
    for i in s:
        h += hex(i)[2:]
    return int(h,16)

def intstr(a):
    h = format(a,'x')
    c = ''.join(chr(int(h[i:i+2],16)) for i in range(0,len(h),2))
    return c

def pr(a):
    if type(r[int(a[1:])]) == bytes:
        print(r[int(a[1:])].decode(),end = '')
    else:
        print(r[int(a[1:])],end = '')

def readstr():
    return input("READSTR INPUT:")

def readint():
    return int(input("READINT INPUT:"))

#Resolves addresses
def resolve_add(x):
    global r
    if x[0] == '[' and x[-1] == ']':
        return mem[resolve_add(x[1:-1])]
    elif len(re.findall(r'^r([0-9]{1,2})',x)) > 0:
        return r[int(re.findall(r'^r([0-9]{1,2})',x)[0])]
    elif x == 'rip':
        return rip
    else:
        return int(x)

func_dict = {'add': add, 'sub': sub, 'mult': mult, 'intstr': intstr, 'div':div, 'mod':mod, 'xor':xor_, 'and': and_, 'orr':or_, 'rev': rev, 'strint':strint, 'pr':pr, 'readstr':readstr, 'readint':readint}

#To handle instructions with 2 args. Takes in tuple (isnt, dest, src)
def handle_2_args(match):
    inst, dest, src = match
    global rip
    #if instruction is mov
    if inst == 'mov':
        '''
            possibilities:
                1. mov [1234], 1234     2. mov r1, 1234
                3. mov r1, r2           4. mov r1, [r2]
                5. mov rip, r2          6. mov r1, [[r3]]
                7. mov [r15], r14       8. mov r2, "asd"
        '''
        #CASE 1
        if dest[0] == '[' and dest[-1] == ']' and dest[1:-1].isdigit() and src.isdigit():
            mem[int(dest[1:-1])] = int(src)
        #CASE 3
        elif len(re.findall(r'^r([0-9]{1,2})',dest)) > 0 and len(re.findall(r'^r([0-9]{1,2})',src)) > 0:
            r[int(re.findall(r'^r([0-9]{1,2})',dest)[0])] = r[int(re.findall(r'^r([0-9]{1,2})',src)[0])]
        elif len(re.findall(r'^r([0-9]{1,2})',dest)) > 0:
            #CASE 2
            if src.isdigit():
                r[int(re.findall(r'^r([0-9]{1,2})',dest)[0])] = int(src)
            #CASE 8
            elif src[0] == '"' or src[0] == "'":
                r[int(re.findall(r'^r([0-9]{1,2})',dest)[0])] = src[1:-1]
            #CASE 4 and 6
            else:
                r[int(re.findall(r'^r([0-9]{1,2})',dest)[0])] = resolve_add(src)
        #CASE 7
        elif dest[0] == '[' and dest[-1] == ']' and len(re.findall(r'^r([0-9]{1,2})',src)) > 0:
            mem[r[int(re.findall(r'^r([0-9]{1,2})',dest[1:-1])[0])]] = r[int(src[1:])]
        elif dest == 'rip':
            if not src.isdigit() and len(re.findall(r'^r([0-9]{1,2})',src)) > 0:
                rip = r[int(src[1:])]
    elif inst == 'jnz':
        if dest[0] == '[' and dest[-1] == ']':
            if mem[r[int(re.findall(r'^r([0-9]{1,2})',dest[1:-1])[0])]] != 0:
            #if resolve_add(dest) != 0:
                handle_1_args(('j',src))
                return
        else:
            if r[int(re.findall(r'^r([0-9]{1,2})',dest)[0])] != 0:
            #if resolve_add(dest) != 0:
                handle_1_args(('j',src))
                return
    elif inst == 'rev':
        if len(re.findall(r'^r([0-9]{1,2})',src)) > 0:
            r[int(re.findall(r'^r([0-9]{1,2})',dest)[0])] = rev(r[int(src[1:])])
        elif src == 'rip':
            r[int(re.findall(r'^r([0-9]{1,2})',dest)[0])] = rev(rip)
        else:
            r[int(re.findall(r'^r([0-9]{1,2})',dest)[0])] = rev(src)
    elif inst == 'intstr':
        r[int(dest[1:])] = intstr(r[int(src[1:])])

#To handle instructions with 3 args. Takes in tuple (isnt, dest, src1, src2)
def handle_3_args(match):
    inst, dest, src1, src2 = match
    global rip
    #if src1 is a register
    if len(re.findall(r'^r([0-9]{1,2})',src1)) > 0:
        src1 = r[int(re.findall(r'^r([0-9]{1,2})',src1)[0])]
    #if src1 is rip
    elif src1 == 'rip':
        src1 = rip
    #if src2 is a register
    if len(re.findall(r'^r([0-9]{1,2})',src2)) > 0:
        src2 = r[int(re.findall(r'^r([0-9]{1,2})',src2)[0])]
    #if src2 is rip
    elif src2 == 'rip':
        src2 = rip
    #if src2 is a const
    else:
         src2 = int(src2)
    #Compute
    ans = func_dict[inst](src1, src2)
    #if dest is register
    if len(re.findall(r'^r([0-9]{1,2})',dest)) > 0:
        r[int(re.findall(r'^r([0-9]{1,2})',dest)[0])] = ans
    #if dest is rip
    elif dest == 'rip':
        rip = ans

#To handle instructions with 1 args. Takes in tuple (isnt, dest)
def handle_1_args(match):
    inst, dest = match
    global rip, labels
    #if instruction is j, set rip to dest address
    if inst == 'j':
        if dest == 'end':
            exit_flag = True
        for j in labels:
            if j[1] == dest:
                rip = j[0]
                break
    #if instruction is pr, print
    elif inst == 'pr':
        pr(dest)

#Read instructions
f = open(file_name,'r')
instructions = f.readlines()
f.close()

#Remove all comments
instructions = list(filter(lambda x: x[0] != '#',instructions))
#Find locations of labels
labels = [(1, 'main'), (4215, 'prval'), (4218, 'notzero'), (4227, 'end')]
l = list(x[1] for x in labels)
instructions = list(filter(lambda x: x.strip()[:-1] not in l,instructions))
#Insert dummy instruction so instructions are numbered from 1
instructions.insert(0,"dummy")

#All the instructions used in this program
inst = set()
for i in instructions[1:-1]:
    inst.add(i.split()[0])
print("ALL INSTRUCTIONS: ", end = '')
print(inst)

#Regex to match different kinds of instructions
regex_2_args = r'([\w]+) ([\[\]\w]+), ([\[\]\w"\']+)'
regex_3_args = r'([\w]+) ([\[\]\w]+), ([\w]+), ([\[\]\w]+)'
regexFLAG = r'([\w]+):$'
regex_1_args = r'([\w]+) ([\w]+)'

#Execute.
if 'j' in inst or 'jnz' in inst or 'jl' in inst or 'jz' in inst:
    while end_flag == False:
        i = rip
        rip += 1
        match_3_args = re.findall(regex_3_args, instructions[i])
        match_2_args = re.findall(regex_2_args, instructions[i])
        match_1_args = re.findall(regex_1_args, instructions[i])
        if len(match_3_args) == 1:
            handle_3_args(match_3_args[0])
        elif len(match_2_args) == 1:
            handle_2_args(match_2_args[0])
        elif len(match_1_args) == 1:
            handle_1_args(match_1_args[0])
        if rip == 4214:
            end_flag = True
else:
    for i in instructions:
        match_3_args = re.findall(regex_3_args, i)
        match_2_args = re.findall(regex_2_args, i)
        match_1_args = re.findall(regex_1_args, i)
        if len(match_3_args) == 1:
            handle_3_args(match_3_args[0])
        elif len(match_2_args) == 1:
            handle_2_args(match_2_args[0])
        elif len(match_1_args) == 1:
            handle_1_args(match_1_args[0])
print()
```

The flag is:
```
ictf{0nly_@n_ICICLE_!s_l0vli3r_th@n_@_tr33}
```


## Boxed In<a name="boxed-in"></a>
```
Description
Box outside the think.

Attachments
https://imaginary.ml/r/BE85-boxed_in.c, nc oreos.imaginary.ml 30000

Author
Robin_Jadoul

Points
100
```
### Solution
This was an interesting challenge, I loved it. You are provided with the source code of the executable running on the server. Executing it, we see it is a game of tic tac toe.

The thing about tic tac toe is that it is impossible to win unless your opponent makes a mistake. So, beating the code normally isn't possible.

So we look through the source code, mainly these parts of it.
```C
//The part which reads in an int
int readint() {
    ...
        char* line = NULL;
        getline(&line, &len, stdin);
    ...
        int res = atoi(line);
    ...
}
```
Here, we see that the integer from the string line is directly stored in the integer res. So, though the function performs checks for - in the input (so we don't enter negative numbers), we can cause integer overflow to store negative numbers in res.
Secondly, we look at:
```C
//The parts that check if a winning line has been made
int is_(int x, int y, int player) {
    if (x < 0 || x >= 3 || y < 0 || y >= 3) return 0;
    return field.grid[x][y] == player;
}

int is_winning_line(int x, int y, int dx, int dy, int player) {
    return (is_(x + dx, y+dy, player) + is_(x + 2*dx, y + 2*dy, player) + is_(x - dx, y - dy, player) + is_(x - 2*dx, y - 2*dy, player)) >= 2;
}
```
Here we see that the ```is_``` function checks whether
  1. The indices are in range 0-2
  2. The box in the grid corresponding to the indices are occupied by the player.

And the function ```_is_winning_line``` checks if the boxes **around it** form a winning line.

We use this knowledge to beat the program by marking a box out of bounds, kinda like this picture: [](https://previews.123rf.com/images/manonteravest/manonteravest1808/manonteravest180800012/108865743-think-outside-the-box-tic-tac-toe-game-text.jpg)

First, we mark a box at (1,2), then at (0,2) and finally at (-1,2). The -1 can be achieved by giving ```4294967295``` as the input. 4294967295 is a four bit number with all its bits 1. That effectively makes it -1 when it is casted to a 4 bit integet (default int in C). That gets us the flag.

One thing to note is that the (-1,2) box MUST BE FILLED AT THE END.

The flag is:
```
ictf{y0u_mu$t_r3al1ze_there_i$_n0_box}
```
