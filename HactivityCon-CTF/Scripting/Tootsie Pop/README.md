# Tootsie Pop

Author: [Gallifrey](https://github.com/gallifrey)


# Challenge

```
How many licks does it take to get to the center of a tootsie pop?

Download the file below.
```

# Solution

I tried unzipping manually to see if there is a pattern. Turns out, nope.
There is no pattern, except:
  - The files are of types ```.zip```,```.bz2```,```.gz```,or ```.xz```
  - If it is a zip file, the file inside will have a different name.
    If not, it will have the same name as the parent file.
  - The files may or may not have extensions, so we have to find out what type of file it is.
  
Working off of this, the logic of the script should be something like:

```
>> Use 'file' command to get the type of file
>> Use 'mv' to change extension
>> Based on extension, extract the file
>> Keep track of new file name
>> Repeat
```

Writing a python script for it:

```
import subprocess
from os import listdir
from os.path import isfile, join
from os import system

mypath = './'

files = [f for f in listdir(mypath) if isfile(join(mypath, f))]
print(files)

while True:
	files = list(filter(lambda x: 'ptest.py' != x, [f for f in listdir(mypath) if isfile(join(mypath, f))]))
	cur_file = files[0]
	if '.' in cur_file:
		#clear formatting first
		cmd = "mv ./%s ./%s"%(cur_file,cur_file[:cur_file.index('.')])
		system(cmd)
		cur_file = cur_file[:cur_file.index('.')]
	#Get format
	cmd = "file ./" + cur_file
	proc = subprocess.Popen(cmd, stdout=subprocess.PIPE, shell=True)
	(out, err) = proc.communicate()
	print("FILE:",out.decode())
	if err:
		print("ERROR:", err.decode())
	if 'ASCII' in out.decode():
		break;
	if 'Zip' in out.decode():
		cmd = "mv ./%s ./%s"%(cur_file,cur_file+'.zip')
		system(cmd)
		cmd = "unzip ./%s.zip"%cur_file
		proc = subprocess.Popen(cmd, stdout=subprocess.PIPE, shell=True)
		(out, err) = proc.communicate()
		print(out.decode())
		cmd = "rm ./%s.zip"%cur_file
		system(cmd)
		#STILL GOTTA DELETE THE OLD ZIP FILE
	elif 'XZ' in out.decode():
		cmd = "mv ./%s ./%s"%(cur_file,cur_file+'.xz')
		system(cmd)
		cmd = "unxz ./%s.xz"%cur_file
		proc = subprocess.Popen(cmd, stdout=subprocess.PIPE, shell=True)
		(out, err) = proc.communicate()
		print(out.decode())			
	elif 'bzip' in out.decode():
		cmd = "mv ./%s ./%s"%(cur_file,cur_file+'.bz2')
		system(cmd)
		cmd = "bzip2 -d ./%s.bz2"%cur_file
		proc = subprocess.Popen(cmd, stdout=subprocess.PIPE, shell=True)
		(out, err) = proc.communicate()
		print(out.decode())			
	elif 'gz' in out.decode():
		cmd = "mv ./%s ./%s"%(cur_file,cur_file+'.gz')
		system(cmd)
		cmd = "gunzip ./%s.gz"%cur_file
		proc = subprocess.Popen(cmd, stdout=subprocess.PIPE, shell=True)
		(out, err) = proc.communicate()
		print(out.decode())		

f = open(cur_file,'r')
print(f.read())
		#print ("program output:", out.decode())
		#break

```

The final text document contains the flag

```
flag{the_an...
```
