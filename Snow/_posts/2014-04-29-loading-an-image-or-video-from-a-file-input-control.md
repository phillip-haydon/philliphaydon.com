---
title: Loading an Image or Video from a File Input control.
layout: post
categories: JavaScript, HTML5
series:
	name: bananas
	current: 1
	part: Part 1: Loading an Image or Video from a File Input control
	part: Part 2: Creating a drop area and validating the file
	part: Part 3: Taking snap shots of a Video & displaying it
	part: Part 4: Uploading snap shots to Nancy (Or Web API)
	part: Part 5: Converting video in the browser using videoconverter.js (ffmpeg)
---

So you've got an input control, and you want to display the video/image before the user uploads, maybe so they can verify it first...

The [File API][0] allows you to get more information from a `<input type="file"...` control than we could get before. 

So lets start with Video:

## Video

Ok so we have a basic HTML page with an `<input type="file"...`, `<video...`, and `<input type="button'...`

	<!DOCTYPE html>
	<html data-ng-app="itcompiler">
	<head>
	    <meta charset="utf-8" />
	    <title></title>
	</head>
	<body>		
		<div>
			<input type="file" id="video-input">
			<input type="button" value="Load Selected Video" id="load-video" />
		</div>
	
		<div>
			<video id="video-container" controls></video>
		</div>
	</body>
	</html>

So if we load up a file, our UI should look something like this:

<!--excerpt-->

![][1]  

Now we need to add a click event to the button:

	<script>	
		(function(){
			
			var fileInput = document.getElementById('video-input');
			var video = document.getElementById('video-container');
		
			document.getElementById('load-video').addEventListener('click', function(){
		
				// Hmmm what happens?!?
	
			});
		
		})();	
	</script>

There is currently only 3 video types that most browsers can handle. *There are others but support is far and few, for now atleast*

- `video/mp4` 
- `video/ogg`
- `video/webm`

`video/mp4` is supported on all browsers except Opera. (tho I haven't tested) so I'll only use a `.mp4` file.

## How to load the selected file into a `<video />` element

Ok so we have selected a file, but how do we load it? The W3 Spec has a section on Blob URL for [Creating/Revoking][2], all we need to do is creat a URL for the file blob, we can do this by calling:

	var fileUrl = window.URL.createObjectURL(fileInput.files[0]);

Now we can assign this to the `<video...` element:

	document.getElementById('load-video').addEventListener('click', function(){

		var fileUrl = window.URL.createObjectURL(fileInput.files[0]);

		video.src = fileUrl;
		
	});

Now we can select the file:

![][3]

*Can see the file selected next to the file input*

And if we click on `Load Selected Video` we get:

![][4]  

## What's it doing?!?

What the browser does is creates a fake URL with the blob loaded into it to simulate a URL for the content, if we put a break point on the button click:

![][5]

We can see a URL prefixed with `blob` and a url. So this allows the browser to simulate making a request for the content as if it was being served up normally...

## Images!

So the great thing about this is it works for images too!!! So if we change the HTML now to have an `<img...` tag instead of `<video...` tag:

	<div>
		<input type="file" id="image-input">
		<input type="button" value="Load Selected Image" id="load-image" />
	</div>

	<div>
		<img id="image-container" />
	</div>

And we update the JavaScript (just the wording so its all `image` instead of `video`)

	(function(){
		
		var fileInput = document.getElementById('image-input');
		var image = document.getElementById('image-container');
	
		document.getElementById('load-image').addEventListener('click', function(){
	
			var fileUrl = window.URL.createObjectURL(fileInput.files[0]);
	
			image.src = fileUrl;
	
		});
	
	})();

We can achieve the same thing with images, selecting a file:

![][6]

And we hit load:

![][7]

And if we debug it, just like the video, it creates a URL we can use for the image:

![][8]

That's how easy it is :) Next we will look at a drop area for files.



[0]: http://dev.w3.org/2006/webapi/FileAPI/
[1]: /images/image-video-part-1-01.png
[2]: http://www.w3.org/TR/FileAPI/#creating-revoking
[3]: /images/image-video-part-1-02.png
[4]: /images/image-video-part-1-03.png
[5]: /images/image-video-part-1-04.png
[6]: /images/image-video-part-1-05.png
[7]: /images/image-video-part-1-06.png
[8]: /images/image-video-part-1-07.png