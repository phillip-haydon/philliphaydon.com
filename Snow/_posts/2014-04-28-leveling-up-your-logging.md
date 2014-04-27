---
title: Leveling up your logging. (part 2)
categories: Logging
layout: post
author: Pure Krome
series:
	name: Simple Logging
	current: 2
	part: If you're not logging, you're doing it all wrong
	part: Levelling up your Logging	
---

## Part 2: Levelling up your Logging.

Make sure you've read Part 1: [If you're not logging, you're doing it all wrong][0] before you continue.

### Production logging : This.Changes.Everything &trade;

Localhost development is easy. Live/production logging is where This.Changes.Everything. &trade;    
Instead of sending all the logging information to your localhost Sentinal app we now send it to LogEntries.

Why LogEntries (or any other Logging Service as a Service)?

1. You can access this information any time.
2. You local computer cannot be online 24/7
3. When you need to triage a problem, the problem as already occured. Therefore, the problem is already *historical*. As such, you can't suddenly turn 'on' logging. You need to go back into time and look at what has already happened.

<!--excerpt-->

These Logging SAAS products are perfect for this situation. They just keep storing logging data as it streams in. You can just ignore all the data ... until it's time to check on things :)

The general idea is this

Step 1. Create an account with [LogEntries](https://logentries.com/). Create a new *host*. Get the 'token' text key. (It's a guid).

Step 2. Install the log entries nuget package: `install-package SimpleLogging.LogEntries.`

![weeeeee][1]

Step 3. `Add->new->nlog.release.config`    
*Note 1:* Notice how we now have a new `extensions` element? Need-dat.    
*Note 2:* Notice how we are now using a `logentries` target type? Also need-dat.    
	
	<?xml version="1.0" encoding="utf-8" ?>
	<nlog xmlns="http://www.nlog-project.org/schemas/NLog.xsd"
	      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
	      autoReload="true"
	      throwExceptions="true">
	
	    <extensions>
	        <add assembly="LogentriesNLog"/>
	    </extensions>
	
	    <!-- NLog example: https://github.com/nlog/nlog/wiki/Examples -->
	    <targets>
	        <target name="logentries" type="Logentries"
	                debug="false"
	                httpPut="false"
	                ssl="false"
	                layout="${date:format=ddd MMM dd} ${time:format=HH:mm:ss} ${date:format=zzz yyyy} ${logger} : ${LEVEL}, ${message}" />
	    </targets>
	
	    <rules>
	        <logger name="*" minlevel="Trace" appendTo="logentries"/>
	    </rules>
	
	</nlog>

Step 4. Modify `web.release.config`

Change this...

`<nlog configSource="NLog.config" />`

to this...

`<nlog configSource="NLog.release.config" />`

Step 5. Run the website.

Step 6. Now jump over to LogEntries and start a LIVE TAIL of the host... and viola... data should be streaming in.

![OMG IT WORKS!!!!111!!111][2]

Recap.

We've got logging messages sprinkled through our code already. When we're happy, we push our latest code up to our website (ie. `RELEASE` build so the web config transformations do their magic) and now the logging messages are logged to LogEntries instead. So at any time, we can log into LogEntries and see what's happening to our site.

## Bonus Level
There's still more we can do. If you don't want to install Sentinal (or you're in some super fail restrictive environment where you are not allowed to install anything) you can always send any localhost debugging messages to LogEntries. Just create a second LogEntires-host called Debug or whatever and use that host for localhost development.

Alternatively, you can also use Sentinal for production/live because you don't want to have any logging information stored on a 3rd party service. Just make sure you setup your Firewall to Port Forward any nLog data to the computer where Sentinal is running. This is what i *used* to do until I found out about LogEntries.


[0]: /2014/04/if-youre-not-logging-youre-doing-it-all-wrong/
[1]: /images/leveling-up-logging-01.png
[2]: /images/leveling-up-logging-02.png