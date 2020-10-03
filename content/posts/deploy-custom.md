---
title: "Abandoning Github Pages for Netlify (ii)"
date: 2020-09-26T11:26:58-04:00
categories:
- meta
---

In the [first part of this post](/posts/custom-domain), I discussed getting and setting up a custom domain for this site (and all its various challenges).  
In this post, I'll be talking about how I abandoned all that hard work just to go back to Netlify.

---

Another thing that's become obviously lacking in my blog setup has been the ability to collaborate (read: for people I know to edit my work).[^edit] This was something that became painfully obvious the week after [Down Under CTF](/posts/ductf-2020), where I basically just published our writeup, and then let my friends go on the blog and point out my mistakes as they were sitting in production.

[^edit]: This is less important for posts like these, and more important for things like our [collective CTF writeups](/categories/writeup/).

Not *exactly* the best setup.

So I started thinking about what our *current* workflow looked like, what a smoother one could look like, and how we could possibly bridge that gap.

This is what I came up with for a "current" workflow:
1. I write up a post and commit my changes to a branch
2. I push my branch to Github and create a pull request
3. My friends pull my branch and build the site locally (to ensure no major formatting issues, and for extra ease of reading).
4. My friends read my work.
5. My friends commit their necessary edits to the branch and push their changes, OR they "start a review" on my PR with their comments/edits

This system could work great as is, but there's one major flaw: my friends don't use Hugo. So the "current" workflow isn't really true. And I don't want to make them install something they don't use, for the simple task of editing my work.

How could we make this simpler?

# Exploring my Options
The pain point for me in this workflow was getting others to install Hugo (building the site once having installed Hugo is easy, that's not the problem).

One way to avoid the problem altogether was just push my work to a branch and let them review a plain Markdown file. Although Markdown is readable, I figured it would be better to allow them to read the formatted version instead - that way they can also catch any (accidental) formatting flukes I've made.

Once I nailed down the fact that I wanted the site to actually be built, I needed to think about *where*. I thought about using a self-hosted Docker container that used a Hugo image (that way Hugo wouldn't have to be redownloaded everytime). The container could have a public IP and exposed port, and a user could then try to view that resulting post by navigating to the combination `IP:port`. 

I wanted to try it out a little as an exercise to me (in writing Dockerfiles), but then I realised that this is actually ... probably a pretty common problem. And someone had likely created a solution for it already.

# Choosing Netlify for Quick Deployments
After some (a lot?) of searching to see what *other* people had chosen to do for their PR deployments, I found my way back to Netlify again.

I discovered that Netlify had this cool feature called [Deploy Previews](https://www.netlify.com/blog/2016/07/20/introducing-deploy-previews-in-netlify/), and remembering how painless it was to set up Netlify originally, I decided to try and use Deploy Previews as a simple way to deploy my PRs.

The one thing that I couldn't *quite* understand was how to actually ... trigger them?

# Creating a Netlify Actions Workflow
Because it's Github, and [I've tried out Github Actions](https://twitter.com/commitsbyjoyce/status/1282792509882748928) before, I decided to look on the Actions Marketplace to find an action that could help me out.

I found [Netlify Actions by nwtgck](https://github.com/marketplace/actions/netlify-actions) which seemed to meet my needs perfectly; the description reads:
> Github Actions for deploying to Netlify

Truly music to my ears.

With that Action in mind, I started thinking about what I needed to make this work. 
- [ ] I needed to have my Netlify site again, but I wasn't actually going to use it for anything - I just needed auth and a site ID so I could run this Action. 
- [ ] I also needed a built version of the site in a directory, so the Action could actually get code to deploy (after all, there's no separate branch here). 
- [ ] Finally, I needed an actual workflow in YAML so that it would actually run on a branch I set up.

So this is the workflow that I actually came up with:
1. `actions/checkout@v2`
2. `peaceiris/actions-hugo@v2`
3. `hugo` (shell step - not an Action)
4. `nwtgck/actions-netlify@v1.1`

Essentially, first, we'll checkout the repo using `actions/checkout@v2`. Then, we'll install Hugo using `peaceiris/actions-hugo@v2`. Then, we'll run `hugo` as a shell step and build the site. Finally, we'll deploy the `public` directory to Netlify using `nwtgck/actions-netlify@v1.1`.

In order to test this all out, I made a branch (with a draft of this current post, actually!) and pushed it, creating a pull request and therefore triggering the workflow.

And ... then it failed. 

# ~~Creating~~ Scrapping my Netlify Actions Workflow
Well, actually, it failed in a couple ways.

First off, I didn't realise that using Netlify Actions wouldn't somehow auto-magically get the right base URL. My base URL `joyce.fyi` is configured in my `config.toml` file, so when pulling assets (like, uh, say, CSS), they would pull from `joyce.fyi/{css_file_name}`. But this didn't just apply to assets: it also applied to links on the page. So when I tried to open my new post from this Netlify deploy, I actually went to `joyce.fyi/posts/{new_post_name}` - which, obviously, didn't exist yet.

The second thing that I didn't realise was that Netlify itself was *also* trying to deploy this branch (using those magical Deploy Previews I'd read about!) and it was failing, miserably. I didn't realise it while setting up in my haste, but [because I granted Netlify repo access](https://docs.netlify.com/configure-builds/repo-permissions-linking/#authentication-with-the-netlify-github-app), it had gone ahead and added [Checks](https://docs.github.com/en/free-pro-team@latest/rest/reference/checks) to my PRs (one of which was the Deploy Preview).

My suspicion was that Netlify was failing because I had configured it to build (the production version of) my site by essentially, not doing anything and pulling the source code from the `gh-pages` branch. So it was getting *real* confused on what the heck was happening.

To fix the Netlify workflow I had created, I probably needed to reconfigure how the `baseUrl` from my site is loaded. After some searching, I discovered that this was configurable from the Hugo command by passing the `-b` option with a URL. However, how was I supposed to know *what* URL was going to be generated for this deploy?

To fix the Netlify checks, I could add some extra configurations to how pull requests are built (through a `netlify.toml` file). Or I could just disable the checks altogether - but that would remove the deploy previews.

Or I could abandon Netlify altogether. And go back to my Docker idea, or find other ways to deploy drafts.

# ~~Creating~~ ~~Scrapping~~ Fixing my Netlify ~~Actions~~ Workflow

In the end, I decided to add extra configurations to how pull requests are built. I created a `netlify.toml` file to the root of my repo and added a few lines to configure my pull requests:

```toml
[build]
HUGO_VERSION = "0.74.3"
# Netlify's Hugo version is lower, causing trouble with taxonomyTerms

[context.deploy-preview]
command = "hugo -b $DEPLOY_PRIME_URL"
publish = "public/"
```

This way, I didn't need a separate Action to install & run Hugo to build my site. In fact, I could get rid of that workflow completely and just *trust* Netlify to work. 

And since I was relying on Netlify for my pull requests, I figured I might as well use Netlify for the hosting of my actual site as well. Remembering how easy it was to hook it up the first time I did it, I went back and moved my setup (*again*, I know) to Netlify once more. I even configured a `netlify.toml` file for the root of my published site[^two] to redirect from `joycekung.netlify.app` to my new domain:

```toml
[[redirects]]
from = "https://joycekung.netlify.app"
to = "https://joyce.fyi"
status = 301
force = true
```

[^two]: If you noticed carefully, this means I have *two* `netlify.toml` files hanging around my repo now. One of them lives in the root of my source code, so Netlify properly builds my deploy previews. The other lives in the root of my published directory, so that Netlify properly redirects the `netlify.app` URL to my custom domain. This is obviously not ideal, but for now, I'm too tired to try and un-fuck it.

# Conclusion
Now, whenever I want to make a new post (and have it vetted), it can go through this process:
1. I write the post and commit my changes to a branch
2. I push my branch to Github and create a pull request
   This triggers a series of Netlify checks, one of which creates a Deploy Preview of my site.
3. My friends read my work in all its fully-formatted glory
4. My friends commit their necessary edits to the branch and push their changes, OR they "start a review" on my PR with their comments/edits

So I'm happy to say that after a few weeks of waffling, I finally have all three things I was looking for:
* A custom domain
* Easy deployments
* Easy deployments for my pull requests

I'll probably keep modifying the setup for this blog over time. But for now, this'll work just fine ☺
