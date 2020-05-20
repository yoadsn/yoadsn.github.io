---
layout: post
title:  Configure HTTPS with HTTP.sys for self hosted web servers
description: Learn the basics of SSL and HTTPS and understand why and how to configure your web server outside of IIS using HTTP.sys
date:   2015-06-29 08:32:08
categories: SSL windows OWIN Guide
author: Yoad Snapir
tags:
  - SSL
  - HTTPS
  - OWIN
  - Self Host
  - HTTP.sys
  - pfx
  - Windows
comments: true
---

## What's the problem? ##
Setting up a web application to be served on HTTPS using IIS is quite easy. IIS takes care of most of the "ugly" details like managing the certificates (even creating a self signed one) and creating the binding to use it. But what if you are self hosting your web server outside of IIS? In that case, you have to understand a bit more to get things working.

## Long story short ##
If all you want is to quickly get the job done without knowing too much, scroll [down](#recap) for a quick run down of the things you need to do to setup HTTPS for a self hosted web server.

Assumptions:

- You are familiar with certificates and SSL, windows http stack and powershell
- Your are running Windows Server 2012 R2 / Windows 8.1 or a more recent version
- You are running everything (configuration and your processes) as an admin
- You have a valid certificate (Correct DNS Names, expiration date, EKU etc.) available including it's private key.
- You want to bind to all IP addresses on the machine

If any of the assumptions above does not hold for you, reading the long story might be more useful to you.

## Short story long ##

I'm not a security guy but from time to time i am required to perform some SSL related acrobatics to get something done. Let's face it, developers don't like SSL, it only complicates things for them and since its not an everyday activity i find myslef always relearning stuff i already foregot.

![Acrobatics]({{ site.baseurl }}/assets/images/https-self-hosted-windows/acrobatics.png)

To make things worse, over the years stuff change like the tools used, and the stacks i am working with.
This post is my way of trying to reduce the waste. (Including the waste in explaining to others the basics).

Self hosting web stacks (as in, not on IIS) has become a wide spread practice and the comforts of IIS are no longer there to abstract away some of the gore in serving HTTPS. Things like:

- Creating a self signed certificate for development purposes
- Creating an HTTPS binding (IP+port+certificate)

Are done against the OS directly so, more pain.

### Shortest passage on SSL I could write ###

*Disclaimer - This is going to be extremely simplistic, if you know your SSL stuff, don't hurt yourself by reading this part.*

To be able to figure out what at all are we doing, some (very) basic understanding of the SSL concepts is required, So here goes.

SSL is a way of securing communcation between two parties over TCP. It uses certificates to do it's job.

The certificates bind some name to a public key and with some scheme called PKI (Public Key Infrastructure) it allows a party which participates in a communication to both ensure the other party is who it claims it is and participate in a private communication. Usually, only the server presents a certificate and the client is not usually required to do so. (But it does validate the server vertificate)

If the above is too much, just know that servers have a certificate that validates they are who they clame they are. And this certificate must be presented to the client.  
The server can use the ceritificate only if it posseses something that is called *private key* which should be kept securely on the server and not shared with any client. This private key allows the server to decrypt things that were encrypted with the public key.

#### The chain of trust - CA's ###

Certificates represent trust, You trust a certificate a server presented to you because you (or actually your OS) trusts the one who signed that certificate. Thus, a chain of trust. The roots of that chains are called Certificate Authorities (CA's) and they also idnetify themselves with certificates. These certificates are already bundled with your OS and so you trust them. When a certificate is presented to a client, it has to follow the chain of trust up until either one of the signing certificates is already trusted by him or the root is declared as untrusted and so does the entire chain.

When you purchase a certificate for you domain, you pay for one of those CA's to sign the certificate for you (directly or indirectly) and vouch for your identity. They usually require some prrof of your authenticity before signing your certificate. In case of a domain name, you would have to prove you actually own that domain.

### Shortest passage on HTTPS I could write ###

*Disclaimer - This is going to be extremely simplistic, if you know your HTTPS stuff, don't hurt yourself by reading this part.*

HTTPS is just talking HTTP on an SSL connection. Yep, that's all.

Now, when a client tries to open an HTTPS connection to a server, it uses it's DNS name, this name should match the name the certificate contains. This is how the client can trust the connection.

For example, if a client opens up a a connection to `www.lovelydomain.com` the certificate should state that the owner is `www.lovelydomain.com`. (or also `*.lovelydomain.com` in case of a wildcard sub domain certificate)

### A mental view of the windows stack ###

If you are like me, a mental vision of the moving parts is very helpful in understading why we need to do things and against what. This also helps in debugging problems and working around situations where the step-by-step guide is not applicable. I find this invaluable and so here is my mental view of HTTP on windows.

![Mental view of the stack]({{ site.baseurl }}/assets/images/https-self-hosted-windows/stack.png)

Since Windows Server 2003 (XP SP2) the OS provides a low-level HTTP protocol stack to handle HTTP communication for hosted applications (HTTP.sys). IIS uses it, and virtually any application or framework which does not want to implement HTTP on top of raw sockets. This stack also provides SSL support. Working against HTTP.sys is done using the [HTTP Server API][http_server_api].  
The .Net framework contains a thin wrapper around the API of this stack - the HttpListener class.

To handle SSL, HTTP.sys uses certificates from the windows certificate store, hence the extra work we have to do to "let windows know" about the certificates we want to use.  
Also, HTTP.sys does not just allow anyone to listen on anything and a security mechanism is in place to authorize who can listen on what. (more on that [below](#acls))

[http_server_api]: https://msdn.microsoft.com/en-us/library/windows/desktop/aa364510(v=vs.85).aspx

### Certificates and Windows Certificate Stores ###

Certificates are a digital entity, they can be stored in files while moving them around, in memory or in any other digital form. Windows provides the concept of a *certificate store* where certificates live. Many frameworks and applications running on windows use that store as a home for the certificates they use, this includes IIS and HTTP.sys. (And also .net WCF if you care)

Stores are organized into locations (Current User, Local Machine, etc.) and System Stores (MY, Root, Trust, CA) - You can think of them as folders and indeed the abstraction in powershell (Version 3.0 and above) to work with the stores is called `certificates drive` and for example the URI for the MY store at the Local Machine location looks like `cert:\localmachine\my`.

![Certificates MMC Snap-in]({{ site.baseurl }}/assets/images/https-self-hosted-windows/mmc-snapin-certs.png)

Since we are setting up the *server* to serve HTTPS, we need along side the certificate also it's private key. Conveniently some file formats can wrap togehter both certificates and it's private key in one file - such format is **PKCS #12** with the common extension `.pfx`.

Using this file format, it is quite easy to import the certificate along side the private key to the windows certificate store.

Note - There are other file formats like `.pem`,`.crt`,`.cer` which can be used to store certificates. Windows works with `.pfx` and `.p7b` to import and export certificates with the private key in a single file - use `.pfx` if you can. If for some reason your certificates are in a different format, consider converting it using a tool like OpenSSL. I found [this][sslshopper] helpful in the past.

[sslshopper]: https://www.sslshopper.com/ssl-converter.html

#### Importing a .pfx file into the certificate store ####

We import the certificates into the LocalMachine location at the MY store.

There are several ways of doing that, including the windows UI tools or powershell. In Powershell:

{% highlight powershell %}
$certpwd = ConvertTo-SecureString -String "123456" -Force –AsPlainText
Import-PfxCertificate –FilePath C:\temp\livepbt_cert.pfx cert:\localMachine\my -Password $certpwd
{% endhighlight %}

The password is the password protecting the contents of the `.pfx` file.

You can look at the certificate you imported or also import using the windows certificates MMC snap-in as described [here][cert_mmc_snapin]

[cert_mmc_snapin]: https://msdn.microsoft.com/en-us/library/ms788967(v=vs.110).aspx

#### Self-Signed certificates ####

For development purposes, you can skip the purcahse of a certificate from a trusted CA and just sign your own. Of course, it would not be trusted by clients but you canlive with that as long as you control **both** the client and the server.

To create a self signed certificate you can use the [`New-SelfSignedCertificate`][New-SelfSignedCertificate] cmdlet which is available in powershell 3.0 and up:

`New-SelfSignedCertificate -DnsName www.lovelydomain.com -CertStoreLocation Cert:\LocalMachine\My`

This will create a certificate and a private key, and import it into the LocalMachine\My store. Contrast that with the usage of [`Makecert`][makecert] which is another alternative and you will see how much it simplyfies life:

`makecert -r -pe -n "CN=www.lovelydomain.com" -b 01/01/2000 -e 01/01/2036 -eku 1.3.6.1.5.5.7.3.1 -ss my -sr localMachine -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048 -sv cert.pfx`

Another benefit in using `New-SelfSignedCertificate` is the support for SAN - but this is out of scope of this article.

[New-SelfSignedCertificate]: https://technet.microsoft.com/en-us/library/hh848633(v=wps.630).aspx
[makecert]: https://msdn.microsoft.com/en-us/library/bfsktky3(v=vs.110).aspx

### Managing HTTP.sys using Netsh ###

HTTP.sys provides an API for software developers while Windows provides some tools that wrap around that API for easier administration (and monitoring).  
You might have came across the old HttpCfg.exe tool which is obsolete since Windows Vista / Server 2008.

The only tool you need to know today is Netsh which is sort of a swiss-army knife for network related activities on windows. It is an extensible tool with *helpers* for different areas. One such helper is the [*http*][netsh] helper which we use to manage HTTP.sys.

[netsh]: https://msdn.microsoft.com/en-us/library/windows/desktop/cc307236(v=vs.85).aspx

### HTTP.sys SSL bindings ###

SSL bindings are the way an ip is bound to a port using a specific certificate. You can only bind one certificate for each combination of IP and port.

Without diving into too much details, HTTP.sys will need you to tell it where to look up a certificate in terms of the store and location it is at when a conenction is made to an IP+Port combination. Within a store, certificates can be searched in many ways, one of which is their Thumbprint (or hash). HTTP.sys would also need to know that hash to retrieve the certificate.

Finding the hash of a certificate is as easy in powershell as running a `dir` command on the certificates "drive" such as so:

    dir cert:\localmachine\my

The output would look something like this:

    Directory: Microsoft.PowerShell.Security\Certificate::localmachine\my
    
    Thumbprint                                Subject
    ----------                                -------
    89C38E9AD6455F9A8782DD29CE089074C57311ED  CN=*.lovelydomain.com

The hash is the hexadecimal `Thumbprint` value.

To create an SSL binding you can run the following command using powershell:

{% highlight powershell %}
$guid = [guid]::NewGuid()
$certHash = "89C38E9AD6455F9A8782DD29CE089074C57311ED"
$ip = "0.0.0.0" # This means all IP addresses
$port = "443" # the default HTTPS port
"http add sslcert ipport=$($ip):$port certhash=$certHash appid={$guid}" | netsh
{% endhighlight %}

Once done, every connection attempt to that IP+Port combination would be answered using the certificate specified.

**Note:** Certificates must be imported with a private key to be valid for this usage.

### Listening on the HTTPS address ###

Eventually, your web server would have to tell HTTP.sys it wants to listen to HTTP communication on a specific URL path. Every framework has it's own way to configure that and in self hosted .Net application it is done by the HTTPListener class. 
When specifying the URL to listen on make sure you specify `https` as the scheme part and the port the binding is on unless it's 443 which is the default anyway.

### <a name="acls"></a>Securing who can listen - URL ACLs ###

This is a somewhat advanced topic, but suffice to say the process who wishes to listen on a URL must have permission to do so. Controlling this permissions can also be done using netsh and if you done everything right but still cannot reach you application - this is a good place to look for answers.

For example, assuming your self hosted application is running as the `NETWORK SERVICE` identity, you could run this command in powershell to give it the permissions to listen on any HTTPS URL on the server with port 443:

`"http add urlacl url=https://+:443/ user=""NT AUTHORITY\NETWORK SERVICE""" | netsh`

I have been using [this great article][Demystify-http-sys-with-HttpSysManager] to know more and manage those ACLs. You might want to do the same.

[Demystify-http-sys-with-HttpSysManager]: http://www.codeproject.com/Articles/437733/Demystify-http-sys-with-HttpSysManager

## <a name="recap"></a>Quick Recap - Configuration Steps ##

These steps are required to configure an SSL binding for a self hosted OWIN web application on Windows Server 2012.  
It relies on powershell and the fact that the operations are performed as an admin.

### Get a Certificate ###
Get your certificate file (which should include also the private key) on the server. For example, this could be a file with the `.pfx` suffix.

### Import the certificate to the windows certificate store ###
Add the certificate file to the windows Local Machine / MY store. One way of doing that would be using powershell:

{% highlight powershell %}
$certpwd = ConvertTo-SecureString -String "123456" -Force –AsPlainText
Import-PfxCertificate –FilePath C:\temp\livepbt_cert.pfx cert:\localMachine\my -Password $certpwd
{% endhighlight %}

### List the certificates to find out the certificate hash ##
Each certificate has a hash identifying it. This is required for the next step.
From within Powershell run:
`dir cert:\localmachine\my`

The output would look something like this:


    Directory: Microsoft.PowerShell.Security\Certificate::localmachine\my
    
    Thumbprint                                Subject
    ----------                                -------
    89C38E9AD6455F9A8782DD29CE089074C57311ED  CN=*.lovelydomain.com


Copy the `Thumbprint` out, it would be used on the next step.

### Create the https binding in http.sys using netsh ###
Using the following powershell code.

{% highlight powershell %}
$guid = [guid]::NewGuid()
$certHash = "89C38E9AD6455F9A8782DD29CE089074C57311ED"
$ip = "0.0.0.0" # This means all IP addresses
$port = "443" # the default HTTPS port
"http add sslcert ipport=$($ip):$port certhash=$certHash appid={$guid}" | netsh
{% endhighlight %}

A valid output would state that the certificate was added and the binding created:

`SSL Certificate successfully added`

### Setup your web server to serve HTTPS ###
Different web hosting server/frameworks would have different API's to do that. As an example, this is how you would do it for an OWIN self hosted asp.net WEB API application:

{% highlight c# %}
    WebApp.Start<Startup>("https://www.lovelydomain.com");
{% endhighlight %}

Usually, providing the `https` scheme as part of the application base url directs the framework to bind using SSL on the 443 default port.

## What i did not cover ##

- **Wildcard certificates /SAN** - The usage of a certificate to secure any possible subdomain is an economical choice if many subdomains are used, some limitations apply though. I did not cover the usage of these types of certificates.
- **Trusting self signed certificates** - On a client you control, you can force the OS to trust your self signed certificates. I did not cover how to do that and the benefits of this method.
- **Ignoring certificate validation in .Net client side** - You can actually disable the validation and ignore problems with the server certificate - recommended only for debugging purposes and never for production use.
- **Certificate Signing Request (CSR)** - How to do create it and the uage of CSRs.

If you would like me to extend this blog post on these missing parts or any other related subject feel free to leave a comment.

