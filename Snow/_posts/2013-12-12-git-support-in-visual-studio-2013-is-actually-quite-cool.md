---
layout: post
category: Visual Studio
title: Git support in Visual Studio 2013... is actually quite cool!
---

Lately I've found myself using the new Git support in Visual Studio 2013 more and more, it turns out once you get used to it, its quite handy! Initially my thoughts were its rubbish, mostly because I found it cumbersome, or maybe just that things weren't working the way I thought they were. I do fine some things strange, but it's still completely usable!

How does it all work?

Let's start by creating a new project:

![][0]

I ticked the `Create new Git repository` option in the bottom right hand corner, this just initializes the folder for you. 

Now that the project is created, we can open the Team Explorer, it should look something like this:

![][1]

*I'm not entirely sure why it does this but when I create new projects, the solution file is never included by default?!?*

So the solution file is excluded, we can right click the file(s) and include them...

<!--excerpt-->

![][2]

Now this gets included with the pend... Included Changes. 

![][3]

If we want to exclude stuff, we can right click in the Included Changes:

![][4]

This is very handy, I was working on a project earlier where I couldn't be bothered typing the login credentials in every time. So I hard coded them in the View and excluded the file so it wouldn't get picked up in the Included Changes. Was nice being able to quickly Right Click > Exclude

Now we can add a comment and commit:

![][5]

The commit is created locally:

![][6]

Now we need to push it, if we click on `Unsynced Commits` we currently get a screen asking us to push to a Remote Repository.

![][7]

If we enter a URL and try to push:

![][10]

My remote repository doesn't exist yet :( so creating it on GitHub:

![][11]

Now... where to push from?!? HAH, this is where I got confused. If you try to push before your repository exists, it sort of breaks the workflow. So to fix it, click on the Arrow next to `master` branch. And select `Manage Branches`

![][12]

You can then see `Unpublished Branches`, right click on your master branch and select `Publish`

Visiting GitHub we can see all our pushed changes!

![][13]

Awesome! Back in Visual Studio, if we navigate back to our `Changes`, we can click on the `Action` button next to `Commit` and select `View History`

![][8]

*this feature seems to lag a little when loading up a large history*

We can see all the history on our commits, for the current selected branch.

![][9]

I think this support for Git in Visual Studio is really awesome! I suggest you take a look, I still use console primarily, but this is really handy, and I find myself committing more often, which is always a good thing!

It's also nice to be able to see changed files in Visual Studio!

![][14]



[0]: /images/git-support-01.png
[1]: /images/git-support-02.png
[2]: /images/git-support-03.png
[3]: /images/git-support-04.png
[4]: /images/git-support-05.png
[5]: /images/git-support-06.png
[6]: /images/git-support-07.png
[7]: /images/git-support-08.png
[8]: /images/git-support-09.png
[9]: /images/git-support-10.png
[10]: /images/git-support-11.png
[11]: /images/git-support-12.png
[12]: /images/git-support-13.png
[13]: /images/git-support-14.png
[14]: /images/git-support-15.png
