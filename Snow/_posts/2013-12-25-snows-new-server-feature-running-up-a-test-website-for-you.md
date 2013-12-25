---
title: Snow's new server feature! Runs up a testing website for you.
layout: post
categories: Snow
---

Just now I released Snow v1.4.0 :) with this we have two new features.

1. New `server=true` argument on Snow to run up an Owin testing website.
1. Merged assemblies

##New server argument

When running Snow, you can now pass in an additional argument `server=true` which will start up a self-hosted website which will allow you to test your updates before pushing them.

Given my blog for example, my config looks like so:

	.\Snow\_compiler\Snow.exe config=.\Snow\ debug=true server=true

Now when I run this:

<!--excerpt-->

![][0]

Awesome right?

Special thanks to [Damian Hickey][1] & [David Fowler][2] for this feature.

Damian told me about [Microsoft.Owin.StaticFiles][3], this is an awesome package, basically you can point it to a directory and it will serve up static files. That means for me to setup Snow all I had to write was basically: 

	public void Configuration(IAppBuilder app)
	{
	    var fileSystem = new FileServerOptions
	    {
	        EnableDirectoryBrowsing = true,
	        FileSystem = new PhysicalFileSystem(Path.GetFullPath(Settings.Output))
	    };
	
	    app.UseFileServer(fileSystem);
	}

This created one little problem though :( when running this up it required the command window to be run as Administrator, which isn't very nice.

Luckily David came to the rescue and let me know about [Nowin][4], it's a...

> Fast and scalable Owin Web Server in pure .Net 4.5 (it does not use HttpListener)

Plugging it in was super easy, I simply added some `WebApp` options specifying the `ServerFactory`:

    var options = new StartOptions
    {
        ServerFactory = "Nowin",
        Port = 5498
    };

    using (WebApp.Start<Startup>(options))
    {
		...
    }

BAM, done, no more asking for admin privileges. :)

##New merged assemblies

With a little help from [Simon Cropp][5], I added [Costura.Fody][6]. This awesome little project merges all your assemblies, with NO effort from you at all!

Simply install the package... and build your project!

That's how easy it was... Well almost, there's a little problem with Razor, it requires an assembly to be loaded in the AppDomain and if it's not there it throws a wobbly, I don't quite understand but I decided to ignore that single assembly.

The end result is, Snow now only have 1 files. A `.exe` and a `.dll`

![][7]

Much cleaner than before :)

Snow repository and Snow.Template have been updated with the new awesomeness. 


[0]: /images/snow-testing-website-01.png
[1]: https://twitter.com/randompunter
[2]: https://twitter.com/davidfowl
[3]: https://www.nuget.org/packages/microsoft.owin.staticfiles
[4]: https://github.com/Bobris/Nowin
[5]: https://twitter.com/SimonCropp
[6]: https://www.nuget.org/packages/Costura.Fody
[7]: /images/snow-testing-website-02.png

