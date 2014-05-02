---
title: Managing your Azure Storage account with Zudio
categories: Azure
layout: post
date: 2014-05-03 02:21:42 
---

Messing around with Azure Queues, using the C# library to create a queue, add messages, read them, delete them... all is well and good...

But at some point I wanted to view what I was putting into the queue...

The Azure blog has a recent post listing a few [Azure Storage explorers][0]. I tried a couple of them, but they were so buggy!

Infact, I'm sorry to say... Azure Storage Explorer was the worst. When I attempted to refresh a queue, nothing happened. I had to add a second queue, then swap between each queue in order to review the list. Most of the buttons don't seem to do anything at all.

Then I remember [Mark Rendle][2] tweeted me about his [Zudio][1] site. Funally enough he got a mention on the blog post. I decided to login and check it out again.

[![][4]][1]

<!--excerpt-->

## Using zudio

Assuming you've already setup a Storage Account on Azure, we can login to zudio, where you will be presented with the following:

![][3]

Clicking on the Add a Storage Account button prompts us to add an account: 

![][5]

We can get the Account Name and Location looking at the Storage List in Azure

![][6]

Then the Shared Key can be found by clicking the Manage Access Keys link in the same page as the Store Accounts list:

![][7]

Selecting one of the Access Keys and pasting it into the Shared Key in Zudio.

![][8]

Once we have added the account we should have the account name appear in the list on Zudio

![][9]

Click the account name and we should get a list of options, we can choose what we want to manage, Blobs, Queues, or Tables. Lets pick Queues.

![][10]

We should get a list of queues, my list is empty since I haven't added any yet...

![][11]

We could manage the queues from this screen using the available options:

![][12]

But lets add some code :)

## Codez - because we need some :)

In a basic console app, I've added the [WindowsAzure.Storage][13] nuget, and written some simple code:

    var storageConnection = @"DefaultEndpointsProtocol=https;AccountName=phzudio;AccountKey=*REMOVED*";
    var storageAccount = CloudStorageAccount.Parse(storageConnection);
	
	var queueClient = storageAccount.CreateCloudQueueClient();
	var queue = queueClient.GetQueueReference("my-first-queue");
	
	queue.CreateIfNotExists();
	
	queue.AddMessage(new CloudQueueMessage("Hello, World"));

That's it... simple simple code. The account key removed in the `storageConnection` is the same key we used to connect to Zudio.

Now when we run the code, and jump back to Zudio, hit refresh...

![][14]

We get our newly created queue from code.

Clicking on the queue shows us our message

![][15]

From here we can manage messages 

![][16]

Adding new messages, refreshing, dequeuing, etc.

## Summary

Zudio is super simple to use and pretty damn powerful for managing Azure Storage, Mark has done an awesome job and I look forward to using Zudio in the future. 

So far I'm only used it for queues, and clicked around on the tables/blobs, but its so easy to use and intuitive! 

I would highly recommend checking out Zudio!

<http://zud.io>


[0]: http://blogs.msdn.com/b/windowsazurestorage/archive/2014/03/11/windows-azure-storage-explorers-2014.aspx
[1]: http://zud.io/
[2]: http://twitter.com/markrendle
[3]: /images/zudio-post-01.png
[4]: /images/zudio-post-02.png
[5]: /images/zudio-post-03.png
[6]: /images/zudio-post-04.png
[7]: /images/zudio-post-05.png
[8]: /images/zudio-post-06.png
[9]: /images/zudio-post-07.png
[10]: /images/zudio-post-08.png
[11]: /images/zudio-post-09.png
[12]: /images/zudio-post-10.png
[13]: http://www.nuget.org/packages/WindowsAzure.Storage/
[14]: /images/zudio-post-11.png
[15]: /images/zudio-post-12.png
[16]: /images/zudio-post-13.png