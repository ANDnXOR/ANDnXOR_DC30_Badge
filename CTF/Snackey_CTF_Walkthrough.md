# AND!XOR DC30 Snackey CTF Walkthrough
August 12-14, 2022
@kur3us

The Snackey CTF was a contest to primarily get dispense codes for the Snackey vending machine.
Snackey would dispense various items, including candy, deoderant, and AND!XOR badges! (DC29 and DC30)
The CTF started with 6 challenges and on Saturday, we added the MUD challenge from @Evil_Mog.
This walkthrough only covers the original 6 challenges.

## Challenges

### You Found a Vuln! - Web Security
You found a vulnerability on this server. Be a good citizen and send an email to the proper address to report it.
##### Solution
You need to report a vulnerability. Some sites contain a "security.txt" file for this purpose. 
[RFC9116](https://www.rfc-editor.org/rfc/rfc9116) describes the format, purpose, and location of "security.txt".
Browsing to this URL: http://192.168.1.207/.well-known/security.txt would give the following response:
```
Contact: mailto:5n4ck3y@gmail.com
Expires: 2022-08-14T22:00:00.000Z
```
Sending an email to Snackey would result in the following reply email:
```
I'm out of the office at Hacker Summer Camp right now, but thanks for reporting whatever it is you reported. I'm sure everything will be fine until I get back.

As a thank you, here is your flag: "s3cur1ty4th3w1n!"


-- 
Snackey, BS, AA
Lean Six Sigma Green Belt (ICGB)
CISSP, CEH, A+, Sec+, PMP, CISA, CISM, GSEC, CCSP, CCNA, MCSE, CND, eJPT, AWS CP, CCT, Net+, GPEN, Linux+, CMMC, CMMI Level IV, WNS-TL, YKtr, ASDI, AFC, WITO, YWGTf, AOG, IJWTY, Hif, GMYU, NGg, Yu, NGLYd, NGRAa, DY, NGMyc, NGSgb, NGTal, AHU
```

Note: There is an Easter Egg somewhere in the email signature. Did you find it?

### Social Ciphers - OSINT/Crypto
Snackey has posted a cipher challenge on social media. Use OSINT to find it, then solve it and enter the flag here.
##### Solution
It shouldn't be too difficult to find Snackey's Twitter page. This can be easily found by looking at tweets from @andnxor, @kur3us, and others.
https://twitter.com/5n4ck3y

A tweet on August 12 has this text:
`Pczgutmuxnhuoql. Rog uohe xgvohrfqd d yeas scd tkx Lnmpyqy FMY. Ezgsd tkx dek gc fhll vibusd tkxke.`

Go to https://www.boxentriq.com/code-breaking/cipher-identifier
Paste in the ciphertext and click `Analyze Text`.
The analysis says it is likely a Vigenere Cipher. Click the Vigenere Cipher link and then click the link for Vigenere Cipher Tool.
Paste in the ciphertext and click `Auto Solve (without key)`
The first result will say this:
````
37731	nomadttam	congratulations you have uncovered a flag for the snackey ctf enter the key to this cipher there
````
The key is the 2nd column: `nomadttam`   (matt damon backwards)
Enter that for the flag.

### Snackey says, "Reverse me!" - Reversing
Can you find the password for this binary?
##### Solution
Download and run the binary in a linux environment (virus check it if you don't trust us).
It says, `Enter the password:` Unless you are incredibly lucky, you will enter the wrong password.
If you run `strings`, it won't help.
You can run Ghidra or a similar tool, and that will probably work, but it isn't necessary.
`ltrace` will work.
```
$ ltrace ./rev2
__libc_start_main(0x40065d, 1, 0x7fff6d4c2548, 0x400810 <unfinished ...>
printf("\n\nEnter the password: "

)                                                                                 = 22
fgets(Enter the password: iDon'tKnow
"iDon'tKnow\n", 20, 0x7ff2aea169a0)                                                                          = 0x7fff6d4c2410
strcspn("iDon'tKnow\n", "\r\n")                                                                                    = 10
strncmp("iDon'tKnow", "$n@ck3y__R3v3r5@l!", 20)                                                                    = 69
puts("Wrong! Try again!"Wrong! Try again!
)                                                                                          = 18
+++ exited (status 0) +++
```
Now we see what the program was comparing to the string the user entered....we have the password! Try again!
```
$ ./rev2 


Enter the password: $n@ck3y__R3v3r5@l!
Correct! The flag is: $n@ck3y__R3v3r5@l!
```

### Catch-22 - Hacking
In the ben_characters.py file on the AND!XOR watch, what is the name of the last class in the file?
##### Solution
You don't have a watch, so you're playing this CTF to try to get one. But the CTF is asking you to hack the watch! Catch-22!
The point of this challenge is to find someone who has a watch and work together with them to hack it and download a file.
If you read the watch RTFM at: https://mattdamon.app/rtfm/ 
you will see that you can download files using ampy:
```sh
ampy -p /dev/ttyACM0 get ben_characters.py > ben_characters.py
```
The last class in that file is: `Trader`

### What's the Password?? - Hash Cracking
My friend John will rock you, but he's got some rules he abides by. His first rule is you gotta toggle the switch a couple times when he's playing. Secrets of length x is his second rule...where x is double the number of columns in the Snackey keypad.

`$1$ScvKOEQi$NTG34AMIZ0vrpkcyXmyjP1`
##### Solution
As the challenge text hints at, use John the Ripper to crack this password. Use the Rockyou wordlist and the ShiftToggle ruleset to toggle case of letters in the words.
Snackey's keypad has 3 columns, so the password length will be 6.
Put the hash in a file (I used pw.txt) and use this command (wordlist location may be different if not using kali):
```sh
john /tmp/pw.txt --wordlist=/usr/share/wordlists/rockyou.txt --rules=ShiftToggle --min-length=6 --max-length=6
```
The password should be cracked within a couple minutes, resulting in: `SnAx07`

### Modulation - ??
D' main part of this challenge is to use this modulus:

````
n=B54652C5C15E1A23CC836DC4E44319DCFFB0561109E7BB205E08EF6D28565CD8435F3AB5D80D5A109B35A9C7E8FC0CD56F8D4003659F6874B77F872162088790ACBEC9BC53BBB31B042D1C62BDF70995CF06CA662D868EEE4A4223D61F7A41BB85D54A4DD0E182DF832CBDAEDFD99C822C92C3FFF966A1F73B887AE8AB673C07949C02F898DFFB3B96A5881C7F33D7AA4DFE422A3094156C496538E2AA3383215789D67A9E2F291892CACFB06D29D9259F02872ADC42241FDC272903BFECA50A01B1C3E5285B89E32A3C60FEDF183C783175F6EA9305D6DAA37459E23C5D0440466B5DA40E94BB0CB817FCCEAF91B6EE37357E7A386341216A01EB914CCA9BBD
````
to find this file: `nN47dd8dE15BWOMXnDbV.txt`

When you find it, you'll have the flag.

You'll need to use yer censys to get this one.
##### Solution
This modulus is 2048 bits, and most likely from an X509 certificate. If we find the right certificate, it will tell us the domain name of the site we're looking for.
The challenge hints at using censys.... censys.io. This site lets you search for things like certificates. All trusted certs are in a certificate transparency log. Censys reads these and lets you search. BUT the searching is a bit tricky.

If you go to https://search.censys.io/ and click the drop down to search certificates, you'll see a link for `Data Definitions`.
On that page, search for `modulus`. You'll find this entry: 
`parsed.subject_key_info.rsa_public_key.modulus`

Ok. Now just click `search` with nothing in the search field. It will return a list of certificates. Click on any one of them. Then go down to the modulus and click the drop down arrow to see the entire value. Take note of that value and then click on `Raw Data->Table` at the top right.
Find `parsed.subject_key_info.rsa_public_key.modulus`. Notice the value is Base64!! 
Let's decode it:
```sh
echo "<base64 encoded data>" | base64 -d | xxd -p
```
This will show the decoded binary data in hex bytes. Now compare this to the modulus value you saw above in the certificate you selected. It should match!

So we need to take our modulus, create a binary file from it, then base64 encode it, and finally search for that in censys.io.

Create a text file containing only the hex string of the entire modulus beginning with `B54652`....
Convert to binary:
```sh
xxd -r -p file.txt > file.bin
```
and base64 encode it:
```sh
base64 file.bin
```
Do a censys search on `parsed.subject_key_info.rsa_public_key.modulus="<base64 text from above>"` (you'll need to remove the carriage returns from the base64 output.)

The search will return a couple results, both having: `CN=ctf.mattdamon.app`. So go to that domain and add the filename from the challenge:

https://ctf.mattdamon.app/nN47dd8dE15BWOMXnDbV.txt

```
Congratulations!

You found the flag: Sn@ck3yLuvsX509
```
