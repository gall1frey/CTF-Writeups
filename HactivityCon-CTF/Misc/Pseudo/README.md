# Pseudo
Author: [gallifrey](https://github.com/gall1frey)

# Challenge

```
Someone here has special powers... but who? And how!?

Connect here:
ssh -p 50014 user@jh2i.com # password is 'userpass'
```

# Solution

The user we're logged in as has no root priviliges. I figured, getting the flag meant getting root first.
So, that's where I started.
I went to ```/etc/``` and searched for sudoers, to find what users had sudo access.
There, I found the sudoers.d directory, which had a ```README```

Reading the readme:
```
user@02181cc2408b:/etc/sudoers.d$ cat README
#
# As of Debian version 1.7.2p1-1, the default /etc/sudoers file created on
# installation of the package now includes the directive:
#
#       #includedir /etc/sudoers.d
#
# This will cause sudo to read and parse any files in the /etc/sudoers.d
# directory that do not end in '~' or contain a '.' character.
#
# Note that there must be at least one file in the sudoers.d directory (this
# one will do), and all files in this directory should be mode 0440.
#
# Note also, that because sudoers contents can vary widely, no attempt is
# made to add this directive to existing sudoers files on upgrade.  Feel free
# to add the above directive to the end of your /etc/sudoers file to enable
# this functionality for existing installations if you wish!
#
# Finally, please note that using the visudo command is the recommended way
# to update sudoers content, since it protects against many failure modes.
# See the man page for visudo for more information.
#
#
# The credentials for the 'todd' account is 'needle_in_a_haystack'
todd ALL = NOPASSWD: ALL
```

Now we have another username and password.
Logging in to todd's account using ```ssh -p 50014 todd@jh2i.com``` and password ```needle_in_a_haystack```

Here, i can ```sudo```
sudo-ing using ```sudo -i```

```
root@ba8c39594f69:~# ls                                                                                     
flag.txt                                                                                                    
root@ba8c39594f69:~# cat flag.txt                                                                           
flag{hmmm_that_could_be_a_sneaky_backdoor}
```

And the flag is:

```
flag{hmmm_that_could_be_a_sneaky_backdoor}
```
