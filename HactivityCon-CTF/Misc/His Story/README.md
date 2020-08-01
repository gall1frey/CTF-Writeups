# His Story
Author: [gallifrey](https://github.com/gall1frey)

# Challenge

```
Will you hear his story? The answers are all in the past!

Connect here:
nc jh2i.com 50001
```

# Solution

On connecting to the server, we are logged in to user@his_story.
I use the ls command and find that there is a ```flag.txt``` file. Cool.
```
user@his_story:/home/user$ ls
ls
flag.txt
```
But, I try ```cat```, ```head```, ```tail```, and a bunch of other stuff, but nope. Nothing works.
```
user@his_story:/home/user$ cat flag.txt
cat flag.txt
command not found
```
I try the ```compgen -c``` command to see what commands I can use
And these are (out of many others), the only relevant commands I could use to print out the
contents of flag.txt
```
compgen -c
...
printf
push
readarray
...
ls

```
It took some googling, but finally got the flag using the following commands:
```
readarray -t a < flag.txt 
```
This stored the contents of flag.txt into the variable a
```
printf $a
```
This printed out a

Andthe flag is:
```
flag{history_is_written_by_the_victor}
```
