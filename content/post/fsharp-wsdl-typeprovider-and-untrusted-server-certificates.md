+++
date = "2013-04-22T22:25:46+01:00"
draft = false
title = "F# WSDL Type Provider and untrusted SSL certificates"

+++

I came across something of a pain while using the Microsoft Wsdl Type Provider (`Microsoft.FSharp.Data.TypeProviders.WsdlService`) at work yesterday. 

The problem that I was facing was that the Wsdl Type Provider just would not play ball with one of our web services. The reason as it turns out was the fact that it used `https` and the certification isn't properly signed.

So when working with the Type Provider in code - using the default method of just pointing at the service's Wsdl; just doesn't do the trick. Documentation for a fix was a little thin on the ground - and it wasn't until I did a `#fsharp` shout out on Twitter that I got some clues.

<!--more-->

Thankfully [Sergey Tihon](https://twitter.com/sergey_tihon) responded to my call for help, and pointed me to one of his [blog posts](http://sergeytihon.wordpress.com/2013/01/07/f-and-fast-search-for-sharepoint-2010/). 

In particular, his use of the `BasicHttpBinding` which alongside a different `EndpointAddress`, gets passed into the Service client was most useful. Couple that with setting the `ServerCertificateValidationCallback` on the `System.Net.ServicePointManager`. 

But despite making these changes I still couldn't get the Wsdl Type Provider to provide me anything in *design time* (i.e. script file in VS212). Hovering over the red squiggly line under the `WsdlService` type declaration told me it still couldn't access the service. 

Sadly there is no way (to my knowledge) to tell the Type Provider (as it runs in the VS2012 runtime) to do any of this stuff. 

The answer involves a little more manual work than I should like - but hey, that's fine. At least the resulting code base isn't littered with generated code etc.

1. Use your browser to go and grab the Wsdl from the web service - using whatever credentials you need to and accepting the untrusted server certificate.
2. Save that Wsdl into a file you can access from your F# app/script, it'll need a `.wsdlschema` file extension (e.g. `myservice.wsdlschema`).
3. Declare your Type Provider service type in F# using the service url, and the additional optional parameters `LocalSchemaFile` (pointing at your `.wsdlschema` file) and `ForceUpdate` set to `false` (defaults to `true`).
4. Then when using the new service type, we can alter the bindings etc, set up authentication/credentials and force the untrusted server certificate to be accepted.

Here's an example of where I've ended up:

    #r "System.ServiceModel"
    #r "System.Runtime.Serialization"
    #r "FSharp.Data.TypeProviders"
    
	open System.Net
    open System.Net.Security
    open System.ServiceModel
    open Microsoft.FSharp.Data.TypeProviders
    
    ServicePointManager.ServerCertificateValidationCallback <- new RemoteCertificateValidationCallback(fun _ _ _ _ -> true)
    
    [<Literal>]
    let ServiceUrl = "https://our.domain.org/web.application/MyService.asmx"
    [<Literal>]
    let SchemaFile = "MyService.wsdlschema"
    
    // Note the LocalSchemaFile and ForceUpdate parameters
    type MyService = WsdlService<ServiceUrl, LocalSchemaFile=SchemaFile, ForceUpdate=false>
    
    let binding = 
        let mutable b = new BasicHttpBinding()
        b.MaxReceivedMessageSize <- 10000000L
        b.Security.Mode <- BasicHttpSecurityMode.Transport
        b.Security.Transport.ClientCredentialType <- HttpClientCredentialType.Ntlm    
        b
    
    let serviceClient = 
        let mutable s = new MyService.ServiceTypes.MyServiceClient(binding, EndpointAddress(ServiceUrl))
        s.ClientCredentials.Windows.ClientCredential <- (Net.NetworkCredential("UserName", "Password"))
        s.ClientCredentials.Windows.AllowedImpersonationLevel <- System.Security.Principal.TokenImpersonationLevel.Delegation
        s
    
    let request = 
        let mutable r = MyService.ServiceTypes.MyServiceMethodRequest()
        r.Parameter1 <- "Some value"
        r
    
    let response = serviceClient.MyServiceMethod(request)
    
    let myQuery = query { for x in response.Items do
                          where (x.Name.Contains("Matt"))
                          sortBy x.Name
                          select x }
    
    myQuery
    |> Seq.iter (fun x -> printfn "%s" x.Name)

The result? Not *quite* as simple as it said on the tin... but you know what? Its sill 100 times better than the non-TypeProvider route.