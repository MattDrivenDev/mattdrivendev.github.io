+++
date = "2013-04-02T22:15:56+01:00"
draft = false
title = "Adopting F#, part 2"

+++

This post has been a long time coming. I've a few floating around in various *draft* states and some that exist only in my head at the moment. But I've decided to use today's lunchtime to try and drive this one home.

I've [previously talked about my journey so far with adopting F#](/2013/01/adopting-fsharp.html), and have decided that as things for me have moved on some since then; it was worth me writing a follow-up.

So what has changed since January?

* Written a new web service in `F#` and seen it deployed to a production environment
* Spent a day doing some pair-programming to help a colleague learn some more about `F#`
* Written a few more `F#` ETL processes that help us slice-and-dice data and move it around
* Written a couple of very little tools in `F#` that help automate and/or provide self-serve functionality to others.

<!--more-->

### New Web Service

Or rather a new component (or logical service) within an existing web service (or physical service, I guess). Here is a little technical information:

* New component (or logical service interface) completely written in `F#` that lives inside an existing `C#` SOAP web service.
* Service is stateless (except for a few health/monitoring counters).
* Service is required to be high-performant.
* Message-based SOAP API.
* Requirements/behaviour(s) documented and automated in Gherkin with SpecFlow, coupled with a fairly extensive unit testing suite.
* Built using `FAKE` and automated with TeamCity.

The development process was quite interesting, using some of the [process improvement](/2013/02/agile-process-improvement-brain-dump.html) methods we've identified and committed to - we (me and the guy developing the first client code to use the new service) decided to front-load all the hard parts. I spent 2 of the of the 3 week sprint building supporting tools (self-serve, ETL etc) and it wasn't until the final week that I actually got on to the service code itself.

One whole day of the final week developing the `F#` service was also done as pair-programming with a 3rd member of the team who is also learning the language. We worked in from some requirements written gherkin and ended up with a completed piece of functionality. That was a great experience as well, and we should certainly commit more time to things like this.

The new service was deployed just over a month ago now, and has been stable and without bugs since (I know it is still *really* early days, but I'm confident).

I did have some interesting observations, some which I've seen and read about elsewhere - but worth repeating:

* Writing `F#` code as part of a test-driven process breaks that process, but in a good way. By the time you've thought about your domain model and have everything in place - when the code finally compiles it normally passes the first test first time. By the time we got around to hoooking up further tests, it all went green without making any changes. As it is still early days I decided to really look into it as I didn't *fully* trust it - but my distrust was proven misplaced and everything was fine. 
* Because we were working in a world without `null`'s and with a very strict type system - we actually uncovered a few nasty bugs in existing/surrounding `C#` code.
* It was rather easy to build a kind of fluent DSL of modules and functions that allowed us to compose the over-arching functionality in a way that was very readable and easy to follow - and in places even read like some of the requirements.

This is our first *production ready* `F#` application code, used as something of a pilot to test the waters (we're already really fond of the language and use it personally, but we needed to prove to the business that it would be of benefit). 

So far, so good. And I think it'll be the first of many new `F#` developments.

### ETL and other little tools

I'll skim over this topic rather as it is generally more of the same as I mentioned in the first part of this 'series'.

The interesting new part for me here is that we've agreed that for little tools like this (i.e. non-mission critial), we're happy to make use of some of the new Type Provider work being done:

* WSDL Type Provider ([read more](http://msdn.microsoft.com/en-gb/library/hh362328.aspx))
* CSV Type Provider ([read more](http://tpetricek.github.com/FSharp.Data/))

I've found that `F#` is the perfect lanugage to do some of this work. You can get a lot done, quite cleanly in very little code. You've the option of packaging it up as a little application, using it as a library or just rolling out scripts (with batch files etc). And with the Type Providers you can get a whole lot done in a very short space of time.

Most of the scripted tools come with a `config.fsx` as well, which is very easily read/edited by anyone with technical knowledge (even if they don't have any experience with `F#`).