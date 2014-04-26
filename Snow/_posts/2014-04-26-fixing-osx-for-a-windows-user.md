---
title: Fixing OSX as a Windows User...
layout: post
categories: OSX, Windows
---


I'm slowly in the process of moving away from my huge desktop computer and moving to my good old 15" Mac Book Pro w/ Retina, as my main computer. Running windows in parallels. But moving to OSX is really hard, things that I take for granted in Windows are just hard in OSX, even after 2 years of having this laptop, and having used OSX for a few years when I lived in New Zealand....

Sooo I'm making this blog post, mostly as a reference for the future, on what I've done to make life a little easier. 

### Fixing Home / End keys

Ahhh I know you can use <kbd>cmd</kbd> + <kbd>&rarr;</kbd> and <kbd>cmd</kbd> + <kbd>&larr;</kbd>, but I just prefer the Windows key mapping. 

<!--excerpt-->

I found [this blog post][0] by [Matthew Holt][1] which fixes the problem for me! 

*(Copying without permission from Matthew Holt's blog, just incase it dies one day...)*

Open the Terminal and do this:

	$ cd ~/Library
	$ mkdir KeyBindings
	$ cd KeyBindings
	$ nano DefaultKeyBinding.dict

Put these lines in that file, including the curly braces:

	{
	/* Remap Home / End keys to be correct */
	"\UF729" = "moveToBeginningOfLine:"; /* Home */
	"\UF72B" = "moveToEndOfLine:"; /* End */
	"$\UF729" = "moveToBeginningOfLineAndModifySelection:"; /* Shift + Home */
	"$\UF72B" = "moveToEndOfLineAndModifySelection:"; /* Shift + End */
	"^\UF729" = "moveToBeginningOfDocument:"; /* Ctrl + Home */
	"^\UF72B" = "moveToEndOfDocument:"; /* Ctrl + End */
	"$^\UF729" = "moveToBeginningOfDocumentAndModifySelection:"; /* Shift + Ctrl + Home */
	"$^\UF72B" = "moveToEndOfDocumentAndModifySelection:"; /* Shift + Ctrl + End */
	}

Press Ctrl+O and then Enter to save the file, and Ctrl+X to exit. Restart your computer to have it take full effect.

Cited Sources:

1. <http://evansweb.info/2005/03/24/mac-os-x-and-home-end-keys>
2. <http://soodev.wordpress.com/2011/07/04/mac-os-x-remapping-home-and-end-keys/>


### Fixing Alt Tab...

I have no idea what the person was thinking when they decided that this was a good idea. 

So you can use <kbd>cmd</kbd> + <kbd>tab</kbd> to tab applications... but if you want to tab instances?!? You need to use <kbd>cmd</kbd> + <kbd>`</kbd>... So to fix that... Enter [Witch][2]

![][5]

This thing is great, back to the old <kbd>alt/opt</kbd> + <kbd>tab</kbd> 


### Fixing Spotlight

I don't know how anyone... This is my Spotlight:

![][3]

Besides pretty much always saying 'indexing' when it does actually 'index'... it's really slow, doesn't give me what I'm looking for, and doesn't seem very helpful.

Windows 8.1 Search is actually far superior to Spotlight. 

![][4]

Enter [Alfred][6]... This thing is awesome, from the moment you install it, open it with <kbd>alt/opt</kbd> + <kbd>space</kbd>, and run your first start, it gives you the results you want.

### Fixing maximize windows

I like to use windows Maximized, but when you press the little green `+` for some reason it only expands the window vertically... I have no idea why...

Spaces on OSX are pretty awesome, so if I want to have a couple of programs on 2 or 3 spaces I can easily flick between them with <kbd>ctrl</kbd> + <kbd>&larr;</kbd> or view all with <kbd>ctrl</kbd> + <kbd>&uarr;</kbd>, but when it comes to maximizing a window... while keeping the top tool bar, it seems you can't...

I always get the argument that I should full screen the app, but I don't want to... 

Enter [RightZoom][7], this thing is great, it makes a window take up all the space, both horizontally and vertically. 

### Things I can't fix :(

1. Right click context menu to add new file...
2. Cut/Paste folders easily (apparently there's some command but I never remember it)




[0]: http://mwholt.blogspot.sg/2012/09/fix-home-and-end-keys-on-mac-os-x.html
[1]: https://twitter.com/mholt6
[2]: http://manytricks.com/witch/
[3]: /images/fixing-osx-01.png
[4]: /images/fixing-osx-02.png
[5]: /images/fixing-osx-03.png
[6]: http://www.alfredapp.com
[7]: http://www.blazingtools.com/right_zoom_mac.html
