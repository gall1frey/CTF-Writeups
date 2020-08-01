# Cat Cage
Author: [gallifrey](https://github.com/gall1frey)

# Challenge

```
We are in the cat cage! Only the good cats get treats!

Connect here:
nc jh2i.com 50000
```

# Solution

After connecting, I used the ls command

```
user@cat_cage:/home/user$ ls
ls
get_flag
```
There is a get_flag file. 
I used ```cat get_flag```, but it didn't yield anything.
```
cat get_flag 
#!/bin/sh

echo "Oh I am sorry, only cats can get the flag!"
exit 0
```
But the size of the file and the content wasn't matching up.
```
ls -al
total 24
dr-xr-xr-x 1 nobody nogroup 4096 Jul 25 02:17 .
drwxr-xr-x 1 user   user    4096 Jul 25 02:17 ..
-rw-r--r-- 1 nobody nogroup  220 Jul 25 02:17 .bash_logout
-rw-r--r-- 1 user   user    3821 Jul 25 01:57 .bashrc
-rw-r--r-- 1 nobody nogroup  807 Jul 25 02:17 .profile
-rwxr-xr-x 1 user   user     177 Jul 25 01:57 get_flag
```
So, finally, I used ```cat -A get_flag``` to get the flag
```
cat -A get_flag 
#!/bin/sh$
$
echo "Oh I am sorry, only cats can get the flag!"$
exit 0$
^[[2Aecho "flag{thats_a_good_trick_heres_some_catnip}"$
^[[1Aecho "Oh I am sorry, only cats can get the flag!"$
$
```
