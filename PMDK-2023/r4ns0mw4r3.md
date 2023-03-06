# ΠΜΔΚ 2023 - R4NS0MW4R3

Με μια ματιά στο `zencrypt.py`, βλέπουμε πως χρησιμοποιείται ο αλγόριθμος AES-128. Επειδή ο ransomware developer είναι -απ' ότι φαίνεται- πολύ καλός άνθρωπος, σε κάθε αρχείο που κρυπτογραφεί μας δίνει και 1B του κλειδιού, στην αρχή, πριν μας δώσει το κρυπτογραφημένο αρχείο. Αφού μας δίνονται 14 συνολικά αρχεία, και το κλειδί που ψάχνουμε είναι 16B, μένει να κάνουμε bruteforce τα τελευταία 2 byte. Έγραψα το ακόλουθο python script.

```py
import os
from Crypto.Cipher import AES
from Crypto.Util.Padding import unpad

files = sorted(os.listdir(os.curdir + '/'))
ciphertexts = []
key = ""
for name in files:
	if name.endswith('.enc'):
		with open(name, 'rb') as f:
			key += f.read(1)
			ciphertexts.append(f.read())

flags = []
for n in range(2**16):
  for i, c in enumerate(ciphertexts):
    decrypted = cipher.decrypt(c)
    if b"FLAG{" in decrypted:
      print(f"Key found: {key}")
      flags.append([unpad(decrypted, 16), c[1]])
      exit(0)
```

Τρέχοντας το script, λαμβάνουμε το κλειδί `b"\x8d\x10\xd4\x90\xb2\x9bx'|\x83\xe8\x99\xc3\x1d\x9b\x0f"`. Μάλιστα, αυτό είναι δυνατό αφού το script εντοπίζει το `FLAG{` στο `fake_flag!1!!!1.txt`, το οποίο περιέχει το κείμενο `FLAG{Th1s_1s_f4k3}`. Δυστυχώς αυτό δεν είναι το πραγματικό flag, αλλά, για καλή μας τύχη, όλα τα αρχεία κρυπτογραφήθηκαν με το ίδιο κλειδί, το οποίο πλέον κατέχουμε. Τώρα ζήτημα είναι να αποκρυπτογραφήσουμε τα αρχεία ένα-ένα (έγραψα ένα πρόχειρο python script, δεν έχει νόημα να το συμπεριλάβω) και τελικά βρίσκουμε το flag στο `flag.png`.

~ synthels
