+++
date = "2013-10-15T22:29:48+01:00"
draft = false
title = "Test-first F# with Acceptance tests and the REPL"

+++

Lunchtime blogging strikes again - this post has probably been brought to you over the course of a few lunch times.

Like most of my posts on the subject of `F#` there really isn't much I'm adding to the wealth of the community, but I feel as if contributing to the community in blog posts from an *average .NET developer's* perspective does have value. F# is pegged and marketed as a great language for niche problem domains (just look at the [Try F#: Learn page](http://www.tryfsharp.org/Learn)) - but I'm all about using F# as a great general purpose language, instead of C# or VB.NET.

This post is probably a long time coming since I'm really enthusiastic about both *test-first* software development and F#. I've been mostly prompted to write my thoughts and findings down in response to seeing [RobAston](https://twitter.com/RobAshton)'s blog post - [On testing and the REPL in Clojure](http://codeofrob.com/entries/on-testing-and-the-repl-in-clojure.html); who I've been following (which as I can see from Twitter, most of the developer community does!) while he adopts [Clojure](http://clojure.org/).

So what am I going to be writing about? Basically how using F# makes it easy for me to drop some of the dogma and get on with it. I've done a few projects now at work which involved writing F#, and this kind of outlines the *how I work* while doing it.

<!--more-->

## 1. Requirements

I'll skim over this, because it really should be a no-brainer. Like most good tests it all tends to start with a requirement - verbal, written, whatever; which we're going to boil down condense into some executable form.

## 2. Outside-in

I almost exclusively work outside-in, for a multitude of reasons that are explained at great length elsewhere. Go Google it. 

So my main tests are going to be running from the outside, based on requirements. Acceptance tests I guess.

## 3. Tests!

*Read as: executable requirements documents*. 

I've toyed with fluent libraries, Gherkin (Cucumber/SpecFlow), building my own *readable* API in my test harness and more recently - just using F# with the double-back-tick syntax. The key here is that I'm directly creating tests that are mapped to requirements - using real examples, stuff that other people can understand (not necessarily the stakeholders, but just other team members is a great start); hopefully the aim is to create tests that simply describe expected **behaviours**.

You're thinking about the problem. Thinking about how you're going to know that you've solved the problem correctly - without actually thinking about solving the problem... if you follow me. The, *thinking about how you're going to know that you've solved the problem correctly* is a key part - which is how you're going to make the code easily testable. Some requirements have less obvious answers, answers that perhaps are only visible in downstream systems etc (end-to-end systems/integration testing anyone?) - this is often a sign where you should put some good logging that can tell people who care, what the system is doing.

## 4. API

Chances are at this point I'll need some new things to talk to from my test code. Interfaces/Schemas/Contracts/API - whatever. Something that I can call from the outside world (outside-in remember) - passing in everything that it needs to know.

## 5. Make it green

Making the first test pass is often the longest part. There is often a large body of *stuff* that needs to be built up, just to get that *skeleton* of an application in place. 

Sometimes the solutions present themselves very easily, it's a known problem with well suited known methods to solve them. No brainers. At this stage I wouldn't get too caught up with creating smaller tests to explore how the implementation of the solution will fit together. I would just invest the time in solving the problem how I know best - any tests you create at this point would be expensive to maintain, brittle and essentially you should just end up deleting them later anyway.

But what happens when I don't know what to do. It doesn't necessarily need to be a problem that isn't solved by the community at large - but a problem that you (and your team) haven't encountered before (probably some weird business process/logic that you're automating). In a stricter TDD world I'd be guided by tests here - fleshing out little parts from the outside-inwards with test cases discovering and designing the internals of the application. Until such a time, that you hopefully - make the first real test that matters, pass (and go green). Some folks call this *changing down gears*.

But with F# you have other options...

### 5. ...make it green, with added REPL!

I'm not experienced enough with functional programming to simply *know* the solution yet, I'm sure with time this might come. I'm starting to recognise a few patterns, and a few ideas like *choice types* and *map/reduce* can solve a huge number of things, as I've seen. But I'm equally sure that even when I do feel as if I *know* the solutions - I won't change the way that I work... which is essentially to live in the REPL.

In the land of `F#` we're using the F# Interactive as our REPL - embedded in an IDE like Visual Studio, Xamarin Studio, Emacs, Sublime Text etc; or just running it from the command line (`fsi`, or `fsharpi` in the Mono world). I'll either be feeling my way through in a F# script file (`.fsx` files), or any other source file and using whatever *send to interactive/repl* commands I have available - and less frequently, even just directly in the REPL itself.

With this tool it is very easily to discover the solution, building one small function at a time - and then composing them together, or just using them as part of other functions. 

The great thing here, particularly with a functional language like F# - is that working in this way kind of forces you to write code that is both pure and testable:

* Stateless
* Free from side effects
* Values in - values out

An added benefit of a functional codebase is that in order to test your functions - you're not having to jump through hoops building up some kind of objects with *setup* and *teardown* test hooks. Test cases become little more than values and expected values.

The key here is that running code in the REPL is fast. This makes it really cheap.

Once you discover that solution, and you're happy with the results - just cut & paste that code straight into your production source code. Job done.

I don't tend to keep that code in a script file, or even move it to a smaller test elsewhere. If I've [designed my types properly](http://fsharpforfunandprofit.com/category/DDD/) I really don't need to. *But if I did want to keep* those test cases (remember, they're just values) - I'd keep them in a `test.fsx` file in the project, which isn't compiled into the application code anyway - and they're certainly not key for the build/test run to complete properly (which means that they are zero cost to maintain, if you don't want to).

So the REPL really is just a form of test-first development - but one that lets me go faster than building up unit tests.

## 6. Refactoring

There should be some hard fast rules to refactoring:

* Don't over-engineer.
* Refactor away only the pain-points.
* Reduce LOC (maintenance overhead).

Point one there is the most important to me - I've picked up a few codebases in my time where the amount of abstraction far outweighs any benefit to the value of the system, and any perceived extensibility etc. 

Sometimes you should really **Keep it ~~Simple~~ Slayer Stupid**.

Whilst F# currently lacks some really smart tooling for refactoring in fun automated ways (though I am pretty sure some people are some great work in that space) - we again have the REPL!

You'll have your top-level acceptance tests to lean on to ensure no regression - but you can feel your way to a more elegant, less expensive way of expressing your solution in a REPL environment. Again, as you're happy with the results - just replace existing code in the production codebase with the new bits. 

Simple as that.

## 7. Write another test, watch it fail...

Uh-oh! I've broken the process.

Sometimes I've noticed that I often write another test (or rather, another concrete example that leans on a certain requirement) - and it passes first time. Don't worry - this is perfectly normal. I used to distrust it and would do a little stepping through the code in the debugger. But it always proved to be a fruitless waste of time. Logging has replaced that (with good logs being written, you can quickly skim over those). 

So that saves some time, right?

But why did it happen? Probably because of the F# type system. The compiler is a hard task master, and tends to like to make you **think** quite hard about the solution up-front. It doesn't like you leaving *i's* un-dotted, and *t's* un-crossed. You aren't writing statements that just *do things* (like modifying internal state) - you're writing expressions - and the types of those expressions need to all tie up nicely.

The downside is that it normally takes a whole bunch of time to get things compiled - but funnily enough; when they do, they almost always work first time (and stay working).

Hopefully you're not too adversed to *thinking* and **not** taking *shortcuts*...

## F# go-faster stripes

So hopefully I've documented my process well enough to convey my thoughts on it - but just in case; here are some bullet points:

* Functional programming (F#) using a REPL (F# Interactive) lets me drop the dogma.
* Dropping the dogma lets me go faster.
* The type system lets me stop making silly mistakes and helps solve more problems, more quickly.