---
layout: post
title: Permament redirect to HTTPS with IIS
category: IIS, Nancy, HTTPS
---

Google has just recently updated their search results to give higher ranking to sites with an SSL Certificate, than to sites without, which is one of the best changes Google has made in recent years. There really is no excuse for not having a cert now. *(note, this is limited to small portion of sites but lets assume that this will be rolled out if Google proves it to be worth while)*

[googleonlinesecurity - https-as-ranking-signal_6.html][1]

Unfortuntely for me it seems Github Pages does not support Certificates on custom domain names, yet... :( hopefully they will support this eventually so that I can avoid moving my blog.

So one thing that pops up in the Nancy channel on JabbR every-now-n-then, is how to make all modules require SSL. 

From within a module you can call:
	
	this.RequiresHttps()

And this will push the site into `https`, but having to write this everywhere is rather annoying, and during development on your local machine this is a bit of a hassle. Despite Visual Studio supporting an HTTPS url...

![][0]

If you didn't know this existed, you should. Basically in Visual Studio, select your Web Application project, then open up the properties window (don't right click and select properties, you need to open the window manually) in the window, set `SSL Enabled` to true, this will add a SSL URL, then you can run the site and use that url instead of the non-https url.

-----

So during development you may want to develop without a certificate, but want your production site to be always using a certificate, this makes the `RequiresHttps()` call a little annoying, we end up with conditional logic or something similar to figure out are we in development, or are we in production.

## URL Rewite Extension for IIS

Download and install the [URL Rewrite extension][2] for IIS. Once installed we can do lots of fancy stuff for URL Rewriting, but we only want to handle the HTTPS redirect for this post.

Now that it's installed, we can configure it one of two ways. If we use the GUI then we still need to copy the contents generated in the Web.config file and put that into a build script.

Using the GUI, we can open up the URL Rewrite extension:

![][3]

Using the GUI we can setup a new `Blank rule` in the `Inbound rules` section:

![][4]

Next we want to fill in the rule. 

For the Match URL, we want to use a super duper simple regular expression.

`.*`

This basically says, match any character, as many times as you want.

![][5]

For the Conditions, we want to check the Input `{HTTPS}` match is off, if we have it on then we will end up in an infinate loop. eek

![][6]

We don't care about Server Variables so skip that.

And lastly is the action we want to take when the rule matches (that it's not an HTTPS request)

We want to Redirect to:

`https://{HTTP_HOST}/{R:0}`

Tick append query string, and for the redirect type, select Permanment. 

`{HTTP_HOST}` is the host name, so if your domain is `bananasareyellow.com` then that's the value that will be put in the redirect url. You can hard code this value if you want, but if you want to support multiple host names then you will want to use the variable. 

![][7]

After applying this new rule, when you visit your website with an HTTP url, it will be pushed into HTTPS. 

## Ahhh I've lost it!

All this information gets stored in your web.config file, and it looks like this:

    <system.webServer>
        <rewrite>
            <rules>
                <rule name="HTTP to HTTPS redirect" stopProcessing="true">
                    <match url=".*" />
                    <conditions>
                        <add input="{HTTPS}" pattern="OFF" />
                    </conditions>
                    <action type="Redirect" url="https://{HTTP_HOST}/{R:0}" redirectType="Permanent" />
                </rule>
            </rules>
        </rewrite>
    </system.webServer>

SO! 

Once you've written up your condition, you may want to roll this into your build script so that during development, that way when it's deployed it will always contain the rule and you wont lose it. 

You can also add a condition in here to say the rule does not apply when the site is a localhost or test domain, but in my opinion you shouldn't add test-checks in production code.



 [0]: /images/https-iis-vs-01.png
 [1]: http://googleonlinesecurity.blogspot.co.uk/2014/08/https-as-ranking-signal_6.html
 [2]: http://www.iis.net/downloads/microsoft/url-rewrite
 [3]: /images/https-iis-vs-02.png
 [4]: /images/https-iis-vs-03.png
 [5]: /images/https-iis-vs-04.png
 [6]: /images/https-iis-vs-05.png
 [7]: /images/https-iis-vs-06.png