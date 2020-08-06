# Web SQLi
Author: [Gallifrey](https://github.com/gall1frey)

# Challenge
View Users

It was a mongodb injection chall, asking us to view all users in the db.

# Solution
use the query ```' || 'a'=='a``` to get the flag

# Challenge
New Login
It was again a mongodb injection chall, asking us to login as admin.
This time I opened uo burpsuite and used proxy to alter the json string sent in the 
request. 
The request was made as follows:
```json
{"username":"admin","password":{"$ne": 1}}
```

# Challenge
NewLogin 2

Had to login as all users.

## other challenges were mySQL injection, that were done using sqlmap.
