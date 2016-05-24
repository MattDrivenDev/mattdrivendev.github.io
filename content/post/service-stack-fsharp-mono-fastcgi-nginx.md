+++
date = "2013-07-09T22:27:36+01:00"
draft = false
title = "F# Service Stack using Mono and NGinx"

+++

Man am I busy right now! I've a **todo** list a mile long. Mosty self-inflicted, and entirely a life choice for me. I can't (won't, rather) go into it all - most of it has more to do with *life* than cutting code or lifting weights (not that I'm doing much of the latter, I can tell you!).

One thing that I have been doing is messing around with [Service Stack](http://www.servicestack.net/) some more - obviously using [F#](http://fsharp.org/). Very nice. 

There is a [little guide](https://github.com/ServiceStack/ServiceStack/wiki/Run-ServiceStack-in-Fastcgi-hosted-on-nginx) for running Service Stack with `fastcgi`, `mono` hosted on `nginx`. But getting that up and running felt a little dirty. 

So I needed to find some homeless guy to help me out...

<!--more-->

## vagrant up!

I've encountered [Vagrant](http://www.vagrantup.com/) a few times over the course of the past year - and I've toyed with it here and there; but have more recently invested a bit more time with it. Mainly because I'm using it for a personal project and I like using the same toolkit for development, testing (VirtualBox or VMWare) and production ([Vagrant Plugin for Digital Ocean](https://github.com/smdahlen/vagrant-digitalocean) or [Vagrant Plugin for AWS](https://github.com/mitchellh/vagrant-aws)) - and the concept of [Immutable Servers](http://martinfowler.com/bliki/ImmutableServer.html) really appeals to me as well.

I got things up and running with said private project - but have spent a little time tonight extracting a kind of *template* from it for others, as there were a couple of little bits that were tricky to get right.

You can grab the template from Github: [FSharp.ServiceStack.Mono](https://github.com/saxonmatt/FSharp.ServiceStack.Mono) - it could do with a bit more love from a Mono/Xamarin developer; but overall it kind of just works.

The most tricky part was trying to figure out how to use [Puppet](https://puppetlabs.com/) to start `fastcgi-mono-server4` **and** have the correct permissions for the `/tmp/SOCK-ServiceStack` file it was using to talk with nginx. I settled on the following, with some help from my colleague [pipe-devnull](http://pipe-devnull.com/):

	exec { "start-fastcgi-mono-server4":
		logoutput => "on_failure",
		group => 'www-data',
		user => 'www-data',
		command => "/usr/bin/fastcgi-mono-server4 /applications=/:/var/www /filename=/tmp/SOCK-ServiceStack /socket=unix &",
		require => File["vagrant-nginx"],
	}

Also note the `&` on the end of the `command`, to run it as a background task, else it won't release the shell back to vagrant and finish provisioning your server.

## So what did I learn?

That I need to **learn-2-bash** (or whatever) some more - any time I struggled it was due to a lack of knowledge of how to do x, y or z in a Linux environment.

This was the first time that I did any provisioning using Puppet scripts for Vagrant - really great.

There is room for a `vagrant helper` in [FAKE](http://fsharp.github.io/FAKE/) (which I'm trying to find some time for) and a `fastcgi-mono-server4 module` for Puppet.

## Interested?

If you want to have a play, instructions and source are all in the Github repo: [FSharp.ServiceStack.Mono](https://github.com/saxonmatt/FSharp.ServiceStack.Mono).