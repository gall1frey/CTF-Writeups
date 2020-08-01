# Prophecy
Author: [gallifrey](https://github.com/gall1frey)

# Challenge

```
C A N Y O U S E E T H E F U T U R E ?

Connect with:
nc jh2i.com 50012
```

# Solution

On connecting, the server asks you to guess a number. If the number is right, it asks you to guess the next one.
If it is incorrect, it tells you what the number was, and then quits.

```


       ██████  ██████   ██████  ██████  ██   ██ ███████  ██████ ██    ██ 
       ██   ██ ██   ██ ██    ██ ██   ██ ██   ██ ██      ██       ██  ██  
       ██████  ██████  ██    ██ ██████  ███████ █████   ██        ████   
       ██      ██   ██ ██    ██ ██      ██   ██ ██      ██         ██    
       ██      ██   ██  ██████  ██      ██   ██ ███████  ██████    ██    

 
==============================================================================
 
                         I C A N S E E T H E F U T U R E
 
==============================================================================
 
 W H A T I S T H E N E X T N U M B E R T O C O M E F R O M T H E F U T U R E ?
 
> 12345
 
         F A I L U R E . T H E C O R R E C T N U M B E R W A S 99126

```

To get the flag, we need to write a script that guesses a number, if it is wrong, stores the correct number in a list, then starts the instance again to get the next number.

```
import sys
import socket
import time

hostname = "jh2i.com"
port = 50012
nos = [99126, 76106, 32378]
data = ''

def netcat(hn,p,content):

	global nos
	ind = 0

	sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
	sock.connect((hn,p))
	time.sleep(0.5)

	global data

	while 'flag' not in data:
		try:
			data = sock.recv(1024).decode()
			while 'F A I L U R E' not in data:
				if len(nos) == 0 or ind > len(nos)-1:
					print("sending:",content)
					sock.sendall(content)
				else:
					print("Sending:",str(nos[ind]).encode())
					sock.sendall(str(nos[ind]).encode())
					ind = ind + 1
				time.sleep(0.75)
				data = sock.recv(1024).decode()
				print(nos)
				print(data)
				if 'F A I L U R E' in data:
					no = [int(i) for i in data.split() if i.isdigit()][0]
					print(no)
					
					nos.append(no)
					sock.close()
					break

			
			if 'F A I L U R E' in data:
				ind = 0
				sock.connect((hn,p))
				time.sleep(0.75)
			if 'flag' in data:
				break
		except:
			netcat(hn,p,content)

	
content = "1234"

netcat(hostname, port,content.encode())
'''

This script isn't a very good way to go about it, but it gets the job done

On running the script, we get the flag: 

```
flag{does_this_count_as_artificial_intelligence}
```
```
