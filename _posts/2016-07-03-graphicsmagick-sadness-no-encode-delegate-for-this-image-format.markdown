---

title: 'GraphicsMagick sadness: "No encode delegate for this image format"'
date: '2016-07-03 20:13:08'
---

So, you're trying to use [GraphicsMagick](http://www.graphicsmagick.org/) (or something that uses GraphicsMagick under the hood) to do some image conversion or processing.

Output files are generated as expected, and their size looks reasonable. But you can't open them. You try several different programs but they all indicate they are unable to open them because something is wrong (e.g. they are corrupted, etc.).

Some further debugging indicates an error along the lines of "No encode delegate for this image format". Which naturally begs the question, how do I get the right encode delegate?

First, run <code>gm version</code> from the console. Is there a "yes" value for each of the file types involved in the conversion or processing? Alternatively, run <code>gm convert -list formats</code>. Are all of the file types involved listed?

If not, there is a good chance this is the root of the problem. The "no" values or not being in the formats list confirms that one or more missing encode delegates are indeed causing problems.

It may be&mdash;at least it was in my case&mdash;that the encode delegates are not linked correctly rather than missing altogether.

Run <code>brew doctor</code>. What does the output say? Follow any instructions it gives you. In my case, it indicated some files GraphicsMagick files were unlinked. Run the commands suggested by brew in your terminal.

Now run <code>gm version</code> or <code>gm convert -list formats</code> again. Do you see "yes" in the appropriate column or see the file types listed?

If so, great! That should solve the problem.

If not, I would suggest trying to install the missing encode delegates using brew. For example, <code>brew install libtiff</code>, then <code>brew reinstall graphicsmagick --with-libtiff</code>.

As best I can tell, when I installed GraphicsMagick, some files were not linked as expected. This may have occurred because I originally installed GraphicsMagick by downloading and extracting it from [graphicsmagick](http://www.graphicsmagick.org/) instead of using brew.

Moral of the story: **use brew**!