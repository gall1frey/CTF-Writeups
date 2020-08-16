# Really Secure Algo
Author: [Gallifrey](https://github.com/gall1frey)

## Challenge

```
n = 977777777777777777777777777777777777777777777777777777777777777777777777
777777777777777777777777777777777777777777777777777777777777777777777
e = 65537
c = 777100860344719251862233099079929815950590358671816920785821983126
256200182002548130383416252558510764139287733939898022176140368825482344800

Downloads

encrypted.txt
```

## Solution

Aside from the obvious n,e and c given, the name Really Secure Algo (RSA) was a hint at the kindof encryption used.
```n``` in RSA is the product of two large primes, ```p``` and ```q```.
The security of the algorithm depends on having a large enough value for p and q so factorization is a very 
time-expensive process.
The obvious mistake is that ```n``` is small.

We can lookup the factors of ```n``` on factordb.
This gives us:
```
n = 977777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777
e = 65537
c = 777100860344719251862233099079929815950590358671816920785821983126256200182002548130383416252558510764139287733939898022176140368825482344800
p = 2851
q = 342959585330683191083050781402237031840679683541837172142328227912233524299466074281928368213882068669862426439066214583576912584278420827
phi (totient, equals the product of (p-1) and (q-1): 977434818192447094586694726996375540745937098094235940605635449549865544253478311703495849409563895709107915351338711563194200865193499354100
```
and we can now compute ```d```:
```
d = e invmod(phi)
d: 580701009828663504201100842124767353624422949652824672832461386143301720113118262118914725919873961934934858051478158497101322991396830658273
```
Now, we can compute plaintext m from ciphertext c using this python script:
```python
n = 977777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777777
e = 65537
c = 777100860344719251862233099079929815950590358671816920785821983126256200182002548130383416252558510764139287733939898022176140368825482344800
p = 2851
q = 342959585330683191083050781402237031840679683541837172142328227912233524299466074281928368213882068669862426439066214583576912584278420827
r = 977434818192447094586694726996375540745937098094235940605635449549865544253478311703495849409563895709107915351338711563194200865193499354100
d = 580701009828663504201100842124767353624422949652824672832461386143301720113118262118914725919873961934934858051478158497101322991396830658273

from Crypto.Util.number import long_to_bytes

#print(n == (p*q))
#print((e*d)%r == 1)

m = pow(c,d,n)
print(m)
print(long_to_bytes(m))
```
The flag is:
```
flag{alright_you_have_taken_too_much_time}
```

# Really Find Fish

## Challenge

```
Yeah! I finally got fish from a river (all text is in single line has been broken to accomdate clearly in the card)

xxkxxxxkxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxcdddcxxxxxxxxxxxxxxxxxc
dddddddcddddddddddddddddddddddddddddddddddddddddcxxxxxxxxxxxxxxxxxc
ddddddddddddddcxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
xcddddddddddddddddddddddddddddddddddddcxxxxxxxxxxxxxxcxxxxxxxcddddd
ddddcdddddddddddddddddddddddddddddddddddddddddddddddddddcxxxxxxxxxx
xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxcddddddddddddddd
dddddddddddddddddddddddddddddddddddddddddddcxxxxxxxxxxxxxxxxxxxxxxx
xxxxxxxxxxxxxxxxxxxxxcdddddddddddcddddddddddddddddddddddddddddddddd
dddcxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxcddddddddddddddd
ddddddddddddcddddddddddddddddcxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
xxxxxxxxxxxxxxxxxxxxxxxxxcdddddddcddddddddddddddddddddddddddddddddd
ddddddddddddddddddddddcxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx
xxxxxxxxxxxxxxxxxxcdddddcxxxxxxxxxxxxcddddddddddddddddddddddcxxxxxx
xxxxxxxxxxxcddddddddddddddcxxxxxxxxxxxxxxxxxxxxxxxc
```

## Solution

Right off the bat I knew this was an esolang. I didn't know which, but a little googling got me there.
Then I used decode.fr 's deadfish decoder to get the flag.

The flag is:
```
darkCTF{Welc0m3_T0_D4rk4rmyctf}
```

# Gene Encryption

## Challenge

```
Something related to genes ?
Flag is underscore separated with braces around it 
enc
```

## Solution

I was stuck for a while here, but johnhammond's [ctf-katana](https://github.com/JohnHammond/ctf-katana) helped me out.
I then wrote a code for it in python, but later I found that the whole thing was available easily on decode.fr.

```python
#given string
dna = '00001100000010001000010110110001111010111110111000100000110110101110010010001000010000001000110100111010100000000100011100100000010000000110010100000100010000001110110010001010010000111110001100110110101110111'

gene = list()

#split it into chunks of len 2
chunks, chunk_size = len(dna), 2
dna = [ dna[i:i+chunk_size] for i in range(0, chunks, chunk_size) ]
dna[-1] = '10'

#get genes from dna
for i in dna:
	if i == '00':
		gene.append('A')
	elif i == '10':
		gene.append('C')
	elif i == '01':
		gene.append('G')
	else:
		gene.append('T')

gene = ''.join(gene)

chunks, chunk_size = len(gene), 3
gene = [ gene[i:i+chunk_size] for i in range(0, chunks, chunk_size) ]

data = list()

#genes to alphabet
dn = list()
alf = list()

for i in 'ACGT':
	for j in 'ACGT':
		for k in 'ACGT':
			dn.append(i+j+k)

#print(dn)

alf = list(chr(i) for i in range(ord('a'),ord('z')+1))
alf.extend(list(chr(i) for i in range(ord('A'),ord('Z')+1)))
alf.extend(list(chr(i) for i in range(ord('1'),ord('9')+1)))
alf.extend(['0',' ','.'])

dic = dict(zip(dn,alf))

#print(dic)

#print(gene)

for i in gene:
	print(dic[i],end='')
```

```darkCTFDeoxyribonucleicAcidCryptoxD```

The flag is:
```
darkCTF{Deoxyribonucleic_Acid_Crypto_xD}
```

# No More Remorse

## Challenge

```
Sometimes, what you see is not what you get!! nomoreremorse
```

## Solution

I spent a lot of time on this trying to figure it out, and it was a whole trip!
Notice the morse code doesn't have delimiters like traditional morse, and that the string length is a multiple of five.

That points to a baudot code. So now we split the morse code into chunks of five characters.
```
.-.-- .---- .---- .-..- --.-. ..-.. -...- ...-. ...-. ..... .---- --..- -..-- .-.-. --... -.-.- 
.--.- ..-.. ...-- ..... -...- ..--- ...-- ..-.. ...-. ..... --.-. .-.-- ..--- --... .---- ----. 
--... -.-.- ----. -.... ---.. ---.. ..-.- ---.. --..- -..-- 
```
Then we replace dashes with 0 and dots with 1.
```
10100 10000 10000 10110 00101 11011 01110 11101 11101 11111 10000 00110 01100 10101 00111 01010 
10010 11011 11100 11111 01110 11000 11100 11011 11101 11111 00101 10100 11000 00111 10000 00001 
00111 01010 00001 01111 00011 00011 11010 00011 00110 01100 
```
And put it through the baudot decoder on decode.fr
```
	HTTPS://TINYURL.COM/SHOUTEUREKAAGAIN
```
we get a pastebin link, which we follow to get a weirdly formatted text.

hｔtpｓ：//іmgur．сοｍ/a／d2ｈhｄHlνｄＸNｚWlｚd2hｈｄHｌvｄＷdldC5５b３ｖqdΧＮ0bmVlZΗRvϹ２VｌｂGｌrＺΧＲoҮＸＲrZWVwdH

The link doesn't lead anywhere, and there are some sort of misleading messages in the base64 string at the end of the link.
But the interesting part is the formatting.
It is homoglyph steganography. On decoding it from https://twsteg.devsec.fr/ , we get another link:

```shorturl.at/bemz1```

On visiting the link, there is a photo, with the flag in it.

![](noremorse.jpg)

The flag is:
```
darkCTF{s0_m4ny_lo0k_alik35}
```
