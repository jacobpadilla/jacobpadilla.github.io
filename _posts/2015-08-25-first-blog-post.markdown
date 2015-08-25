---
layout: post
title:  "First Blog Post!"
date:   2015-08-25 10:27:09
categories: jekyll update
---
I've been himming and hawing about getting on GitHub and trying to be more social, so here I am. I would definitely characterize myself as a long time lurker of many projects hosted on GitHub. Some of which transitioned to GitHub a long while ago, like [youtube-dl][youtube-dl]. And some of which transitioned to GitHub recently, like [ASP.NET][aspnet]. Anyways, it seems like a good time to get more involved in the community.

Additionally, it was a sort of long strange trip just getting Jekyll working. So I might just poke around with that for a while to get acclimated with the workflow and tooling. I recently switched back to Windows from Fedora (Windows 10 free upgrade), so I'm currently playing around with the GitHub Desktop client and Atom.

Jekyll was kind of a piece of work trying to get it running. First of all it doesn't seem to run on Windows very well at all. You can't just install Ruby and then `gem install github-pages` and expect to be in business. At least one of the gem packages requires a native build and Windows just doesn't seem to be its thing.

So me being the wise guy I am, I'm thinking I'll start a little minimal Ubuntu VM and run Jekyll off that. Wrong again. I go and `sudo apt-get install ruby ruby-dev` then `gem install github-pages` and come to find out we need Ruby version >1.9. At this point I screw around with RVM for a couple hours and then go to bed pissed.

Today was a new day. I installed Fedora server on a VM which has the appropriate version of Ruby / zlib and whatever else I needed. Then `gem install github-pages` and found out I needed some kind of javascript engine. So I installed npm `gem install github-pages` and we're in business.

Doesn't that all seem a little ridiculous? How many different package distribution channels and wacky error message hoops do I have to jump through to install a static web site generator?!!?

[youtube-dl]:      https://github.com/rg3/youtube-dl
[aspnet]:   https://github.com/aspnet
