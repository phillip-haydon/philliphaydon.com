---
title: Snow gets Drafts! 
layout: post
categories: Snow
---

A special thanks goes out to [@johanilsson][0] for this new feature!

Snow now ships with Drafts and Privates, this is a really cool feature since it allows you to create drafts, and a page to list all drafts (optional). 

## What are the options

There are three options for a `published` state:

 - draft
 - private
 - true

Draft is basically a finished or rough cut blog post that you will publish soon, it will be compiled to `/drafts` URL and be publicly accessible (either via a known page or the direct URL)

Private is a post that will not be compiled at all, this will stay as markdown, good for when you want to begin writing something but not have it show up online yet.

<!--excerpt-->

True, this is optional, but its basically the same as not supplying the `published` state at all. It will compile your post and make it publicly available. 


## How do I do it?!?

In the header of your markdown file, simply add a new property called `published` and away you go!

	---
	title: Snow gets Drafts! 
	layout: post
	categories: Snow 
	published: draft
	---

## Where does it go?

Your file should be located where all your other posts go. Using this post as an example:

![][1]

As well as the same header as above, with `published` set to draft, I can now compile the site. 

![][2]

We get this new drafts folder. The folder structure is the same as a normal post, so the file name and chosen slug will dictate the URL.

![][3]

Given this information, the URL would be:

	http://www.philliphaydon.com/drafts/2013/11/snow-gets-drafts/

Neat huh?

## What about that viewing page you told me about?!?

To create a viewing page of all drafts, we can create a brand new template in the root of our Snow folder called `drafts.cshtml`

The contents is super simple:

	@inherits Nancy.ViewEngines.Razor.NancyRazorViewBase<Snow.ViewModels.ContentViewModel>
	@using System.Collections.Generic
	@{
	  Layout = "default.cshtml";
	}
	
	<header>
		<div class="head-inner">
			<h1>Drafts</h1>
		</div>
	</header>
	
	<ul class="category-list fancy-container darkishblue">
	@foreach(var draft in Model.Drafts){
	  <li><a href="@draft.Url" title="@draft.Title">@draft.Title</a></li>
	}
	</ul>

This is how I have mine marked up, similar to how my archive pages work. 

Next, add it to the config file.

*This is a snippet of my config*

	},{
		"file": "archive.cshtml"
	},{
		"file": "drafts.cshtml",
		"loop": "Drafts"
	},{
		"file": "about.cshtml"
	},{

And that's it, now we can compile our Snow site.

![][4]
 
Bam we have a new index file, and it has listed out the current drafts.

	<header>
		<div class="head-inner">
			<h1>Drafts</h1>
		</div>
	</header>
	
	<ul class="category-list fancy-container darkishblue">
	  <li><a href="/drafts//2013/11/snow-gets-drafts/" title="Snow gets Drafts!">Snow gets Drafts!</a></li>
	</ul>

That's all there is to it.

## Things to note.

Snow has some Razor helpers for things like Analytics. If you're using the helper:

	@Html.RenderGoogleAnalytics("UA-######-##")

Then Snow will not render any analytically code for the Draft index or compiled Draft Posts. This means Google wont pick it up unless you link to it somewhere. *(considering adding a helper to set no-cache and such in the header)*

Anyway that's it, the new Drafts feature in Snow. Thanks again to [@johanilsson][0] for implementing this for us!



[0]: https://twitter.com/johanilsson
[1]: /images/snow-gets-drafts-01.png
[2]: /images/snow-gets-drafts-02.png
[3]: /images/snow-gets-drafts-03.png
[4]: /images/snow-gets-drafts-04.png