# Cryptography challenges

## substitution0

I just used dcode.fr 's mono-alphabetic substitution decryptor.

flag: `picoCTF{5UB5717U710N_3V0LU710N_357BF9FF}`

## substitution1

Did the same thing as the previous challenge, however this time the flag didn't submit. This confused me for a bit and I noticed in the flag there should be a 'Q' instead of an 'X', I made the correction and submitted the flag.

flag: `picoCTF{FR3QU3NCY_4774CK5_4R3_C001_7AA384BC}`

## substitution2

This time I had the 'spaces can be ignored or are missing' option on dcode.fr 's mono-alphabetic substitution cipher.

So I used it.

flag: `picoCTF{N6R4M_4N41Y515_15_73D10U5_8E1BF808}`

## la cifra de

Put the entire cipher text in dcode.fr's identifier, got vignere to be the most likely, put on auto decrypt

got flag for the key: `GFLAGFLA`

flag: `picoCTF{b311a50_0r_v1gn3r3_c1ph3ra966878a}`

I am pretty sure we are supposed to make our own scripts for this or something but I am honestly too lazy to do that.

## waves over lambda

Opened it. Looked like monoalphabetic substitution, went over to dcode.fr's tool, decoded it.

Very easy.

flag: `frequency_is_c_over_lambda_agflcgtyue` (flag not in usual format)

## c3

first we look at the given script:-

```
import sys
chars = ""
from fileinput import input
for line in input():
  chars += line

lookup1 = "\n \"#()*+/1:=[]abcdefghijklmnopqrstuvwxyz"
lookup2 = "ABCDEFGHIJKLMNOPQRSTabcdefghijklmnopqrst"

out = ""

prev = 0
for char in chars:
  cur = lookup1.index(char)
  out += lookup2[(cur - prev) % 40]
  prev = cur

sys.stdout.write(out)
```

For every character in the given input, the algorithm looks up the index of that character in `lookup1` (saves the value to `cur`) and then takes that index and operates on it using: `(cur-prev)%40` to get a new index value which is used to get a new character from `lookup2` and the `prev` value is set to `cur` for the succeeding character

We know that the value of `prev` for the first character is 0 and the `cur` value lies between 0-39 (since both `lookup1` and `lookup2` have len = 40) so `(cur - prev) %40` will always yield values between 0-39 (even in the case when `cur - prev < 0`), since `prev = 0` for the first character and `cur` lies in 0-39 the index won't change when operated on and hence we can decrypt the first character just by taking it's index in `lookup2` and finding `lookup1[index]`, then we set `prev = index` and begin the decryption process for subsequent characters.

For the next character and onwards we know the value of `prev` and we can also find out the value of `(cur - prev)%40` simply by looking up the encrypted character in `lookup2` now we've something like this:-

```
lhs = (X - prev) % 40
```

Now although `X` can have multiple solutions, we only need that solution which lies in 0-39 which will be:-

```
lhs + 40p = X - prev; 	p is some integer
lhs + prev = X + 40q;	q is some integer	

(lhs + prev) % 40 = X 
```

Using modular arithmetic, we can find X which is `cur` value for the encrypted character and now we can just lookup `lookup1` using the `cur` value to find the decrypted character and set `prev = cur` for the succeeding character.

On this logic I wrote this script:-

```
lookup1 = "\n \"#()*+/1:=[]abcdefghijklmnopqrstuvwxyz"
lookup2 = "ABCDEFGHIJKLMNOPQRSTabcdefghijklmnopqrst"

def find_cur(lhs, prev):
	return (lhs + prev) % 40


ciphertext = list("DLSeGAGDgBNJDQJDCFSFnRBIDjgHoDFCFtHDgJpiHtGDmMAQFnRBJKkBAsTMrsPSDDnEFCFtIbEDtDCIbFCFtHTJDKerFldbFObFCFtLBFkBAAAPFnRBJGEkerFlcPgKkImHnIlATJDKbTbFOkdNnsgbnJRMFnRBNAFkBAAAbrcbTKAkOgFpOgFpOpkBAAAAAAAiClFGIPFnRBaKliCgClFGtIBAAAAAAAOgGEkImHnIl")

decrypted =  ""
prev = 0

for char in ciphertext:
	
	lookup2_index = lookup2.index(char)
	cur = find_cur(lookup2_index, prev)
	decrypted += lookup1[cur]
	prev = cur


print(decrypted)

```


On running this I got:-

```
#asciiorder
#fortychars
#selfinput
#pythontwo

chars = ""
from fileinput import input
for line in input():
    chars += line
b = 1 / 1

for i in range(len(chars)):
    if i == b * b * b:
        print chars[i] #prints
        b += 1 / 1


```

judging by "#selfinput" and "#pythontwo" I realised this was a python2 script and asked for itself as input

so on running `$ python2 c3_2.py < c3_2.py`

I got:-

```
a
d
l
i
b
s
```

flag: `picoCTF{adlibs}`

## pixelated

fucking annoying. someone explain this to me.

