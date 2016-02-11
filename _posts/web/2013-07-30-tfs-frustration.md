---
layout: post
title: Team Foundation Server Frustration
categories: web
---

# Team Foundation Server Frustration

For my first post on my first blog, let's complain about a Microsoft product - Team Foundation Server 2010. 
If you're not familiar with TFS 2010, think of it as Github. Except it's not on the cloud, and its added with 
a lot of useful features that are needlessly complicated, especially if you have a problem with one of them. 
Now, I'm sure some or all of my complaints were remedied in later versions of TFS, but, where I work, I don't 
have the luxury of always receiving the latest and greatest software versions. I must use what's given to me. 
It doesn't quite hit the mark in all areas, but it is a satisfactory source code version control repository 
and software life-cycle management tool.

## A Foundation of Team Frustration

> I poured endlessly over the installation guide, navigating back and forth between steps, taking 
> Microsoft's suggested recommendations, and yet I still felt like I was doing it wrong.

My first frustration with TFS is its over-complicated installation process. I swear I was installing the 
guidance software on a billion dollar weapon system. Every configuration option I made was taken with held 
breath. I poured endlessly over the installation guide, navigating back and forth between steps, taking 
Microsoft's suggested recommendations, and yet I still felt like I was doing it wrong. SharePoint services? 
Really? What's a web proxy? We need a build server, right? I guess so. That sounds important. Wait. What's 
reporting services? Managers need reports, right? So, let's install that too. Sigh.

I ended up installing everything because there's no way I wanted to go through that process again. Features 
that I later figured out I didn't need, I switched off.  However, let's move on to the bane of my existence 
with TFS 2010. Two things, really: 

1. Removing artifacts from TFS
2. Configuring reports

## The Lesser of Two Evils?

> Someone should have slapped me with the force of a thousand backhands.

Why is deleting artifacts (backlog items, tasks, sprints, etc.) in TFS seemingly impossible? There is no delete 
button or menu option. I dare you to find it. Sure, you can save a backlog item as removed, but, guess what, 
it's still there. The only way to remove an artifact permanently is via command line, and Microsoft doesn't 
make it easy to find. For gawd's sake, let me delete what I want to even if it screws my project up! Let me own 
that decision. I don't like being boxed in. I shouldn't have to delve into secret manuscripts to uncover Easter 
eggs of functionality long meant to never see the light of day for mere mortals such as myself. Using software 
should never be this hard.

Configuring reporting...hell, I gave up on this venture after months of hair-pulling. The crazy thing is that is 
worked beautifully, at first. That was before our network team forced us to re-install SQL Server 2008 R2 a 
couple of months later. I figured that backing up the TFS databases before re-installation and then restoring 
them after wouldn't be a big deal. Someone should have slapped me with the force of a thousand
backhands. How dare you, fool?! Well, after some tinkering, the restoration did work. All of our 
artifacts were there; however, reporting has been broken since. I've rebuilt the reporting and analytic databases 
from scratch and reconfigured everything to no avail. The last thing I want to try is re-installing TFS from 
scratch. Yes, I swore I would never go through that process again, but it's the only thing I haven't done.

On the other hand, why even go through all that pain? We've got Team Foundation Service now. Let them go bald.
