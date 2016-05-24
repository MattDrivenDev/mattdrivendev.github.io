+++
date = "2013-07-26T22:22:22+01:00"
draft = false
title = "Continuous Deployment with FAKE and Octopus Deploy"

+++

It's a nice day today, and I've just sat outside for a short break while the other guys ate their lunch (I don't eat during the day, mostly). It struck me that I've not been blogging much of late, and that I've managed to use my lunch times to get some content out on the web before; so I'm back at my desk and hammering `rake new_post` again.

So in recent-ish times I've been evaluating [Octpus Deploy](http://octopusdeploy.com/):

<img alt="Octopus deploy dashboard" width="527" height="219" src="/images/octopus-dashboard-big.png" />

If you're in the business deploying web/service based .NET systems to Windows environments - do yourself a favour and take a look; I think it is a really sound product.

<!--more-->

## Octopus Tools

One of the great things about the product, is the RESTful API that is exposed that allows you to control software releases and deployments remotely. There is a toolset out of the box for doing so: **Octopus Tools** (available on [GitHub](https://github.com/OctopusDeploy/Octopus-Tools) and [NuGet](http://www.nuget.org/packages/OctopusTools/)) - all from the command line.

This has been great and I've been using this with various `.cmd` scripts to automate release and deployment from [TeamCity](http://www.jetbrains.com/teamcity/) build steps.

## but...

1. I don't like having too many build steps set-up in my CI server. I like a CI server to be the system of build automation (scheduling, permissions, selecting the correct source code etc). But I prefer to have the process by which it all happens to be in 1 single script file, that can be properly versioned etc - and could be repeatable by a human on another machine if the CI server were to go **pop!** one day... of if we want to move to another CI system.
2. I wanted to do some automated testing against deployed applications and services - as they are hosted; in a *production-like environment*, where systems are connected to one another and we can get a feeling of how healthy a system is *as a whole*. **BUT** I didn't want my build script to finish, use a separate process to deploy, and then jump back into a second script to run more tests. I want it in one script, fired off in one process (parametized, of course for granular control).
3. I use [F#](http://fsharp.org/) and [FAKE](http://fsharp.github.io/FAKE/) for doing such things, and saw no reason why this should escape too!

## ...and so

A couple of hours later I was able to open a pull request back to the FAKE repo on GitHub, and am able to add a few more `Target`'s to my build scripts:

	Target "Clean" (fun _ ->
	    // Clean build output folders.
	)

	Target "BuildService" (fun _ ->
	    // Build the web service from source.
	)

	Target "BuildUnitTestSuite" (fun _ ->
	    // Build the unit test suite from source.
	)

	Target "RunUnitTestSuite" (fun _ ->
	    // Run the Unit Test suite using your favourite test runner.
	    // These tests directly interact with the production code.
	)

	Target "NuGetPackageAndPublish" (fun _ ->
	    // Package and Publish the web service with NuGet to some kind of NuGet
	    // repository somewhere.
	)

	Target "ReleaseAndDeployToTestEnv" (fun _ ->
	    
	    let release = { releaseOptions with Project = "My Web Service" }
	    let deploy  = { deployOptions with DeployTo = "TestEnv" }    

	    Octo (fun octoParams -> 
	        { octoParams with
	                     ToolPath = "./packages/octopustools";
	                     Server = { Server = "http://octopus.com/api"; ApiKey = "YOUR-CI-API-KEY-HERE" };
	                     Command = CreateRelease (release, Some deploy); }
	    ) 

	)

	Target "BuildIntegrationTestSuite" (fun _ ->
	    // Build the integration test suite from source.
	)

	Target "RunIntegrationTestSuite" (fun _ ->
	    // Run the Integration Test suite using your favourite test runner.
	    // These tests talk the the system as a whole, interacting with the service
	    // in the "TestEnv".
	)

	Target "ReleaseAndDeployToUatEnv" (fun _ ->
	    
	    let deploy  = { deployOptions with Project = "My Web Service"; DeployTo = "UatEnv" }    

	    Octo (fun octoParams -> 
	        { octoParams with
	                     ToolPath = "./packages/octopustools";
	                     Server = { Server = "http://octopus.com/api"; ApiKey = "YOUR-CI-API-KEY-HERE" };
	                     Command = DeployRelease deploy; }
	    ) 

	)

	Target "DeveloperBuild" (fun _ ->
	    // Empty target for a developer build
	)

	Target "CIBuild" (fun _ ->
	    // Empty target for a CI build
	)

	"Clean"
	    ==> "BuildService"
	    ==> "BuildUnitTestSuite"
	    ==> "RunUnitTestSuite"
	    ==> "DeveloperBuild"
	    ==> "NuGetPackageAndPublish"
	    ==> "ReleaseAndDeployToTestEnv"
	    ==> "BuildIntegrationTestSuite"
	    ==> "RunIntegrationTestSuite"
	    ==> "CIBuild"

	RunParameterTargetOrDefault "target" "DeveloperBuild"

## Give it a go

You'll need [FAKE version 1.74.214+](http://www.nuget.org/packages/FAKE/1.74.215.0) (not sure if it is included in the version 2 alphas - probably not) to make use of the `OctoTools` helper module.

## Thanks

Thanks really goes to [sforkmann](https://twitter.com/sforkmann) for maintaining FAKE so well - it is super easy to contribute to the project; and the deployment pipeline to NuGet is seamless and fast. I pulled the latest build from NuGet less than 10 minutes after opening the pull request. 

Awesome.