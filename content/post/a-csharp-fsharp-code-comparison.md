+++
date = "2013-04-03T22:11:15+01:00"
draft = false
title = "Code Comparison: C# v.s. F#"

+++

Since I found using yesterday's lunchtime to write a blog post so good - I'm following that up with another lunchtime blog post.

Yesterday I continued what I think will slowly become something of an anecdotal series on adopting `F#`, in which I briefly talked about a new service I'd worked on using F# (see [Adopting F#, Part II](/2013/04/adopting-fsharp-part-ii.html)).

Inspired by ['Does the language you choose make a difference?' by simontcousins](http://www.simontylercousins.net/journal/2013/2/22/does-the-language-you-choose-make-a-difference.html) I'm throwing up a comparison of lines of code.

<!--more-->

### First with some charts:

<img alt="CSharp Chart" width="684" height="662" src="/images/code.comparison.csharp.jpg" />

<img alt="FSharp Chart" width="684" height="662" src="/images/code.comparison.fsharp.jpg" />

### Now for some numbers:

<table>
	<thead>
		<td><strong>Metric</strong></td>
		<td><strong>C#</strong></td>
		<td><strong>F#</strong></td>
		<td><strong>The F# difference</strong></td>
	</thead>
	<tr>
		<td>Useful lines</td>
		<td>6548</td>
		<td>1004</td>
		<td>6x</td>
	</tr>
	<tr>
		<td>Comment lines</td>
		<td>3496</td>
		<td>7</td>
		<td>350x</td>
	</tr>
	<tr>
		<td>Null check lines</td>
		<td>137</td>
		<td>4</td>
		<td>34x</td>
	</tr>
	<tr>
		<td>Blank lines</td>
		<td>2283</td>
		<td>182</td>
		<td>12x</td>
	</tr>
	<tr>
		<td>Brace lines</td>
		<td>3066</td>
		<td>0</td>
		<td>n/a</td>
	</tr>
	<tr>
		<td><strong>TOTALS:</strong></td>
		<td><strong>15530</strong></td>
		<td><strong>1197</strong></td>
		<td><strong>12x</strong></td>
	</tr>
</table>

(Unlike Simon's post I'm not comparing team size, bugs and time taken - there are way too many process/people/priority changes involved to paint an accurate picture.)

### What does it all mean?

Well you should probably take this comparison with a pinch of salt. Code comparisons are normally laced with some bias.

But there are a few key points that you should note:

* This is a fairly small service.
* The `C#` code base **should** be larger because that is where all the XSD generated classes live, and there is some additional code for configuration etc. BUT! I think even if you half those `C#` numbers above, and assume that the ratios (the chart) stay the same - then the comparision is still rather stark.
* The `F#` code does have some extra weight to make interop with the calling `C#` code easier - to the tally of maybe ~100 lines.
* Both code bases essentially deliver 3 business requirements/behaviours (which are obviously broken down into lots of smaller requirements).

It is fair to say that so far as developing solutions is concerned, `C#` will work *just fine* and there is nothing you cannot deliver in `F#` that you cannot do in `C#` (or Assembler for that matter!). The key here is really that `F#` is just another language - a tool for delivering value to somebody (in this case a business); and I think that this rather anecdotal comparison helps show that `F#` can be a more efficient tool available for the job.

Thanks to *Kit Eason* for his cool [Count noise-lines script](http://fssnip.net/h4)!

If you're interested in learning more about `F#` I urge you to take a look at [The F# Software Foundation](http://fsharp.org/) and [F# for Fun and Profit](http://fsharpforfunandprofit.com/).