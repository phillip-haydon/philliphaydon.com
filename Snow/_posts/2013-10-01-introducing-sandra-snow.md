---
title: Introducing Sandra.Snow
categories: NancyFX, Snow
layout: post
---

I've only been blogging since 2009, and my blog had gone through multiple iterations. I began on BlogEngine.NET, moved onto Wordpress, then was introduced to Jekyll. 

I loved Jekyll, but I personally found it fiddly, and people who I've spoken to have also found it fiddly, but are quiet happy after they had set it up.

I really liked the idea, so I thought I would recreate it in .NET :) Because reinventing the wheel is fun!

## Introducing Sandra.Snow

With the help of [@jchannon][0], I've managed release Snow. Using NancyFX we have created a small application that allows you to write Markdown Posts with Razor Layouts, which will be compiled static HTML files.

Currently, besides my own, I'm aware of 3 other blogs running on Snow:

 - [blog.jonathanchannon.com][1]
 - [thecodejunkie.com][2]
 - [blog.owenrumney.co.uk][3]

So what is it? Basically:

> Sandra.Snow is a Jekyll inspired static site generation tool that can be run locally, as a CAAS(Compiler as a Service) or setup with Azure to build your site when your repository changes. It is built on top of NancyFX.

<!--excerpt-->

## Getting Started

The easiest way to getting started with Snow is to fork it!

[Sandra.Snow.SnowTemplate](4)

You can fork the Snow Template repository, which is all setup and ready to go. Basically you fork it, run the `.bat` file, and it will build the website into the current directory, and away you go.

Push the fork back up to `gh-pages` and you've got a site on GitHub pages :)

## So what happening?!?

If you take a look at my blog repository: 

[https://github.com/phillip-haydon/philliphaydon.com][5]

This was originally a Github Pages blog running on Jekyll, I've re-purposed it using the Sandra.Snow.SnowTemplate project.

Basically in the root directory of the website are two 4 key things:

  1. ./.nojekyll
  2. ./CNAME
  3. ./compile.snow.bat
  4. ./Snow

### .nojekyll

This is exactly what you think it is... it basically tells Github to not treat the site as a jekyll site. :) prevents me from receiving emails from Github.

### CNAME

This file is super basic. It contains my blog URL. :)

### compile.snow.bat

This file does two things. 

It executes [OptiPNG][6] which is a little `.exe` file which recompresses images to a smaller size. All my screen grabs are done using <kbd>PrtScn</kbd> + Paint, so the `.png` files are often larger than they should be. 

It may not be much, but my entire site is about 12mb of images, with the optimization its only 8.8mb. Github hosts my blog, but I'm more worried about end users downloading the images ASAP, especially if they are viewing on a mobile device.

The second thing it executes is `Snow.exe`

`.\Snow\_compiler\Snow.exe config=.\Snow\ debug=true`

This line tells it to execute Snow, and the location of the config file. 

### Snow

This directory is my actual un-compiled website. 

[Github Snow Directory][7]

In here is the actual exe under `_compiler` and a snow.config file.

The snow.config file looks like this:

	{
	  "siteUrl": "http://www.philliphaydon.com",
	  "posts": "_posts",
	  "layouts": "_layouts",
	  "output": "../",
	  "urlFormat": "yyyy/MM/slug",
	  "directoryname": "",
	  "copyDirectories": [
	    "images/output => images",
	    "javascripts",
	    "stylesheets",
	    "stuffz"
	  ],
	  "processFiles": [{
	    "file": "index.cshtml",
	    "loop": "Posts"
	  },{
	    "file": "category.cshtml",
	    "loop": "Categories"
	  },{
	    "file": "categories.cshtml => category"
	  },{
	    "file": "archive.cshtml"
	  },{
	    "file": "about.cshtml"
	  },{
	    "file": "feed.xml",
	    "loop": "RSS"
	  }]
	}

It's nothing special, it's just a JSON file. :)

I specify where my posts are, where my layouts are, and where I want to output my site.

This basically just generates all the markdown posts which live in `/Snow/_posts`, then I have some additional files to process.

Jekyll works by processing any file, but I personally don't like that. I opted for being specific and telling it what it should process. 

## Those markdown files!

The markdown files are similar to Jekyll, they use the same styled header like, well this blog post I'm writing right now looks like this:

	---
	title: Introducing Sandra.Snow
	categories: NancyFX, Snow
	layout: post
	---

When I ported my blog to Snow. I updated NONE of my markdown files. :) 

That's really all there is to it. 

The rest is just Markdown files, some Razor views, and a .exe

It generates to the folder below by specifying the output as `../`, but it preserves some folders. i.e it wont delete the .bat file, CNAME, .nojekyll, Snow, or .git/.svn

## Stuff...

So that's it, I have some nifty little features too. I'm slowly detailing all this on the wiki. But the one I like most was inspired by a conversion with [@TheCodeJunkie][8]

It's the ability to specify a Series of posts:

My blog series on setting up Mono on Azure/Ubuntu, has a special header in the markdown file:

	---
	layout: post
	category: Azure
	title: Setting up Mono on nginx
	series:
		name: AzureMono
		current: 3
		part: Part 1 - Setting up the Virtual Machine and nginx
		part: Part 2 - Setting up new Website and Domain on nginx
		part: Part 3 - Setting up Mono on nginx
		part: Part 4 - Setting up a NancyFX website
		part: Part 5 - Setting up a ServiceStack web service
	---

Each post has a 'series' and 3 key properties:

> Name - specifies the unique identifier for the series.

> Current - is the current index of the posts (its starting index is 1, not 0)

> Part - the parts is a collection of all parts that will be in the series.

Future posts will show without links, while past posts will show with the link they relate to.

If you changed a future post name, it will take the current post's part name and use that, while previous post parts will be ignored. When the HTML files are generated they will be updated with the new series. Meaning you never need to go back and update old posts, or if they are far apart you don't have to hunt them down :)

-----

Well I hope you like Snow. So far I've had positive feedback, but if you choose to use it, I would love to hear what you use it for and any feedback, good or bad! Or post an issue on the repository.

  [0]: https://twitter.com/jchannon
  [1]: http://blog.jonathanchannon.com
  [2]: http://thecodejunkie.com/
  [3]: http://blog.owenrumney.co.uk/
  [4]: https://github.com/Sandra/Sandra.Snow.SnowTemplate
  [5]: https://github.com/phillip-haydon/philliphaydon.com
  [6]: http://optipng.sourceforge.net
  [7]: https://github.com/phillip-haydon/philliphaydon.com/tree/gh-pages/Snow
  [8]: https://twitter.com/thecodejunkie

