---
title: "Using Netlify and Github Actions to Deploy Pull Requests"
date: 2020-09-26T11:26:58-04:00
tags:
- hugo
---

I've been using my [Hugo setup](/posts/a-note-old-blog/) for just over a month now (and really enjoying it!), but one of the biggest things I feel I'm lacking is a decent way for people to edit my posts. [^1]

[^1]: This is less important for posts like these, and more important for things like our [collective CTF writeups](/categories/writeup/).

This is what a current workflow could look like, for editing:
1. I write up a post and commit my changes to a branch
2. I push my branch to Github and create a pull request
3. My friends pull my branch and build the site locally (to ensure no major formatting issues, and for extra ease of reading).
4. My friends read my work.
5. My friends commit their necessary edits to the branch and push their changes, OR they "start a review" on my PR with their comments/edits

This system could work great, but there's one major flaw: my friends don't use Hugo. And I don't want to make them install something they don't use, for the simple fact of editing my work.

# Choosing Netlify
My original thought when exploring this idea was to use a Github Action run on a self-hosted Docker container that used a Hugo image. The container could have a public IP and an exposed port, and then a user trying to view the resulting post could then navigate to that IP:port.

Or - I could just use what other people had built. "Stand on the shoulders of giants" and all that.

I'd heard of Netlify as a great, quick way to deploy websites before, and in particular statically-generated sites, like this one. So I wanted to take the chance to try it out.[^2]

[^2]: I'm currently using Github Pages for this site, but who knows - I might move it in the future.

Linking this repo to Netlify was fairly easy. All I had to do was sign into Github and allow Netlify permissions for this repository. That's it!

# Building the Workflow
Now that I have Netlify set up, all I have to do is set up the Github workflow.

I'm using 3 actions (available on marketplace) for this:
1. `actions/checkout@v2`
2. `peaceiris/actions-hugo@v2`
3. `nwtgck/actions-netlify@v1.1`

First, we'll checkout the repo using `actions/checkout@v2`. Then, we'll install Hugo using `peaceiris/actions-hugo@v2`. Then, we'll run `hugo` and build the site. Finally, we'll deploy the `public` directory to Netlify using `nwtgck/actions-netlify@v1.1`.
