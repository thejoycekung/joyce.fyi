---
title: A note on my former blog
date: 2020-08-12T03:16:47+0400
categories:
- misc
- meta
summary: Moving to more technical content.
---

Towards the beginning of 2017, I started a blog using Jekyll on Github Pages to talk about my interviewing processes and the things I was learning. Though that was the original intention, there were always a few things that irked me a little:

- My blog content always skewed a little too personal, in my opinion. Aptly named as "life.log", I talked about everything from my extracurriculars to my personal life to my actual interviews and how I *felt* about all of it, but not really what I learned, at all.
- The fact that my "username" website from Github Pages and my blog were technically two different repos - something I didn't really know how to reconcile.
- My "username" website was always rooted in the idea that it should be a simple one-pager. I'm not a complicated person. So I just wrote it in HTML and CSS. However, as I've started to seek a way to showcase my literary works as well (something quite repetitive in form), I wanted something more flexible and sustainable.

Today I'm moving to Hugo. Some reasons that helped motivate me:

- I wanted to do a write-up for a CTF I recently did, but I had no idea where I wanted to host it. Shockingly, the place I called my blog - I really did not want anyone to actually see it for technical content (see: "too personal" above).
- I found themes (quite easily, actually) to help me remedy the whole "two repo" situation. I'm sure Jekyll could've done it for me too, but too late, we're here now.
- Hugo is fast, and built on Go (which I'm learning on Exercism right now). Jekyll is a little slower, and built on Ruby. With my Jekyll blog, the list of Ruby dependencies I needed was **staggering** (most likely due to my theme choice - added before Github offered the theming option). I didn't even understand half of what they were - I just updated them whenever dependabot came along. That kind of lack of understanding something I, myself, maintained made me feel pretty insecure and uncomfortable. With Hugo, I like the enthusiastic community and I'm excited to dig into (and maybe even contribute to!) their source code.

Edit: Hugo itself has its own problem where the source and `public` folders are designed to be in separate areas (for a monorepo setup), but for now this will do just fine (just shows how I did _not_ thoroughly do my research).

Other methods I considered were Medium (a site that hosted some of my more literary content for a while), Ghost, and just using a Notion page. 

- While Medium is certainly an *easy* option to use, I'm much less comfortable hosting all of my content on it now than 5 years ago. I've changed a lot and so has the Medium revenue model.
- Ghost is still an alluring option, somewhat, but I enjoy how Hugo can integrate with Github Pages easily and I don't have to finagle what's hosting this.
- Finally, Notion pages are so, so, incredibly convenient given that I just use Notion for almost all of my notes ever (since around 2018) but I wanted a bit more flexibility still, and using my notebook as a publishing space veers into the "personal" space once more. Also, configuring domain names and such seems like it would be a pain (I don't even have a custom domain for this page yet).

Some inspirations for choosing the Cactus theme: [Sophie Alpert](https://sophiebits.com/), [Sy Brand](https://blog.tartanllama.xyz/) (and their [poetry site](https://sybrand.ink/)), [Julia Evans](https://jvns.ca/), and [Lucy Zhang](https://kowaretasekai.wordpress.com/).

If you're interested in reading my old blog contents still, they'll still be on [my Github as the "archived_blog" repo](https://github.com/thejoycekung/archived_blog). I'll freeze it as is today.

On that note, just over 3 and a half years after I tried this the first time: hi. My name is Joyce. Welcome to my blog.
