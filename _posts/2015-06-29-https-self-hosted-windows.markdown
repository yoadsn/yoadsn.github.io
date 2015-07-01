---
layout: post
title:  "Setting up HTTPS for self hosted web servers on windows"
date:   2015-06-29 08:32:08
categories: SSL windows OWIN
author: Yoad Snapir
---

##What's the problem?##
Setting up a web application to be served on HTTPS using IIS is quite easy. IIS takes care of most of the "ugly" details like managing the certificates (even creating a slef signed one) and creating the binding to use it. But what if you are self hosting your web server outside of IIS? In that case, you have to understand a bit more to get things working.

##Long story short##
If all you want is to quickly get the job done without knowing too much, here is a quick run down of the things you need to do to setup HTTPS for a self hosted web server.

Assumptions:
- Your are running Windows Server 2012 R2 / Windows 8.1 or a more recent version
- You are running everything as an admin
- You have a valid certificate (Correct DNS Names, expiration date, EKU etc.) available including it's private key.
- You want to bind to all IP addresses on the machine

If any of the assumptions above does not hold for you, reading to long story might be useful for you.

###Get a Certificate###
Get your certificate file (which should include also the private key) on the server. Usually it would be a file with a `.pfx` suffix.

###Import the certificate to the windows certificate store###
Add the certificate file to the windows certificate store

###List the certificates to find out the certificate hash##
Each certificate has a hash identifying it. This is required for the next step.
From within Powershell run:
`dir cert:\localmachine\my`

The output would look something like this:

{% highlight powershell %}
PS C:\Windows\system32> dir cert:\localmachine\my

    Directory: Microsoft.PowerShell.Security\Certificate::localmachine\my
    
Thumbprint                                Subject                                                         
----------                                -------                                                         
89C38E9AD6455F9A8782DD29CE089074C57311ED  CN=*.lovelydomain.com   
{% endhighlight %}

Copy the `Thumbprint` out.

###Create the http binding in http.sys using netsh###
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