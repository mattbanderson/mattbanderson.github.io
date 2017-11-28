---

title: Updating a Ghost Blog on Azure
date: '2016-04-18 01:32:59'
---

#DON'T

Just kidding. Sort of. 

#Quick Create Works Great

In my experience, updating a Ghost blog on Azure is hit-or-miss. 

I originally set up this blog in October 2014 using the "Quick Create" option in Azure to create a Ghost blog. This worked very well and I was up and running in minutes. I don't specifically recall what Ghost version this was, but I think it was something like 0.3.x or 0.4.x. 

#Upgrade #1: Success!
Fast forward a few months and I thought it would be a good idea to upgrade. If I recall correctly, I wanted to set up a static page, and that feature had recently been added to Ghost. I also took a stab at deploying from a local Git repository. 

This upgrade went reasonably well, aside from the fact that it took a while because I added everything&mdash;node_modules and all&mdash;to my Git repo because I wasn't sure if Azure would run an <code>npm install</code>. At this point I was running Ghost 0.5.7.

#Upgrade #2: Not So Much
A few days ago, it occurred to me that I hadn't upgraded Ghost in a while. I downloaded the most recent Ghost release (0.7.9) and followed the [steps to upgrade Ghost](http://support.ghost.org/how-to-upgrade/). I even went thought the trouble of testing the upgrade first locally to make sure everything worked ;) 

I also backed up a copy of the entire blog as well as exported a file containing my blog settings and data using the "Export" feature of the Ghost admin site (in the "Labs" section).

This time I decided to be smarter about my Git repo and exclude things like <code>node_modules</code> (and <code>config.js</code>, oops!). With the upgrade tested and my repo set up, I added Azure as a remote and did a push. The console output looked promising (Azure does perform an <code>npm install</code>) and eventually reported success.

##IIS 500.1002 Errors

Unfortunately, when I tried to view my blog, I was greeted by a 500 error. Closer inspection of the error indicated it was a 500.1002 error. Based on other sites I've run on Azure, this seems to be a very generic type of error. I double checked my configuration, tried changing a few settings, etc. but was unable to fix the problem or find any additional details.

Since I was already in my FTP client tweaking various files, I figured it couldn't hurt to try just deploying the site via FTP. Sadly, this did not fix the problem either and I continued to get the same error.

##Ghost Updater to the Rescue?
By this point I was Googling around trying to find ideas to fix the problem when I thought I found a tailor-made solution: [Ghost Updater](https://github.com/felixrieseberg/Ghost-Updater-Azure) by Felix Rieseberg. It's a small application specifically designed to update a Ghost blog running on Azure. 

Unfortunately, the updater did not work for me. Much like my Git deploy, the output logging appeared to indicate success, but I was still greeted by the same 500.1002 error when I tried to access my blog.

#Just Spin Up a New Ghost Blog

By this point I was getting frustrated and I was starting to wonder if maybe there was some "special sauce" that the Azure "Quick Create" Ghost blog added that my attempts at deployment were missing.

I'm hosting my blog in the cloud, might as take advantage of the cloud, right? I created a new Ghost blog instance (using Quick Create), imported my exported data, changed my custom domain from the existing Azure app to the new one, pointed my DNS records at the new site, and updated <code>config.js</code>. Sounds like a lot, but it's all pretty straightforward. A few minutes later, my newly updated blog was back up and running. The only thing missing was my cover image, which I had to re-upload.

Sadly, at this point in time, spinning up a new Ghost blog using Quick Create and doing an import seems to be the most painless way to upgrade a Ghost blog on Azure. When the time comes to upgrade again, I may take a stab at Git deployment, but if my site fails to load, I will go straight to spinning up a new instance. ¯\\\_(ツ)_/¯




