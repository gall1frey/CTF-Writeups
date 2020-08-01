# Caesar Mirror
Author: [gallifrey](https://github.com/gall1frey)

# Challenge

```
Caesar caesar, on the wall, who is the fairest of them all?

Download the file below.

```

# Solution

This is a fairly straightforward challenge. 

The given file is encoded using the caesar cipher:
We can use a caesar decoder online to decrypt it. 
```
     Oh boy! Wow, this warmup challenge sure   os I !rehtegot tup ot nuf fo tol a saw 
    definitely absolutely always love trying   sgniht evitavonni dna wen pu kniht ot 
       to do with the very basic, common and   fo trap tsrif ehT !seuqinhcet FTC cissalc 
     your flag is flag{julius_ and that is a   gnihtyreve ton si ti tub trats taerg 
 that you will need to solve this challenge.    dna edih ot gniyrt ekil t'nod I 
  separate each part of the flag. The second   od uoy tub _a_ni_ si galf eht fo trap 
   need just a little bit more. What exactly   ekam dna yrt ot ereh edulcni ew dluohs 
     this filler text look more engaging and   ?senilwen dda ew dluohS ?elihwhtrow 
    Should we add spaces and try and make it   hguone si senil ynam woH ?lacirtemmys 
 to make this filler text look believable? A    a nihtiw srettel fo erauqs dilos 
 simple, monospace-font text file looks good   eht ta tsomla ew erA .em ot hguone 
   end? It looks like it! I hope it is good.   }noitcelfer si galf ruoy fo trap driht ehT 
and at this point you should have everything   rof galf siht timbus ot deen uoy taht 
    points. The beginning is marked with the   ,ecarb ylruc gninepo eht dna xiferp galf 
  and it includes English words separated by   ylruc gnisolc a ni dne ot ,serocsrednu 
  brace. Wow! Now THAT is a CTF! Who knew we   siht ot rehpic raseac eht klim dluoc 
            extent?? Someone get that Julius   !ladem a yug raseaC 
```
This decrypts half the file, or so it seems.
The next task is to simply reverse the strings of the other half of the message.

```
     Oh boy! Wow, this warmup challenge sure   was a lot of fun to put together! I so 
    definitely absolutely always love trying   to think up new and innovative things
       to do with the very basic, common and   classic CTF techniques! The first part of 
     your flag is flag{julius_ and that is a   great start but it is not everything
 that you will need to solve this challenge.   I don't like trying to hide and 
  separate each part of the flag. The second   part of the flag is _in_a_ but you do 
   need just a little bit more. What exactly   should we include here to try and make 
     this filler text look more engaging and   worthwhile? Should we add newlines? 
    Should we add spaces and try and make it   symmetrical? How many lines is enough
 to make this filler text look believable? A   solid square of letters within a 
 simple, monospace-font text file looks good   enough to me. Are we almost at the
   end? It looks like it! I hope it is good.   The third part of your flag is reflection} 
and at this point you should have everything   that you need to submit this flag for 
    points. The beginning is marked with the   flag prefix and the opening curly brace, 
  and it includes English words separated by   underscores, to end in a closing curly
  brace. Wow! Now THAT is a CTF! Who knew we   could milk the caesar cipher to this
            extent?? Someone get that Julius   Caesar guy a medal!'
```
Then, we concatenate the flag

```
flag{julius__in_a_reflection} 
```
