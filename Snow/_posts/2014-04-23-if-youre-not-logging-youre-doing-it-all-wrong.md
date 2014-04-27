---
title: If you're not logging, you're doing it all wrong. (part 1)
categories: Logging, NancyFX
layout: post
author: Pure Krome
series:
	name: Simple Logging
	current: 1
	part: If you're not logging, you're doing it all wrong.
	part: Levelling up your Logging	
---


## Part 1: If you're not logging, you're doing it all wrong.

![Really? Seriously?][0]

### Synopsis: Simple way to start logging your .NET application.

Maintaining some production software is most overlooked until a problem arises ... which by then it could be more costly to support and fix if some simple, basic procedures were not considered from day zero. In my opionion, adding the ability to get acess to the internal runnings of your system is crutial to *seeing what is going on under the hood* and helping you get some facts to help problem solve a production issue.

Logging is one of these mechanisms and should be considered and planned before you even start any coding.

<!--excerpt-->
    
What this is not about:    

1.  Not an opinion piece about why you should be logging your code. Google for that, then come back when you're convinced that you need it. 
2. This is not a lesson in Dependency Injection. Don't know that? Google it then come back.
3. Nor is this a lesson in [NLog](http://nlog-project.org/), [AutoFac](http://autofac.org/) or [NancyFX](http://nancyfx.org/) / [ASP.NET MVC](http://www.asp.net/mvc) (sample tools we use in this example)
4. Nor will I explain how to setup an account and 'hosts' with LogEntries (what the hell is LogEntries? I'll explain that later).
5. Nor will I explain what/how are web.config transformations.


### If you're not logging, then you're doing it all wrong.

You build websites or mobile/desktop applications. Awesome. Therefore, you need to know what is happening *during* runtime of your live/production application, otherwise you're flying blind. 

The trick to logging a .NET application is to make sure that you leverage a logging interface all over your code. Based upon your solution's configuration (ie. `DEBUG` or `RELEASE` or `ANY_OTHER_CUSTOM_CONFIG`) we leverage `web.config` *transformations* to use the appropriate logging *targets*, based on the configuration.

Note: `Target` = a fancy word for where we dump our logging information to. This could be the console or a file or another gui-application (more on this, later) or some internet website (also more on this magic, later).

### Tools - we need tools!

Then general idea is this

1. Code your message(s): "Hi, I'm here."  or "OMG, BOOM" or "Just created a new user. UserId: '1'.".
2. NLog sends the message somewhere: your GUI app, or email, or a logging website.
3. View the messages: use Sentinal (localhost) or LogEntries (production).

And where do we visualize this data again?

- [Sentinal](http://sentinel.codeplex.com/): A free windows app that displays log information, as it streams in *live*. (yes, ~~streams~~ in …)
- [LogEntires](https://logentries.com/): A website that *stores* your log entry information, which you can view it at any time. Also has a live stream.     
**Free account limits data retention to a week .. which for most people (especially when you're testing/debuging) is *totally* sufficient.**

Okay so when to use what?    

- Sentinal: use this for localhost debugging.
- LogEntires: use this for live/production system.

Pro Tip: Yes, you can use Sentinal for live/production if you really want to. But this involves setting up NAT rules in your firewall, opening ports on your local firewall, etc. Basically - a PITA vs using LogEntries which is for free.

### Show me some code, already! Sheesss…

Fine. Let's do this: LEEEEROOYYYYY JENKINNNNNSSSSSSS……

Step 1. Download & install [Sentinal](http://sentinel.codeplex.com/).

Step 2. Create/Setup a new solution.    
    eg. `File -> New -> Web Application.`

Step 3. Lets add some logging information.

First, we need an existing logging interface package.

![Nuget command line][1]

Next we'll use DI/IOC to inject the logging instance, so lets wire that up first.

NancyFX:    
(This is using TinyIOC for IoC (which is built into NancyFX))


	protected override void ConfigureApplicationContainer(TinyIoCContainer container)
	{
	    base.ConfigureApplicationContainer(container);
	
	    // NOTE: Use a specific constructor, which is why we have to use the delayed registration.
	    var loggingService = new NLogLoggingService();
	    container.Register<ILoggingService>((c, p) => loggingService);
	
	    // Register any other singleton services.
	    // …
	}


ASP.Net MVC    
(This is using [AutoFac](http://autofac.org/) for IoC)    

	public static class DependencyResolutionConfig
	{
	    public static void RegisterContainers()
	    {
	        var builder = new ContainerBuilder();
	
	        // Register our services.
	        builder.Register(c => new NLogLoggingService())
	            .As<ILoggingService>();
	
	        // Register our controllers (so they will use constructor injection)
	        builder.RegisterControllers(typeof(MvcApplication).Assembly);
	
	        var container = builder.Build();
	
	        DependencyResolver.SetResolver(new AutofacDependencyResolver(container));
	    }
	}


Now we'll use constructor injection for our logging interface.    
**Side Note**: I do not consider injecting an `ILoggingService` into classes as an [Anti-Pattern](http://jeffreypalermo.com/blog/constructor-over-injection-anti-pattern/) because IMO I usually log things in *all* methods (I'll touch on this, below) so therefore, logging is a fundamental part of each class that contains business logic. As such, my class has a logging dependecy throughout. Not on 1 or some methods, only.

NancyFX

	using System;
	using Nancy;
	using Shouldly;
	using SimpleLogging.Core;
	
	namespace SimpleLogging.Samples.NancyFX.Modules
	{
	    public class HomeModule : NancyModule
	    {
	        private readonly ILoggingService _loggingService;
	
	        public HomeModule(ILoggingService loggingService)
	        {
	            loggingService.ShouldNotBe(null);
	            _loggingService = loggingService;
	
	            Get["/"] = _ => GetHome();
	        }
	
	        private dynamic GetHome()
	        {
	            _loggingService.Trace("GetHome");
	
	            _loggingService.Debug("Current DateTime: '{0}'", DateTime.UtcNow);
	
	            return View["home"];
	        }
	    }
	}

ASP.NET MVC

	using System;
	using System.Web.Mvc;
	using Shouldly;
	using SimpleLogging.Core;
	
	namespace SimpleLogging.Samples.MVC.Controllers
	{
	    public class HomeController : Controller
	    {
	        private readonly ILoggingService _loggingService;
	
	        public HomeController(ILoggingService loggingService)
	        {
	            loggingService.ShouldNotBe(null);
	            _loggingService = loggingService;
	        }
	
	        //
	        // GET: /Home/
	
	        public ActionResult Index()
	        {
	            _loggingService.Trace("Index");
	
	            _loggingService.Debug("Current DateTime: '{0}'", DateTime.UtcNow);
	
	            return View();
	        }
	    }
	}

Step 4. Add an NLog file.

Add this new file to the root website / application folder.

	
	nlog.config    
	
	<?xml version="1.0" encoding="utf-8" ?>
	<nlog xmlns="http://www.nlog-project.org/schemas/NLog.xsd"
	      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	      autoReload="true"
	      throwExceptions="true">
	
	    <!-- NLog example: https://github.com/nlog/nlog/wiki/Examples -->
	    <targets async="true">
	        <target xsi:type="NLogViewer"
	                name="sentinal" 
	                address="udp://127.0.0.1:9999" />
	    </targets>
	
	    <rules>
	        <logger name="*" minlevel="Trace" writeTo="sentinal"/>
	    </rules>
	    
	</nlog>

Step 5. Run Sentinal

Step 6. Run the website.

![Zoh Mai Gawd][2]

### Recap.
So now we've started sprinkling logging messages throughout our code. This gives us some Serious.KickAss&trade; insights into what is going on under the hood with our code. We view this logging data locally with our Sentinel app. 

It's not hard to add logging to your website / application. 

Please start to get into the habbit of `TRACE`ing and `DEBUG`ing your code so you can see what's going on under the hood - when you really need to.

### NEXT: Part 2 - Levelling up your Logging

In the next part, I take all this simple logging magic to the next level : doing this for your live / production website / application!


[0]: /images/simple-logging-01.png
[1]: /images/simple-logging-02.png
[2]: /images/simple-logging-03.png