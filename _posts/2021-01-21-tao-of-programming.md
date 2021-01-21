---
layout: post
title: A run-in with the Tao of programming
---

It was a lazy lockdown afternoon, and my team had gathered to discuss some architectural decisions for one of our applications. We were building an API to a larger system, which would be accessed via an interface to configure and tweak the components of the system. A component of this API involved acquiring a lock on a certain resource, and releasing the lock after the resource was released. My original proposal involved maintaining an *in-memory* lock, which would gate access to the resource on that instance of the application. At the time, the amount of traffic coming to that application was quite low, and a single app instance would suffice, at least for the foreseeable future.

Then somebody brought up the idea that the in-memory lock solution would not be future-proof. What if we scaled up and eventually needed some form of distributed locking? We’d have to rewrite the locking mechanism. It’s better to handle the potential problem now than to wait until it becomes a problem. <br><br>

 *"It’s better to handle the potential problem now than to wait until it becomes a problem."*
<br><br>
At first thought, this sentence seems intuitively sound. I’d heard and read this sage advice in many forms, and there was no reason now to pick it apart. On this occasion however, something felt off. There was something wrong with this argument, but I couldn’t figure out what it was. Eventually I relented, not able to articulate why I felt that it was the wrong decision. But that day, the thought still stayed in my mind. A disturbance in the force.

That night, I rolled it around in my mind. Why would changing the locking mechanism in the future be a “problem”? We would have to rewrite it, yes, but would that be a problem? I thought maybe the problem was in the reputation of the term. “Rewrites” have gotten a bad rap in software engineering. We think of rewrites as a last resort to untangle bad or shortsighted design decisions, or a sign of badly written code. 
<br><br>

*“If it needs to be rewritten, it wasn’t written right in the first place”*
<br><br>
If I accidentally plagiarised this quote from somebody’s LinkedIn feed, then I am not sorry in the least. As with most simplistic statements, this too gets things horribly wrong. What about the side effects? What about the requirements that changed over time? In my case, my application would do with a single application server for more than 10x the original traffic. Why would I need to design a distributed locking mechanism for a system that wasn’t distributed in the first place? I would inherit all the drawbacks of distributed locking, with none of the advantages. While trying so hard to design our system to be future-proof, we had forgotten to make it present-proof!

So if I had to rewrite the LinkedIn quote, it would go :

*“If it needs to be rewritten, it wasn’t written right in the first place if all requirements remain unchanged and within a reasonable time horizon”.*

Not so sexy now, is it?

The point is that if modules are written with well defined APIs and state boundaries, rewriting modules can be quick, painless, and dare I say, fun! It’s natural for the requirements and scale of a system to evolve over time, and it is virtually impossible to predict and handle all the potential problems that come with scale. 

And so it was that I went back to the team and told them of my epiphany. We implemented our plain old monolithic locking module, which exposes a well defined set of *`lock()`* and *`release()`* APIs. If there comes a day when it’s no longer enough, all we need do is rewrite(*:wince:*) the guts of the lock service, and everything just works. And that is the story of my run-in with the “Tao” of programming.


