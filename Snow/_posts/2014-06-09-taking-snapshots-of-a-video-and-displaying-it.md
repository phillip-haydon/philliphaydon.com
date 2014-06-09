---
title: Taking snap shots of a Video & displaying it
layout: post
categories: JavaScript, HTML5
series:
	name: bananas
	current: 3
	part: Part 1: Loading an Image or Video from a File Input control
	part: Part 2: Creating a drop area to drop a file
	part: Part 3: Taking snap shots of a Video & displaying it
	part: Part 4: Uploading snap shots to Nancy (Or Web API)
	part: Part 5: Converting video in the browser using videoconverter.js (ffmpeg)
---

Now we've loaded an image/video, we've captured the drop event, we've displayed it...

But what about taking screen grabs of a video?

To start with I'll go back to the code we used in Part 1, so we have an input, a load button, and a video control:

	<!DOCTYPE html>
	<html>
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

Racing ahead we load that up:

<!--excerpt-->

	<script>	
		(function(){
			
			var fileInput = document.getElementById('video-input');
			var video = document.getElementById('video-container');
		
			document.getElementById('load-video').addEventListener('click', function(){
				var fileUrl = window.URL.createObjectURL(fileInput.files[0]);
		
				video.src = fileUrl;			
			});
		
		})();	
	</script>

Now that we have loaded our video, how do we capture screen grabs?

***Note:** All sizes are halved becaused the test video is too large :)*

## The hidden canvas

The first thing we have to do is get the current data of the current frame being displayed. To do that we need to create a hidden canvas, and then draw onto it. 

So we're gonna add a canvas element to the UI:

	<canvas id="temp-canvas" style="display:none"></canvas>

The canvas doesn't need to display, it's simply there to capture data. Now we need to put the video into it, so lets add a button, lets also add somewhere to display the image once we have it. :

	<input type="button" value="Copy current frame to image below" id="load-image" />

	<br />Image......<br />
	<img id="image-container" />

## The fluff in between...

Now we need to add some fluff when the button to copy the current frame is clicked.

Let's find the controls:

	var image = document.getElementById('image-container');
	var canvas = document.getElementById('temp-canvas');

And add a click event to the new button:

	document.getElementById('load-image').addEventListener('click', function(){
		
	});

## Load that image!

What we need to do now is take the dom video control, as is. *(this means if you're using jQuery to find the element, you need to get the real dom element, not the jQuery wrapped element)* 

This is currently our `video` variable. 

First up we will size our canvas:

	canvas.height = video.videoHeight / 2;
	canvas.width = video.videoWidth / 2;

Then we need to create a context on our canvas. We will create a 2d context like do:

	var context = canvas.getContext("2d");

This gives us a 2d rendering context whih we can use to draw on. We will draw the current video frame. Assuming we have pause the video in the middle somewhere, we can hit the button to capture the current frame by padding the video in like so:

	context.drawImage(video, 0, 0, video.videoWidth, video.videoHeight, 0, 0, canvas.width, canvas.height);

**// NOTE**

For this I used the 9 property `drawImage` function, this is:

	drawImage(source, sourceStartX, sourceStartY, width, height, destinationStartX, destinationStartY, width, height) 

This means, the sourceStart is where you want the first pixel to be captured from, the width/height of the source from the start pixels. 

The destinationStart is where you want to place the image on the canvas and how you want it to stretch.

My sample takes the actual height/width of the source and places it in the smaller canvas.

You can also use

	context.drawImage(video, 0, 0, video.videoWidth, video.videoHeight);

Which will assume the same height/width for source/canvas.

**// END NOTE**
	
Now all that's left is to display it, to do this we call the `toDataURL()` function defined on the canvas. This takes what's currently drawn and shows it in a image element.

	image.src = canvas.toDataURL();		

Why do we do this rather than display the canvas itself? Well this allows you to right click the image and download it. :)

Now we can load a video:

![][0]

And then press the button to capture it!!!!

![][1]

## The final codez

	<div>
		<input type="file" id="video-input">
		<input type="button" value="Load Selected Video" id="load-video" />
	</div>

	<div>
		<br />Video...<br />
		<video id="video-container" controls></video>

		<br />
		<input type="button" value="Copy current frame to image below" id="load-image" />

		<br />Image......<br />
		<img id="image-container" />
	</div>

	<canvas id="temp-canvas" style="display:none"></canvas>
	
	<script>
		(function(){
			
			var fileInput = document.getElementById('video-input');
			var video = document.getElementById('video-container');
			var image = document.getElementById('image-container');
			var canvas = document.getElementById('temp-canvas');
		
			document.getElementById('load-video').addEventListener('click', function(){
				var fileUrl = window.URL.createObjectURL(fileInput.files[0]);
		
				video.src = fileUrl;			
			});
			document.getElementById('load-image').addEventListener('click', function(){
	
				canvas.height = video.videoHeight;
				canvas.width = video.videoWidth;
	
		        var context = canvas.getContext("2d");
	
		        drawImage(source, sourceStartX, sourceStartY, width, height, destinationStartX, destinationStartY, width, height) 
		
				image.src = canvas.toDataURL();			
			});
		
		})();
	
	</script>

## What do we have...

So we've got an file `input` control, it loads a video file into a `video` element, then we scrub the video & hit that button, and bam, we capture what ever the current frame is.




[0]: /images/screen-capture-video-html5-01.png
[1]: /images/screen-capture-video-html5-02.png