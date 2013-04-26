---
title: Windows Store App - Microsoft Advertising SDK Error Handling
category: Windows 8 App
---

Few months ago I updated my Windows Store App to include ads [Advertising](/2013/02/windows-store-app-adding-advertising), the problem was this took AGES to get accepted into the App Store.

Every single time I submitted it, it would get past everything except Content Check. Each time it would fail on:

 - App Crash
 - Content Age Restriction

The content age restriction I still disagree with since my app displays YouTube content, I'm forced to have a higher age restriction since the videos shown by default are Documentaries which have questionable content. But I digress on Microsoft's retarded Content Reviewing process. 

## The Problem

![](/images/windows-store-ad-error-handling-1.png)

These `AdException` errors get thrown over and over. In fact from 6  failed attempts to submit the app I got a total of 85 exceptions on `No ad available.`

Then every now-n-then I get:

![](/images/windows-store-ad-error-handling-2.png)

This was highly frustrating. Every time this error happened the app crashed. Handling the error I was setting `IsEnabled = false`

	void AdvertErrorOccurred(object sender, Microsoft.Advertising.WinRT.UI.AdErrorEventArgs e)
	{
	    try
	    {
	        Advert.IsEnabled = false;
	
	        new RaygunClient("* my key *").Send(new RaygunMessage
	        {
	            Details = new RaygunMessageDetails
	            {
	                Error = new RaygunErrorMessage(e.Error)
	            }
	        });
	    }
	    catch (Exception)
	    {
	    }
	}

It turns out, setting `IsEnabled` doesn't actually disable the control, `Refresh()` is still called and for some reason this causes the application to crash. To make matters worse, when I pushed my app video into Full Screen, I needed to hide/show the control since the control, despite the z-index, will always sit on top of the video content. 

When the `Visibility` of the control is `Collapsed`, the app continues to run in the background, and continues to throw exceptions over and over.

	void VideoPlayerIsFullScreenChanged(object sender, RoutedPropertyChangedEventArgs<bool> e)
	{
	    var detailView = this.DataContext as DetailViewModel;
	
	    if (e.NewValue)
	    {
	        ...
	        Advert.Visibility = Visibility.Collapsed;
	    }
	    else
	    {
	        ...
	        Advert.Visibility = Visibility.Visible;
	    }
	}

## The Solution

After some back and forth emails with Microsoft, it turns out the correct way to handle the control is to use `.Suspend()` and `.Resume()`

So we update the `ErrorOccurred` event to use `Suspend()`

	void AdvertErrorOccurred(object sender, Microsoft.Advertising.WinRT.UI.AdErrorEventArgs e)
	{
	    try
	    {
	        Advert.Suspend();
	
	        new RaygunClient("* my key *").Send(new RaygunMessage
	        {
	            Details = new RaygunMessageDetails
	            {
	                Error = new RaygunErrorMessage(e.Error)
	            }
	        });
	    }
	    catch (Exception)
	    {
	    }
	}

Calling `.Suspend()` has the following affect:

> Replaces the current view of the ad with a static snapshot of what was being rendered.

[MSDN](http://msdn.microsoft.com/en-us/library/advertising-windows-sdk-api-reference-adcontrol-methods-suspend(v=msads.10).aspx)

We suspend because in the case where an advert cannot be shown for what ever reason, it's most likely that the advert is not going to show the next time. This could be because there's no internet connectivity, so maybe we only want to disable the advert for the duration that the application is active, and try again the next time the user opens the app. In any case this will prevent `Refresh` being called.

When Hiding/Showing the Ad control we want to change the `Visibility`, but we want to prevent exceptions being thrown by the control still attempting to `Refresh` when the control isn't actually visible.

	void VideoPlayerIsFullScreenChanged(object sender, RoutedPropertyChangedEventArgs<bool> e)
	{
	    var detailView = this.DataContext as DetailViewModel;
	
	    if (e.NewValue)
	    {
	        ...
	        Advert.Suspend();
	        Advert.Visibility = Visibility.Collapsed;
	    }
	    else
	    {
			...
	        Advert.Resume();
	        Advert.Visibility = Visibility.Visible;
	    }
	}

Here we call `.Suspend()` followed by setting the `Visibility` to `Collapsed`, and then we reverse the entire process.

This prevents the exceptions being thrown when the control is not visible. 