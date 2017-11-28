---

title: Today, I Finally Learned Regular Expressions
date: '2015-05-01 02:14:03'
---

Having been a professional software engineer for 8+ years now, this is somewhat embarrassing to admit: 

*I never learned how to write regular expressions.*

Sure, I've *used* regular expressions plenty of times, but those usages typically involved heading over to Google, searching for the regex I needed, and copy/pasting it into the appropriate place in code.

I sort of knew what some of the syntax meant, such as [a-zA-Z] or \d, but most of the time each expression was an incomprehensible set of characters/gibberish/black magic that I hoped would do whatever the search result claimed it would.

Of course, any time regular expressions were mentioned, I was always the guy that would chime in with the clever *"...and now you have two problems!"* Jamie Zawinski quote. While in most cases it was probably true, perhaps I would have actually been able to provide some help from time to time if I had put forth the effort to understand them.

Today, my moment of truth arrived. 

I had written a utility to load data via a REST API, which I had used on several occasions and worked perfectly fine. I needed to load a vastly larger data set and I was concerned the overhead of making thousands of individual REST calls. Being the lazy developer that I am, I wanted to change as little code as possible. I modified my utility to write out queries to a text file, which I would consume and execute in batches using a native API (different language, hence the two separate programs). Perfect! Except that the generated queries were serialized into JSON, and I needed to strip out the quotes around the keys in the key/value pairs in order to generate valid statements for the API I was using. 

I spent a few minutes investigating how easy it would be to change the serialization. Since I'd be trying to get a library to serialize into invalid JSON, the answer was "not very."

	Some people, when confronted with a problem, think "I know, I'll use regular expressions." Now they have two problems.
    
Actually, I had *four* problems. The two from Zawinski's quote, plus two more: it seemed very unlikely that I'd be able to find a pre-packaged regex to do what I wanted, and *I didn't know how to write regular expressions.*

So, I sucked it up, read a few tutorials, and after some trial and error and the help of an online regex tester (thanks [RegexPlanet](http://www.regexplanet.com/advanced/java/index.html)!), I built the regex I needed. 

Now I no longer have to suffer silently in shame, and I can tell people I meet at parties I know regular expressions! (or do [this](https://xkcd.com/208/)!)

Oh, and for the record, this is the regex: (\s"([a-zA-z]*)":\s("?))

