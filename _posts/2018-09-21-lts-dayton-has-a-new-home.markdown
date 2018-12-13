---

title: LTS Dayton Has a New Home
date: '2018-09-21 19:00:00'
---

A few years ago, I attended a presentation by the Miami Valley Regional Planning Commission ([MVRPC](https://www.mvrpc.org/)) at [The Adventure Summit](http://theadventuresummit.com/) about how they had adapted a Level of Traffic Stress (LTS) analysis to the [2015 Update](https://mvrpc.org/bike-plan-update) to the MVPRC Bikeways Plan.

The presentation piqued my interest, and I followed up with the speaker afterwards to obtain a copy of the data used by MVRPC for the LTS analysis. I transformed the data into a web app that was hosted on Azure. The app has always been near and dear to my heart, both because I do a fair bit of bicycle commuting myself and because I gave a [talk](/assets/ppt/lts-dayton.ppt) about it a local [meetup](https://meetup.com/daytondv) that lead directly to my current role at [AIS](https://www.appliedis.com) build geospatial web apps. For more info on the app and the data behind it, check out the [README](/lts-dayton).

The last time I checked on LTS Dayton, it occurred to me that it was really just a static site and there was no reason it needed a full-fledged web server. It has Express bundled with it, but that is really only necessary for local develop to avoid cross-origin requests. Anything that can serve up static HTML, CSS, and JavaScript could serve up LTS Dayton.

Today I migrated it from Azure to its new home on this site, powered by GitHub Pages. The only changes I had to make were updating the Leaflet CDN references to HTTPS, since this site uses HTTPS.

Ladies and gentlemen, [LTS Dayton](/lts-dayton/app/index.html)!
