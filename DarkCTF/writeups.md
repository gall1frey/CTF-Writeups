# OSINT
Author: [Gallifrey](https://github.com/gall1frey)

## Find cell
```
I lost my phone while I was travelling back to home, I was able to get back my eNB ID, MCC and MNC could you help me catch the tower it was last found.
note: decimal value upto 1 digit
Flag Format : darkCTF{latitude,longitude}
```
### Solution

Searching for eNB ID on 
https://www.cellmapper.net/map?MCC=310&MNC=410&type=LTE&latitude=32.82059844945921&longitude=-24.577407925948716&zoom=6.337042870587223&showTowers=true&showTowerLabels=true&clusterEnabled=true&tilesEnabled=true&showOrphans=false&showNoFrequencyOnly=false&showFrequencyOnly=false&showBandwidthOnly=false&DateFilterType=None&showHex=false&showVerifiedOnly=false&showUnverifiedOnly=false&showLTECAOnly=false&showENDCOnly=false&showBand=0&showSectorColours=true
fives us the coordinates to the cell tower, 32.82059844945921 and -24.577407925948716

The flag is:
```
darkCTF{32.8,-24.5}
```

## Time Travel
```
Can you find the exact date this pic was taken (It is Australian forest fire)

Flag Format: darkCTF{dd-mm-yyyy}
```
An image file, ```TimeTravel.jpg``` was also given.

![](TimeTravel.jpg)

### Solution

Reverse google searching yields that the image is courtsey of NASA, taken from the ISS. Going to their website, https://worldview.earthdata.nasa.gov/?v=149.0555510075992,-31.596054937400808,156.75441151318898,-27.81679659025584&t=2019-09-15-T22%3A00%3A00Z&l=VIIRS_SNPP_Thermal_Anomalies_375m_Day(hidden),VIIRS_SNPP_Thermal_Anomalies_375m_Night(hidden),Reference_Labels(hidden),Reference_Features(hidden),Coastlines,VIIRS_SNPP_CorrectedReflectance_TrueColor,MODIS_Aqua_CorrectedReflectance_TrueColor(hidden),MODIS_Terra_CorrectedReflectance_TrueColor(hidden)&tr=australia_fires_2019_2020,
we find that the photo was taken on 15th sept, 2019.

The flag is:
```
darkCTF{15-09-2019}
```

# Cryptography

## Pipe Rhyme
```
Chall:- Pipe Rhyme

Chall Desc:- Wow you are so special.

N=0x3b7c97ceb5f01f8d2095578d561cad0f22bf0e9c94eb35a9c41028247a201a6db95f
e=0x10001
ct=0x1B5358AD42B79E0471A9A8C84F5F8B947BA9CB996FA37B044F81E400F883A309B886

```

### Solution

This is a simple RSA challenge. 
On converting N, e and ct to decimal, we get:
```
N = 1763350599372172240188600248087473321738860115540927328389207609428163138985769311
e = 65537
ct = 810005773870709891389047844710609951449521418582816465831855191640857602960242822
```
Using factordb to get the factors p and q, of N:
```
p = 31415926535897932384626433832795028841
q =  56129192858827520816193436882886842322337671
```
Now that we have p, q and e, we can calculate totient and the secret key d as follows:
```
totient = (p-1)*(q-1) = 1763350599372172240188600248087473321682730891266173271675081787918842463868402800
ed≡1modϕ
therefore d = 188047321955721375508157638187334651345661324123156155999468187676652730213105073
```
Then using n and d to decrypt the message, we get:
```
darkCTF{4v0iD_us1ngg_p1_pr1mes}
```

Or, using a simple python script,
```python
from Crypto.Util.number import *
n = 1763350599372172240188600248087473321738860115540927328389207609428163138985769311
ct = 810005773870709891389047844710609951449521418582816465831855191640857602960242822
p = 31415926535897932384626433832795028841
q =  56129192858827520816193436882886842322337671
totient = (p-1)*(q-1)
d = inverse(e,totient)
print(long_to_bytes(pow(ct,d,n)))
```

The flag is:
```
darkCTF{4v0iD_us1ngg_p1_pr1mes}
```

## WEIRD ENCRYPTION
```
I made this weird encryption I hope you can crack it.
```

### File:
```
prefix="Hello. Your flag is DarkCTF{"
suffix="}."
main_string="c an u br ea k th is we ir d en cr yp ti on".split()

clear_text = prefix + flag + suffix
enc_text = ""
for letter in clear_text:
    c1 = ord(letter) / 16
    c2 = ord(letter) % 16
    enc_text += main_string[c1]
    enc_text += main_string[c2]

print enc_text
```
and
```
eawethkthcrthcrthonutiuckirthoniskisuucthththcrthanthisucthirisbruceaeathanisutheneabrkeaeathisenbrctheneacisirkonbristhwebranbrkkonbrisbranthypbrbrkonkirbrciskkoneatibrbrbrbrtheakonbrisbrckoneauisubrbreacthenkoneaypbrbrisyputi
```

### Solution

On close inspection of the code, we know that the encrypted message can be divided into fragments given by the elements of the list ```main_string```.
So we split the encrypted string into said fragments. We get:
```
ea we th k th ...
```
We put that in a list, ```dec_list```, and run the following python code on it to get our flag:
```python
msg = ''
main_string="c an u br ea k th is we ir d en cr yp ti on".split()
dec_list = list() #That list we got
for i in range(0,len(dec_list),2):
  msg += chr(main_string.index(dec_list[i])*16 + main_string.index(dec_list[i+1]))
  
print(msg)
```

output: ```Hello. Your flag is DarkCTF{0k@y_7h15_71m3_Y0u_N33d_70_Br3@k_M3}.```

The flag is:
```
DarkCTF{0k@y_7h15_71m3_Y0u_N33d_70_Br3@k_M3}
```

# Misc

## Minesweeper
```
I'm lucky to be surrounded by even-minded people from all around. Flag is not in the regular format.

Submit flag in darkCTF{flag} format.
```
A file, minesweeper.txt, with a huge matrix was also provided.

### Solution

Got first blood on this one.
On first look at the challenge, I noticed not as many odd numbers in the matrix as there were even. So I figured the flag lay in those numbers that had even numbers on all four sides, right, left, top and bottom. 

The next part was writing a python script to find those values, and it was simple.
The following script got me the reverse flag. 
```python
mat = [[]] #The matrix we got from the file
req_list = [] #store all required nos
for i in range(len(mat)):
    for j in range(1,len(i)-1):
        if mat[i-1][j]%2 == 0 and mat[i+1][j]%2 == 0 and mat[i][j-1]%2 == 0 and mat[i][j+1]%2 == 0:
            req_list.append(chr(mat[i][j]))
print(''.join(req_list)) #Prints rev flag

print(''.join(req_list)[::-1]) #Prints flag
```

The flag is:
```
darkCTF{32.8,-24.5}
```

## Secret Of The Contract
```
Ropsten network contains my dark secret. Help us find it. Name of the contract was 0x6e5EA18371748Db7F12A70037d647cDFCf458e45
```
### Solution

The address is of a contract in the ropsten network of the etherium blockchain. On searching for the address on https://ropsten.etherscan.io/, we get two transactions.
(The third on was done after I had solved the challenge, but the picture was taken afterwards)

![](misc/transactions.png)

On viewing the data of the two transactions, we see a hex code, divided into three parts:

![](misc/t_hex1.png)
![](misc/t_hex2.png)

The message: ``` Hmm-6461726B4354467B337468337233756D5F353730723467335F3772346e3534633731306e7d0 ```

Converting that hex to text, we get the flag.

The flag is:
```
darkCTF{3th3r3um_570r4g3_7r4n54c710n}
```

# Forensics

## Wolfie's Contacts
```
Wolfie is doing some illegal work with his friends find his contacts.
```
A file, wolfie.E01 was also provided.

### Solution

Used [OSF Mount](https://www.osforensics.com/tools/mount-disk-images.html) to mount the ```.E01``` file.
It had the following folders:

![](forensics/folders.png)

Contacts looked promising.
Viewing all contacts    

```python
mat = [[]] #The matrix we got from the file
req_list = [] #store all required nos
for i in range(len(mat)):
    for j in range(1,len(i)-1):
        if mat[i-1][j]%2 == 0 and mat[i+1][j]%2 == 0 and mat[i][j-1]%2 == 0 and mat[i][j+1]%2 == 0:
            req_list.append(chr(mat[i][j]))
print(''.join(req_list)) #Prints rev flag

print(''.join(req_list)[::-1]) #Prints flag
```

The flag is:
```
darkCTF{32.8,-24.5}
```

## Secret Of The Contract
```
Ropsten network contains my dark secret. Help us find it. Name of the contract was 0x6e5EA18371748Db7F12A70037d647cDFCf458e45
```
### Solution

The address is of a contract in the ropsten network of the etherium blockchain. On searching for the address on https://ropsten.etherscan.io/, we get two transactions.
(The third on was done after I had solved the challenge, but the picture was taken afterwards)

![](misc/transactions.png)

On viewing the data of the two transactions, we see a hex code, divided into three parts:

![](misc/t_hex1.png)
![](misc/t_hex2.png)

The message: ``` Hmm-6461726B4354467B337468337233756D5F353730723467335F3772346e3534633731306e7d0 ```

Converting that hex to text, we get the flag.

The flag is:
```
darkCTF{3th3r3um_570r4g3_7r4n54c710n}
```

