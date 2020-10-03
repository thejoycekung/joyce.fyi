+++
title = "Custom Domains on Github Pages Are Hard (i)"
date = 2020-10-02
categories = ["meta"]
tags = ["it's always dns"]
+++

I've been using my [Hugo setup](/posts/a-note-old-blog/) for just over a month now (and really enjoying it!), but I've made a few changes to this blog that I think are going to make things even better:

1. I have a custom domain now
2. I'm using Netlify for hosting, instead of Github Pages
3. I'm taking advantage of Netlify's Deploy Previews

All of these changes came with _some_ level of consideration, so I want to take some time to walk through my thought process here. (Just in case anyone else is thinking about it!)

In this first post, I'll be tackling my troubles with custom domains and hosting.  
In the [next post](/posts/deploy-custom), I'll be talking about why I moved to Netlify and using Deploy Previews.

# Getting a Custom Domain

A few weeks ago, among the flurry of excitement that was [Down Under CTF](/posts/ductf-2020), I bought the domain `joyce.fyi` on a whim.

I say "on a whim", but buying a domain was a huge step for me! I've been using Github Pages since 2017, and I've always, always just used the `thejoycekung.github.io` URL. 

There are a lot of problems with it[^problems], but it's _free_. And I'd never bought a domain before (even for a project), so the thought of venturing out into the domain-name buying world was a little off-putting. So I just kept using the default one.

[^problems]:  Mostly it's super long. It's a pain to type out, and to dictate to another person. It's also distinctly not _mine_ - even though it has my name and all, it's still Github's at the end of the day.

When I restarted blogging this year, I realised that linking to posts was creating some really long URLs. And this wasn't going to go away: I was stuck with long URLs as long as I was still using Github's free domain.

So I started looking for domains. I wanted to scope out domains that were related to my name somehow (`joy.ce` or `joyceku.ng`, anyone?) but when that didn't pan out, I started looking at the list of possible TLDs and considering which ones I would actually want to use.[^urls] Amazingly, I found `joyce.fyi`, for sale on Google Domains for $27/year.[^pricing] And after spying on it for a week (hoping desperately no one would snatch it from me), I finally caved in and bought it.

[^urls]: Candidates (that didn't make the cut 😢) included `.author`, `.bio`, `.info`, `.here`, and of course `.ooo`.

[^pricing]: I don't actually know what the pricing scheme for URLs is. $27/year is like, just over $2/month? That's cheap, right?

Afterwards, all I had to do was set my brand-new domain up with Github Pages. Easy, right? *_Right?!?_*

# Easy Domain Setup with Github Pages
Man, I wish.

To start, I tried using [Github's own documentation](https://docs.github.com/en/free-pro-team@latest/github/working-with-github-pages/managing-a-custom-domain-for-your-github-pages-site) for setting up a custom domain (in my case, an apex domain[^apex]).

[^apex]: An apex domain is a domain without the stuff before the first period. For example, for the domain name `www.google.com`, `google.com` is the apex domain.

... And got to about step 3 before I got stuck.[^stuck] For some unknown reason (to me, at least), Github was giving me an error everytime I tried to save my `CNAME` - it kept giving me the error `CNAME already taken`.

[^stuck]: This is the part where you actually have to do stuff. Uh oh.

As someone who has never bought a domain, this was a little horrifying to read. What do you mean my freshly minted domain name is already taken? Who took it? What is going on? How do I get it back?

It really didn't help that Github had this disclaimer on their docs:
> Configuring your custom domain with your DNS provider without adding your custom domain to GitHub could result in someone else being able to host a site on one of your subdomains.

Which doesn't sound ominous at all!

I knew this situation didn't apply to me, though. I hadn't configured *anything* through Google yet, so this weird error was all on Github. 

So, instead of trying to use the Github UI to add a `CNAME`, I did it through Git itself. I just created a file called `CNAME` and pushed it, with my domain `joyce.fyi` inside. Github didn't complain then! So I kept forging ahead.

I went back to Google and continued following Github's instructions for apex domains, adding an `A` record to their servers, and a `CNAME` record to point to `thejoycekung.github.io` with the `www` name. I even patiently waited for DNS to propagate and checked with `dig` that my domain was now pointing to the Github load-balancers.

Easy, right? Everything should work, right?
> no ❤

Instead of showing *my* beautifully styled Hugo site, navigating to `joyce.fyi` instead showed, well, I guess, someone *else's* site. Someone else who also had the name Joyce, who wanted to say "Hello World. I am Joyce."

Someone who wasn't me. 👀

# A Quick Tour of DNS
I'll start off by saying I don't really understand DNS. All I know is that it is the routing system for most of the internet as I know it, as it is the translator for IP addresses to/from domains[^ip], and when things go wrong with the internet, "it's always DNS" will probably be said at some point (because it's probably DNS).

[^ip]: I seriously cannot memorize IP addresses. I hope I never have to in this lifetime. Maybe in another lifetime I have a different education system and I learn from a young age how to memorize IP addresses. But not now.

To understand my newfound case of ~~identity fraud~~ DNS troubles, I tried to break down what on earth was going on with my domain name and Github.

The `CNAME` record that I added to Google pointed to my Github Pages URL, `thejoycekung.github.io`. Its purpose was to say, "If you're looking for `www.joyce.fyi`, where you should *really* go is actually `thejoycekung.github.io`". 

The `CNAME` record in my repo, `thejoycekung.github.io`, then pointed *back* to `joyce.fyi`. Unsure why this was needed?

The `A` record was the important DNS record, from what I knew. By adding in the IP addresses for Github's servers, I was essentially telling everyone on the internet, "Hey! If you want to go to `joyce.fyi`, you should go ask Github!"

To summarize, this is what I thought the path was:  
`CNAME` (Google) ➡ `CNAME` (Github) ➡ `A` (Google) ➡ Github serves my page.

But something had clearly gone wrong in the last step.

So my first thought wasn't that someone had stolen my domain; my first thought was actually that someone had previously owned the domain `joyce.fyi` and had *also* used Github Pages to configure it. 

My guess is that Github probably used those `CNAME`s to determine which repo to serve when their nameserver was given a domain name query. And since it had errored out trying to accept *my* `CNAME`[^error], it meant that their name server didn't know that they were supposed to serve my repo when given the query `joyce.fyi`.

[^error]: That was probably why I had encountered the error `CNAME already taken` when trying to add my domain through the UI. (And probably why I shouldn't have ignored the error...)

# Now What?
I did a lot of Googling about this. It turns out that someone owning your domain before you and also having it hosted on Github Pages is not exactly a common problem. (Sigh.)

I sent an email off to Github Support, fingers crossed, asking if they had any guidance on what to do. How was I even going to find this person and tell them pretty please, stop hogging my domain because it's mine now?

The correct answer was, I was not. So I needed to find another way to host my website.

# Easy Domain Setup with ~~Github Pages~~ Netlify
I'd heard of Netlify for a while now (and by a while I mean this summer). It seemed like a great way to get a site up and running really fast - especially a static site - just by hooking up a Git repo to my account.

And ... it was, actually. It even had a spot for a build command, like `hugo`, and a spot to configure which directory and branch to build from. However, since I had a perfectly fine Github Action that built my Hugo site to my `gh-pages` branch already, I simply pointed Netlify to my `gh-pages` branch, told it not to run any commands, and use the repo directory `.` as the source code.

And it worked.

And then all I had to do was configure my Google DNS records to point to Netlify's load balancer and my new Netlify URL, and add `joyce.fyi` in the domain settings on Netlify.

And that ... also worked. After encountering so much friction with Github Pages, having something *just work* felt like magic again.

But obviously that wasn't the end of it.

# Easy Domain Setup with ~~Github Pages~~ ~~Netlify~~ Github Pages
I know, I know. I had a working setup. Why go back to Github Pages? Just burn the whole thing and move to Netlify. [^netlify]

[^netlify]: Okay, I actually did end up doing this, but that comes later in part 2.

Github Support actually responded to me around 12 hours after I filed my ticket, saying:
> The error you're seeing generally occurs when a custom domain has previously been owned by another GitHub user, or the domain has been added to a GitHub repository in error. If we can verify that you own the domain, we can free it up for your use using our admin tools.

A little odd that they can just `free it up` like that. With their `admin tools`.

But no matter. I was a woman on a mission to have `thejoycekung.github.io` redirect to `joyce.fyi`[^redirect], and my Netlify site was just a temporary diversion. I switched my `A` and `CNAME` records to point to Github again.

[^redirect]: Another reason why I valued my redirect was because I still had links to `thejoycekung.github.io` on some job-related stuff, I'm sure, somewhere ...

Github Support gave me instructions on adding a `TXT` record, and they asked me to include a verification code as the value, so that I could prove I owned the domain. 

![Screenshot of Google Domains: An A record has 4 Github IP addresses; below, a TXT record that has a Github verification code; finally, a CNAME that points my site to thejoycekung.github.io.](/img/github-verify.png)

After verifying that my `TXT` record had properly propagated using `dig`, I sent an email back to them saying so, to which they replied that they "released" the `CNAME` for me to claim.[^release]

[^release]: A part of me seriously wonders about the security concerns here. Isn't it at least a *little* disconcerting that Github Support can just reach in and "release" a repo's `CNAME`? Considering how it's stored per repo as a file, it seems like Github can just ... reach in and delete any file from your repo. And even ignoring their power for a second, are there any ways for bad actors to potentially ... add DNS records without an owner's knowledge? (Is that DNS cache poisoning? I don't understand how DNS works. This is a long footnote.)

And with that ... entering the `CNAME` on the Github UI finally didn't error out.

And finally, after at least 15 hours of "what on earth is going on", my site was being properly hosted from Github Pages using my new domain `joyce.fyi`.

Of course, I had to go and change that again in the coming weeks. But that's in Part 2.
