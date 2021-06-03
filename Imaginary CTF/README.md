# Cyber Apocalypse CTF 2021 Writeups
Author: [Gallifrey](https://github.com/gall1frey)

### Contents
1. [&aaa](#aaa)
2. [Waiting Game](#waiting-game)
3. [Countdown Baseball](#countdown-baseball)
4. [neat-rsa](#neat-rsa)
5. [Horst](#horst)
6. [Green Shell](#green-shell)


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
From p and q we can compute phi. ```phi = (p1-)*(q-1)```

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
