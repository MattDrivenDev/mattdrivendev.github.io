+++
date = "2013-01-13T22:14:03+01:00"
draft = false
title = "Adopting F#, part 1"

+++

A second blog post of the day? Barely - I ran `rake new_post` at 23:59. Odd. Anyway...

I've been thinking a little about my foray into the world of function programming, specifically F# - and thought it might be potentially useful to others **how *I*** have progressed from knowing nothing; to the point that I (and others) feel confident that I can ship production code in the work place using the language.

I'm **not** really going to cover the following:

* Benefits of Functional Programming & F#
* How-2-F#

...largely because there are a great many resources out there for those that want to know more about what it all is, why you should care and how you go about learning it. A great starting place for that sort of information is the [F# Software Foundation](http://fsharp.org).

What I will focus on is how I've gone from the point of learning, to the point of using.

<!--more-->

The main thing arch that I've followed is trying to avoid biting off more than I can chew. To that end I stayed away from building anything resembling an application in F# to begin with (although the temptation was certainly there). Instead I decided to use it for those little scripting jobs that I needed - instead of using Powershell or whatever.

F# is a great language for tackling those "little jobs". Why? Firstly it can be used as a scripting language instead/as well as a compiled code base (`.fsx` files can be run through the F# Interative executable). Secondly it lacks the required code structuring that a language like C# has - you don't have to build up classes etc; you can get straight in there write a few functions and call it. Thirdly due to the nature of the language and the compiler - duff code simply doesn't work, and it shouts at you before running; this makes it safer (and by the time you're done working on it, it's likely to work perfectly).

### 1. Build Scripts

My first adventure into using F# for something "real" was build/test scripting, and subsequently automation in a CI server (TeamCity for those that care). I won't go into why build/test scripts are important - that's a whole different topic and obviously other people will have covered that better than I can.

I'd been told about [FAKE (F# Make)](http://fsharp.github.com/FAKE/) - so looked into it and started using it on the project that I was working on at the time. It was simple to read, quick to get up off the ground and had next to no code "noise" (like XML markup). Take a look at [this example build script](https://github.com/fsharp/FAKE/blob/develop/Samples/Calculator/completeBuild.fsx) - Clean. Concise. Human readable.

Because FAKE is nice and extensible - I was able to do some extra bits that I needed as well. Some of which has been pushed back into the OSS project (SpecFlow helper code) and some of it that needs to stay in-house at work and really is bespoke for our needs (CSV-to-Gherkin work that helped our software testers easily generate 1000 tests that would re-use my own acceptance test code). But this gave me a little more exposure to F# as a language - and was still a small enough component that I didn't get lost.

Since then I've retro-fitted FAKE build scripts on almost all of our .NET projects at work, and used it personally as well. Normally with something new the FAKE build scripts are in the 2nd or 3rd commit, without fail.

### 2. Non-ETL Scripts

I've dubbed them Non-ETL scripts because they are not really ETL scripts as we know from SQL land - but they do the same job. Grab some data, transform it and load it elsewhere. But I was dealing with SOAP web services as a data source, XSL transformations and pushing the output to wherever it was needed (either another SOAP web service or a document repository).

Again F# scripts were perfect for this job. I could leverage all the power of the .NET framework - while making something that was compact, clear, concise, human readable and very easy to configure (inline rather than config files). Wrap them with a `.bat` file and allow operations to set up scheduled tasks using their favourite task scheduler.

The result was generally 1 main script that does the heavy lifting, generally with 1 entry point function with some params as the single variable - and 1 wrapper script that had configuration that created the params and called the function. 

Since then I've done about a half-dozen of these little jobs - and I have to say that F# is now my go-to scripting language without a doubt.

### 3. Tests

I've now taken to writing my tests with F#. A few reasons: firstly I like writing F#, secondly because it is clear and concise and very easy to read the code of each test and see what is going on and finally because of the ability to name types and functions with string values (wrapped in double back-ticks) [example (crude, but shows what I mean)](https://github.com/saxonmatt/HouseViewing/blob/master/HouseViewing/HouseViewingTests/Health.fs).

This final point allows you to better express your test code as readable requirements - and helps others read your test results and better see your intentions etc.

I'm now quite interested in using some of the F# Type Providers (go Google that) to help use F# test frameworks to test code that doesn't necessarily lend itself to be otherwise easily tested. 

E.g. testing ETL jobs (real ones) - using the SQL Type Provider to quickly (and very cheaply) just query the loaded data to assert everything is in order. Or using the Wsdl type provider to quickly (and very cheaply) check to make sure that changes to a system are correctly exposed in a web service.

### Onwards

I'm looking forwards to this year - we're now looking at projects coming up and identifying parts that can easily be separated out into components; and seriously considering using F# instead of C#. 

The things that most interest me now are trying to learn how to:

* Refactor F# code... because it just feels a little different to refactoring OO code. I'm sure the answer is with currying/partial application and/or composition.
* Application structure... when to use modules and namespaces, how to separate out responsibilities etc. Again it feels a little different to structuring an OO application code base.