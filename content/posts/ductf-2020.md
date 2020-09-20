---
title: "Partial Writeup: Down Under CTF 2020"
date: 2020-09-19T09:28:04-04:00
categories:
- writeup
tags:
- ctf
- security
draft: true
---

This past weekend, my friends and I had the chance to participate in Down Under CTF 2020, a CTF aimed at Australian secondary- and tertiary-school students. Though our team didn't come anywhere close to placing, or even completing all the challenges, we had a fun time and learned some new techniques for our next CTF.

This writeup is a team effort by me, Dean Jiao, and Ruju Jambusaria, and was written while we were solving challenges. It is *not* an exhaustive writeup (please consult the organizers' writeup for a full writeup). Instead, this writeup is a collaborative effort in exploring dead ends, throwing ideas on the wall, and seeing what sticks. 

Challenges we made a decent attempt at completing (or successfully solved) are therefore included below; other challenges are not.

# Background

Down Under CTF 2020 ran from Friday, September 18 5AM EDT to Sunday, September 20 5AM EDT (7PM Australian Eastern Time). There were [over 3000 participants](https://twitter.com/DownUnderCTF/status/1307651854134734848), and over 1000 teams on the scoreboard.

Our team was called "why not make team" (... this is a long story), and consisted of me, Dean Jiao, and Ruju Jambusara. If you're curious about how we did overall, you can checkout [our team stats](#team-stats) near the end.

There were seven challenge categories, with difficulties ranging from "sanity check" to "insane".
1. [OSInt](#osint)
2. [Forensics](#forensics)
3. [Web](#web)
4. [Crypto](#crypto)
5. [Misc](#misc)
6. [Pwn](#pwn)
7. [Reversing](#reversing)

# OSInt

## Petstagram
**Difficulty**: Beginner  
*Who is Alexandros the cat exactly? And who is this mysterious "mum" he keeps talking about?
Submit his mum's full name in lowercase and with underscores instead of spaces, as the flag: DUCTF{name}
Author: dahlia*

Given the challenge name, searching on Instagram for Alexandros the cat seemed like a good start. It brought us to this profile:

![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_49e21dc6e6be0e792d73e7a8ea970c56.png)

Dipping into the followers, we then found this person:

![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_d8c165320041d52711756714504a0183.png)

Whom we suspected to be the "mum" referenced. However, entering `DUCTF{emily_waters}` didn't work, and we realised there was a middle name from the email:

```
emilytwaters@gmail.com
```

Sending an email to that email address got us an out-of-office reply:

[PICTURE GOES HERE]

Which gave us the flag in the signature.

**Flag**: DUCTF{emily_theresa_waters}

## Bad Man
**Difficulty**: Easy  
*We have recently received reports about a hacker who goes by the alias und3rm4t3r. He has been threatening innocent people for money and must be stopped. Help us find him and get us the flag.*

Googling `und3m4t3r` or searching for the `und3rm4t3r` user on Discord revealed nothing.

*Post-CTF*: Turns out we were supposed to look for them on *Twitter* instead. 

They had a deleted tweet, which was then visible on the Wayback Machine:
http://web.archive.org/web/20200723112257/https://twitter.com/und3rm4t3r

[PICTURE GOES HERE]

**Flag**: DUCTF{w4y_b4ck_1n_t1m3_w3_g0}
**Tools**: Wayback Machine

##  I think this one is really going to take off
**Difficulty**: Medium  
*You'll never believe it but I swear I saw one of those big American refueling planes flying around right over the boxing croc on the first of September. If only I knew its registration number... I bet I'd be able to find out all kinds of information, like the day it first flew!  
The flag will be the date of the first flight of the plane I saw in this format: DUCTF{DD-MM-YY}

HINT: What kind of plane did they see? Did you know some flight trackers don't track all kinds of planes!"*

Fun fact: we were the 10th team to solve this one!

We started with Googling the "boxing croc", since we all had no idea what it was:

[PICTURE GOES HERE]

Looking at a map, we noticed that Darwin International Airport was nearby, so it was likely that any aircraft going over the Boxing Croc was probably heading into or out of Darwin.

![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_c241a5cfea9f7126987de715ce2c0f11.png)

Next, we looked at a list of American refueling planes, hoping to find a model so we could find flight data for them. This was inconclusive, especially since flight trackers only showed flights from the past 7 days.

We were referring to [this totally not sketchy site](http://www.planeflighttracker.com/2014/04/united-states-military-aircraft-in.html) for finding the model codes for military aircraft

![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_8e12b8c3761920882e49408680e5b668.png)

*also not sketchy*

Then a whole bunch of googling later, still no results. We were trying to look at flights for the Airbus A330 MRTT

We happened upon an article about an [Australian/American Joint Air Force exercise](https://www.pacom.mil/Media/News/News-Article-View/Article/2336270/increasing-interoperability-b-2s-b-1s-join-us-marine-corps-australian-defence-f/) that happened near then, and it mentioned a particular refueling plane, the Boeing KC-135R Stratotanker. Its model was K35R
Still, flight trackers had no historical data going that far back, so we had to get a 7-day free trial for RadarBox. Searching for all flights with the model K35R, we looked for flights around September 1st.

![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_1bc3c3b16122fce965c963234a32f1c7.png)

We eventually found a flight whose path looked like it went over where Boxing Croc would be:

![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_f7d9c48c26d3350edd0bd58301d98526.png)

Googling the plane registration number: `58-0086` leads to 
[https://planefinder.net/data/aircraft/58-0086](https://planefinder.net/data/aircraft/58-0086)
which reveals the flag

**Flag**: DUCTF{16-07-59}

## A turn of events w/ some REALLY bad OPSEC
**Difficulty**: Hard

If u look at the ~~PDF~~ JPG:
- Female
- Born September
- Last location: Netherlands
- MAC address isn't real `f8:ab:05:cf:37:54` but belongs to https://maclookup.app/macaddress/F8AB05
- Burner phone?
- Gelato?
- Might be Owner - Cat's name. Alexandros and their mum is mentioned in Petstagram.
- Plans to leave [Netherlands?] on Sept 18 2020 via [???]

2nd post - video on mum's insta: has beeping in the background: t9 phone keyboard

https://www.dcode.fr/t9-cipher

MEET ME AT 09300 - 9:30 ?

Gelato on the Docks in Melbourne

## Off the Rails
**Difficulty**: Hard
*I'm in trouble, I'm on the run... My friend told me he would meet me underneath a rail bridge 2km from his house before we escape on a train but he forgot to tell me where he lived.

He took two day trips relatively close by and sent me two photos. I also know that late last year, there was a fire near the suburbs approximately 25km from where he lives.

He lives somewhere in the middle of those three points. Could you tell me the name of the road parallel to the bridge, and the latitude and longitude of the bridge?

P.S. The longitude and latitude should be to 3 decimal places (plus if necessary include the . and/or -). Each segement of the flag should be seperated by an underscore. The road name type should be the same as when you find it. The flag is also case insensitive.

Example: DUCTF{latitude_longitude_nameofroad_roadnametype}*

Attached files:

    koala.jpg (sha256: 71633f105b88a45a1ed237f196bdaf8ef706ad2c7eca3571e0519411da8f346d)
    on_track.jpg (sha256: 77acaa70a5b20c5a7dc6f555316492891aa278f995646d6a5cc7750312b0b38c)

this kind of bridge is called a trestle bridge (one way to figure this out is a reverse google images search)

it's quite possible i got the coordinates wrong. but here are the "wrong" ones i've tried:
- noojee
- moncreek
- wairewa
- stony creek

# Web

## Leggos
welp now what
![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_177cd35c4611d71c7e86917514a5e07c.png)

Look in the source via inspector and it's right there, commented out

DUCTF{n0_k37chup_ju57_54uc3_r4w_54uc3_9873984579843}

## Web Badmin

This takes you to a boring-looking site with a link at the bottom saying you can login 

The login screen has only 1 field for the username and after entering one, it leads you here: 

![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_06e6185d80b821304ae9b101fdea355f.png)

Submitting https://google.com gives you a score of 8, apparently 

There is also a link to the playground which leads to: 
![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_0ccc768d2182dd4c355d79329fbe9928.png)
where you can open up an editor from the corner

The editor: 
(it was initially blank but Ruju filled it in with the code from the page source)
![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_4c855fe93ab3436ebeea817eee71976b.png)
However, it seems that this is invalid. The preview won't load either.

## Robotsss
You're redirected to a site about robots. The message says you can view the posts after you make an account.

After registering an account, I'm led to a page with 2 posts.

![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_632a07fb1591181290c5b9760d8ba715.png)

In the second post, the message says the flag is located in: `s3cr3t_p4th/robot_fl4g.txt` but appending that to the URL gives a 404 Not Found error :(

`admin:This-Is-The-Admin-Password-XD!`

# Forensics

## On the spectrum

A WAV file that sounds like white noise with some patterns.
![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_c6fe7af26378f0956ee38a14949322fb.png)

Did some serious noise cancellation in Audacity, it started to look like something.

![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_108c12bc66616c41cb8b166b75edd093.png)

then uploaded the .wav into an online spectrogram analyzer
https://academo.org/demos/spectrum-analyzer/

![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_575633a41fe07f649a8f913ef376b71b.png)

DUCTF{m4by3_n0t_s0_h1dd3n}

## Spot the Difference

341
easy

Author: TheDon

An employee's files have been captured by the first responders. The suspect has been accused of using images to leak confidential infomation, steghide has been authorised to decrypt any images for evidence!

Files: https://storage.googleapis.com/files.duc.tf/uploads/SpotTheDifference/Publish.zip (sha256: be6fd22e658b51124da5a608cc50e5fdc6698772a024cfe4dd9fb393f6ee5227)

There are a bunch of files with directories: `.config`, `badfiles` (which contains a bunch of noisy images), `Desktop`, `Downloads`, `Images`, `Messages`, `Music`, `Videos`
Most of these folders contain memes LOL


## i love scomo

I really do love Scott Morrison! <3 <3 <3

However, some people don't like me because of my secret crush :(. So I have to hide my secrets using steganography. This is my hidden space, where I can dream about being with Scomo and I really appreciate that no one tries to reveal my secret message for him.

Author: ghostccamm

Attached files:

    ilovescomo.jpg (sha256: c1a820c8b17c179b93a1bdf677b01080a27cec2d130e58d50117ef669b4ab9af)

Tried:
- Binwalk (extract files)
- Looking at strings/xxd

so i went back to the "extract files" idea but with steghide this time and it looks like there's a password (thinking "hidden space") has something to do with it but i have tried many scomo related passwords and i cannot

Scott Morrison
Scott_Morrison
scomo
Scomo
ilovescomo
I_love_Scott_Morrison
I love Scomo
I_love_Scomo
sco mo
Sco Mo
Sco mo
sco_mo
Sco_mo
ilovesco_mo
DO NOT WORK :(

ok never mind it might not be file in a file the "terminating" JPG bytes are in the right place at the end

# Crypto

## ROT-i

100
beginner

Author: joseph

ROT13 is boring!

Attached files:

    challenge.txt (sha256: ab443133665f34333aa712ab881b6d99b4b01bdbc8bb77d06ba032f8b1b6d62d)


increasing shift cipher
biggest hint was the IABJO
joyce noticed letter distances and ruju actually wrote out the alphabets bless her
we pointed out the shifting cipher (i.e. the changing "i", since ROT-13 is boring)

Flag: CTF{crypto_is_fun_kjqlptzy}

## BabyRSA

200
easy

Author: joseph

This is just RSA for babies!

Attached files:

    babyrsa.py (sha256: 37676169a895f541454f4a93a943a7718c09bd245233e1fa38eb1f85181e3fe8)
    output.txt (sha256: b1a705ce9efd6403006b00ecccc8225e7e103b704dc276797eafa68d26fba068)


n is 2 random 1024bit prime numbers p,q multiplied together  
it is 618 digits long so i am having some REAL trouble trying to factorize it lmao
https://asecuritysite.com/encryption/factors?n=19574201286059123715221634877085223155972629451020572575626246458715199192950082143183900970133840359007922584516900405154928253156404028820410452946729670930374022025730036806358075325420793866358986719444785030579682635785758091517397518826225327945861556948820837789390500920096562699893770094581497500786817915616026940285194220703907757879335069896978124429681515117633335502362832425521219599726902327020044791308869970455616185847823063474157292399830070541968662959133724209945293515201291844650765335146840662879479678554559446535460674863857818111377905454946004143554616401168150446865964806314366426743287 does not even work :(

phi should be (p-1)(q-1)
what is the sig of (557p - 127q) ?
```
e = 65537 (or 0x10001 in hex format)
s = (557*p - 127*q)**(n-p-q) mod n
c = (bytes_to_long(flag))**e mod n

n = 19574201286059123715221634877085223155972629451020572575626246458715199192950082143183900970133840359007922584516900405154928253156404028820410452946729670930374022025730036806358075325420793866358986719444785030579682635785758091517397518826225327945861556948820837789390500920096562699893770094581497500786817915616026940285194220703907757879335069896978124429681515117633335502362832425521219599726902327020044791308869970455616185847823063474157292399830070541968662959133724209945293515201291844650765335146840662879479678554559446535460674863857818111377905454946004143554616401168150446865964806314366426743287
s = 3737620488571314497417090205346622993399153545806108327860889306394326129600175543006901543011761797780057015381834670602598536525041405700999041351402341132165944655025231947620944792759658373970849932332556577226700342906965939940429619291540238435218958655907376220308160747457826709661045146370045811481759205791264522144828795638865497066922857401596416747229446467493237762035398880278951440472613839314827303657990772981353235597563642315346949041540358444800649606802434227470946957679458305736479634459353072326033223392515898946323827442647800803732869832414039987483103532294736136051838693397106408367097
c = 7000985606009752754441861235720582603834733127613290649448336518379922443691108836896703766316713029530466877153379023499681743990770084864966350162010821232666205770785101148479008355351759336287346355856788865821108805833681682634789677829987433936120195058542722765744907964994170091794684838166789470509159170062184723590372521926736663314174035152108646055156814533872908850156061945944033275433799625360972646646526892622394837096683592886825828549172814967424419459087181683325453243145295797505798955661717556202215878246001989162198550055315405304235478244266317677075034414773911739900576226293775140327580
```


# Misc

## Twitter
**Difficulty**: Sanity check
*Check out our Twitter! Find the post with the flag! You can give us a follow if you like <3*

![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_b3e754fbfd8f17ebee4978561ae2b9a8.png)

The first post from the DUCTF Twitter.

convert text from base64

**Flag**: DUCTF{https://www.youtube.com/watch?v=XfR9iY5y94s}


## Discord
**Difficulty**: Sanity-check
*JOIN OUR DISCORD!*

![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_0b5b8b55a75a75007270922070c3f853.png)

**Flag**: DUCTF{c0n6r475_y0u_h4v3_n0w_j01n3d_0ur_4m4z1n6_d15c0rd}

## homepage
Difficulty: beginner
*Hmm wasnt the homepage logo rainbow before? I wonder how it works...
https://downunderctf.com*

![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_12bb31f827a527e488c1d0d720f7d95d.png)

The dot colors are determined from this string:

```
const lol = "00101000000010000010111101100001101001011101000101101110100001100011111101101010" +
        "1010001111000111101000110010000001000000010010001000011111011101001111101001110111101010" +
        "0110011110010111100011011110001010000010001100110110000101011001110101010001011101001001" +
        "0110000011011110001010110011001011111001110010011101100011110000110111111001000011010101" +
        "0100000000101000111110101000111001111100111000010001000100110";
```

concatted is: "001010000000100000101111011000011010010111010001011011101000011000111111011010101010001111000111101000110010000001000000010010001000011111011101001111101001110111101010011001111001011110001101111000101000001000110011011000010101100111010101000101110100100101100000110111100010101100110010111110011100100111011000111100001101111110010000110101010100000000101000111110101000111001111100111000010001000100110"

so that string doesn't actually split evenly into 8-bit binary but if you add "101" (i.e. lol) to the front it does!

Still gibberish

## Welcome!
**Difficulty**: Beginner
*Welcome to DUCTF!*

This challenge asked us to `ssh` using these credentials:
```
ssh ductf@chal.duc.tf -p 30301
password: ductf
```

Which then led to this beautiful screen.

![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_bbafdfec830fc4c24b557017eb4db27a.png)

The phrase "Welcome to DUCTF!" kept popping up, filling the terminal and overwriting past phrases as well. [^1]

However, if you look closely, there is a flag in some of them (you can spot them because they have the curly braces). It took a couple tries to really see the whole thing, but we got there in the end.

[^1]: Optional: the filled terminal. ![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_1ce6ac896ba2b18b72c8d428fdcf2b5e.png)

**Flag**: DUCTF{w3lc0m3_t0_DUCTF_h4v3_fun!}

## 16 Home Runs
**Difficulty**: Beginner
*How does this string relate to baseball in anyway? What even is baseball? And how does this relate to Cyber Security? ¯(ツ)/¯*

```
RFVDVEZ7MTZfaDBtM19ydW41X20zNG41X3J1bm4xbjZfcDQ1N182NF9iNDUzNX0=
```

With the equals sign at the end, the string is a pretty dead giveaway for Base 64 encryption. Running it through a decryptor we get the flag.

**Flag**: DUCTF{16_h0m3_run5_m34n5_runn1n6_p457_64_b4535}

## in a pickle
**Difficulty**: Easy  
*We managed to intercept communication between und3rm4t3r and his hacker friends. However it is obfuscated using something. We just can't figure out what it is. Maybe you can help us find the flag?*

Looking at the data we get 

```python
import pickle

with open('data', 'r') as f:
    read_data = f.read()
    print(pickle.loads(bytes(read_data, 'utf-8')))
print("done")
```

this spat out:

```
{1: 'D', 2: 'UCTF', 3: '{', 4: 112, 5: 49, 6: 99, 7: 107, 8: 108, 9: 51, 10: 95, 11: 121, 12: 48, 13: 117, 14: 82, 15: 95, 16: 109, 17: 51, 18: 53, 19: 53, 20: 52, 21: 103, 22: 51, 23: '}', 24: "I know that the intelligence agency's are onto me so now i'm using ways to evade them: I am just glad that you know how to use pickle. Anyway the flag is "}
```

which strings together

```
DUCTF{112499910710851951214811784951095153535210351}
```

which is still not the flag, use an ASCII table for reference: http://www.asciitable.com/

**Flag**: DUCTF{p1ckl3_y0uR_m3554g3}
**Further reading**: the stack overflow message that helped me realise lmao https://stackoverflow.com/questions/41005412/how-to-turn-pickle-output-into-an-unreadable-format

##  Tim Tams
273
easy

Author: QUT_WH

When I eat too many Tim Tams, I get rather slow!

WARNING You will want to turn down your audio for this one!

Download: https://storage.googleapis.com/files.duc.tf/uploads/Clive.wav

File Hash (SHA256): 4C1CC12D002956A83E168CA650B776B55AAC36F2131D0DF617BE7D55DBEF93D1

This was actually so bad. Clive.wav is an incomprehensible mess. It's like a stack of multiple different noise frequencies. I tried to mute certain pitches but no new info was gained.

Spectrogram is pretty but it still tells us nothing
![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_71789269baa1d1cc36c12c9f7a910f56.png)

## Addition
*Author: n00bmaster 
Joe is aiming to become the next supreme coder by trying to make his code smaller and smaller. His most recent project is a simple calculator which he reckons is super secure because of the "filters" he has in place. However, he thinks that he knows more than everyone around him. Put Joe in his place and grab the flag.*

https://chal.duc.tf:30302/

calculator

causing an error generates
![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_37435ed3eab9da2748a3382349b0f6ff.png)

BUT if you enter a calculation that results in 0, it gives the following
![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_a1f71c50a3b977df725b2413e38a686d.png)

```
__import__('subprocess').getoutput('ls')
```

gaves us this:

```
__pycache__ main.py prestart.sh templates test.txt
```

Printing out `main.py` via 
```
__import__('subprocess').getoutput('ls')
```
lends itself to:
![](https://cdn.discordapp.com/attachments/747237254782517358/757065947793195078/unknown.png)

Which shows the flag.

**Flag**: DUCTF{3v4L_1s_D4ng3r0u5}
**Further reading**: eval, subprocess

## Koala Habitat
Difficulty: easy
*Author: QUT_WH
What an Aussie Banger!
Flag Format: STRING you end up with after solving challenge --> Spaces seperate the words
NO DUCTF{} required*

morse code with severe distortions and joyce is really not that patient

the song in it is: https://www.youtube.com/watch?v=MLWzPQmd5sc

Listening to the bass-boosted peaky audio was certainly an experience. I noticed that there was morse code sprinkled in amongst the music. The morse was all the same tone, so after putting the sound file into Audacity and reading the frequencies, I could see that a 600Hz tone was very prominent.

Equalized the audio so that only the Morse Code tones were audible, removing everything else.
![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_a870cacf38c55b2b9ac77b6f1d239622.png)

Dean cleaned up the audio so that it was somewhat audible morse

Then it was just a matter of reading morse from this mess.
![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_e74164ce144f138cf10fa1c5a6d8beaa.png)

That took a while

Result:
-.-. .- .-. .- -- . .-.. .-.. --- / -.- --- .- .-.. .- ... / .- .-. . / - .... . / -... . ... - / -.- --- .- .-.. .- ...

FLAG: CARAMELLO KOALAS ARE THE BEST KOALAS

# Pwn
Looks like a bunch of buffer overflow-type except for the pwn or web one

## Shell this!
100
beginner

Author: Faith

Somebody told me that this program is vulnerable to something called remote code execution?

I'm not entirely sure what that is, but could you please figure it out for me?

nc chal.duc.tf 30002

Attached files:

    shellthis.c (sha256: 82c8a27640528e7dc0c907fcad549a3f184524e7da8911e5156b69432a8ee72c)
    shellthis (sha256: af6d30df31f0093cce9a83ae7d414233624aa8cf23e0fd682edae057763ed2e8)


buffer overflow
I got it to hang at 56 char
but it doesn't do anything
i forget how buffer overflows work :(

# Reversing

## formatting
 
100
beginner

Author: h4sh5

Its really easy, I promise

Files: formatting

if you look at the strings there's a `DUCTF{haha its not that easy}` and it is NOT the flag :(

it turns out that this file is an ELF file

executing the file prints: 
`haha its not that easy}`

if you continue looking at the strings, you see a bunch of commands / file names BUT there are words (not commands / file names) interspersed between them: 

`rtstuff.c deregister_tm_clones __do_global_dtors_aux completed.7452 __do_global_dtors_aux_fini_array_entry frame_dummy __frame_dummy_init_array_entry formatting.c __FRAME_END__ __init_array_end _DYNAMIC __init_array_start __GNU_EH_FRAME_HDR _GLOBAL_OFFSET_TABLE_ __libc_csu_fini too _ITM_deregisterTMCloneTable D puts@@GLIBC_2.2.5 _edata brac0 fmt __libc_start_main@@GLIBC_2.2.5 __data_start __gmon_start__ __dso_handle this _IO_stdin_used crap __libc_csu_init easy the __bss_start main what sprintf@@GLIBC_2.2.5 __TMC_END__ brac1 _ITM_registerTMCloneTable flag heck __cxa_finalize@@GLIBC_2.2.5  .symtab .strtab .shstrtab .interp .note.gnu.build-id .note.ABI-tag .gnu.hash .dynsym .dynstr .gnu.version .gnu.version_r .rela.dyn .rela.plt .init .plt.got .text .fini .rodata .eh_frame_hdr .eh_frame .init_array .fini_array .dynamic .got.plt .data .bss .comment`

and we end up with: 
`d1d_You_Just_ltrace_`    
and 
`too D brac0 this crap easy the main what brac1 flag heck`

let's look into ltrace: 
https://en.wikipedia.org/wiki/Ltrace
https://linux.die.net/man/1/ltrace

it looks like ltrace is 
https://www.codementor.io/@packt/reverse-engineering-a-linux-executable-hello-world-rjceryk5d

https://yurichev.org/1564f46f1c207b2dbc84a2bd4a41ed4c/RE4B-EN.pdf


## I'm getting Emotional
*Author: h4sh5*

*Bob received a very important email regarding some very important information. Can you figure out what's inside?*

*Note: the document is based on a real emotet maldoc sample! The malicious bits have been removed, but this is what real world phishing docs look like...*

*Attached file:  important_information__.docm*

Downloading this word file and then opening it causes it to disappear (and I could no longer find it)

Trying to open in word / notepad indicates that hte file has a virus / it can't be opened 

Trying to "Open and Repair" in word also doesn't help

## flag getter
Difficulty: medium
*Author: joseph
An app that gets the flag for you! What more could you possibly want?
Attached files: flag-getter.apk*

On a phone: ![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_3e853179896110ca6724d68d800474ca.png)

Hitting the buttons only gives a response code: 200 

Ruju tried to install abg to get the logs but there were some installation issues so we didn't get to it :(

# Team Stats
![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_62278a5cbb04bc46a9abfd459556b090.png)
![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_ed6e981cec9221a4bbcd521a6cbe8f17.png)[](https://codimd.s3.shivering-isles.com/demo/uploads/upload_2630ecf6aaf94c880cfe2a4a949ed0bf.JPG)
![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_a02f3c1843066ea84debae22b5181f02.png)
![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_f4c06468be99be4b150466ddbd42a30a.png)
![](https://codimd.s3.shivering-isles.com/demo/uploads/upload_7b982084e75f51ab8d88d9325faa375e.JPG)
