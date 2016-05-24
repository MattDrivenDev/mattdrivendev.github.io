+++
date = "2013-10-10T22:17:34+01:00"
draft = false
title = "Adopting F#, part 3"

+++

Back in January when I blogged [Adopting F#](/2013/01/adopting-fsharp.html) - I didn't really anticipate it being something of a series; but before too long I blogged again [Adopting F#, part II](/2013/01/adopting-fsharp.html). As it goes I think it is interesting to document the journey I've been able to take from `C#` to `F#` as my bread-and-butter language.

Since I started **part II** with a *"...what has changed since..."*, I don't see much of a problem in doing the same again:

* Got married (life part 1).
* Sold a flat. Bought a house. Moved. [This time for real...](2012/10/moving-house-dot-dot-dot.html) (life part 2).
* More F# tools and scripts.
* Fixed **zero bugs** in my [first production service written in F#](/2013/01/adopting-fsharp.html).
* Helped a few colleagues and friends learn some F#.
* Learned a heap more about software development.
* Played around with [F# on Linux with Mono](/2013/07/service-stack-fsharp-mono-fastcgi-nginx.html) etc 
* Spent a couple of nights creating a [clone of Snake with F#](https://github.com/saxonmatt/Fsssss).

<!--more-->

### F# tools and scripts

I thought I'd delve into scripting a little more since I absolutely believe that one of the best features of `F#` is the interative experience - both as a [REPL](http://en.wikipedia.org/wiki/Read%E2%80%93eval%E2%80%93print_loop) while developing code; and as a way of running F# script files (`.fsx`). I also think it is worth giving a bit of a shout out to some of the libraries and tools that I've used both privately and as part of my working life.

I've now lost count of the number of scripts that I have knocked up to do some of the following:

* ETL
* SQL script generation
* Build/Test/Deployment automation
* Command line tools

...using some of the following libraries etc:

* WSDL Type Providers (see: [official](http://msdn.microsoft.com/en-us/library/vstudio/hh362328.aspx), and: [more](/2013/01/fsharp-wsdl-typeprovider-and-untrusted-server-certificates.html))
* JSON, XML and CSV Type Providers (see: [FSharp.Data](http://fsharp.github.io/FSharp.Data/))
* FAKE - F# Make (see: [official](http://fsharp.github.io/FAKE/))
* SQL Generation/CI (see: [tools](https://github.com/saxonmatt/DbProject/tree/tools), makes heavy use of FAKE above)

Basically my go to language for any scripting work is F#.

### Games dev

I played around for a couple of evenings with F# as a lanuage to do some games development, using the XNA framework. Largely inspired by [GarethIW's](https://twitter.com/GarethIW) efforts for [1 Game a Month](http://www.garethpw.co.uk/search/label/1GAM).

So I built a little clone of Snake. XNA was completely overkill for the occasion - but I really want to get into XNA/MonoGame more - so the more exposure I get to it, the better.

One of the more interesting things for me was how the memory allocation would affect performance. Turns out that the performance tools built into Visual Studio 2012 are pretty reasonable and was able to track down some performance issues (or rather, unnecessary memory allocation issues) in the following lines of code: 


    /// Collision detection between the head and tail of the snake.
    let touchingSelf (Snakey (direction, body, eating)) = 
        body.Tail |> List.map(fun part ->
            body.Head.Intersects(part)
        )
        |> List.reduce (||)  
        
    /// Collision detection between the snake and the garden wall.
    let touchingWall (Snakey (direction, body, eating)) wall = 
        wall |> List.map(fun brick ->
            body.Head.Intersects(brick)
        )
        |> List.reduce (||)

[source](https://github.com/saxonmatt/Fsssss/blob/master/src/Fsssss/Snake.fs#L127-L139)

I also talked about my efforts at an [#IWDev](/iwdev/) meet in October:

<script async class="speakerdeck-embed" data-id="218a81300cc501310c903a30d744b908" data-ratio="1.33333333333333" src="//speakerdeck.com/assets/embed.js"></script>

I'm trying to build another game now with it, again something incredibly simple - but struggling to find the time.

### Part 4?

Maybe...