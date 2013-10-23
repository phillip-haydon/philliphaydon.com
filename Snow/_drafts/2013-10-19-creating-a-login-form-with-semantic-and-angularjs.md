---
layout: post
title: Creating a login form with Semantic and AngularJS
categories: AngularJS, Semantic-UI
series: 
	name: angular-login
	current: 1
    part: Creating a Login form with Semantic and AngularJS
    part: Logging in using Angular
    part: Unit testing Angular
---

Continuing on my learning with Semantic and Angular, I'm going to show how you can create a login form, wire it all up, login, and then unit test the Angular codes.

I'm going to use NancyFX for the website, but you can use MVC/WebAPI or what ever. Nancy will be easier though ;)

References you will need. 

1. [Semantic UI][0]
2. [AngularJS][1]
2. [jQuery][2]

Download and reference these in your project:

![][3]

Now lets reference them in our HTML page. In the image you can see I have a `index.html` page, that's our landing page.

<!--excerpt-->

In there we want to setup the basic HTML, in the header we will add:

    <link href="/content/semantic/css/semantic.min.css" rel="stylesheet" type="text/css" />

And before the `</body>` tag we will add:

    <script src="/content/jquery-1.10.2.min.js"></script>
    <script src="/content/semantic/javascript/semantic.min.js"></script>
    <script src="/content/angular.min.js"></script>
    <script src="/content/demo/app.js"></script>

The `app.js` file is our file that we will implement our `LoginController` and other such Angular stuff.

I've also added a little bit of CSS at the top to center align this on the page:

    <style type="text/css">
        body {
            width: 800px;
            margin: 0 auto;
            font-family: arial;
            padding: 25px;
        }
    </style>

Next lets write up a basic login for using the Semantic-UI look and feel.

    <div class="ui form segment">

        <h3 class="ui header">Login</h3>

        <div class="field">
            <label>Username</label>
            <input type="text" placeholder="Username">
        </div>

        <div class="field">
            <label>Password</label>
            <input type="password" placeholder="Password">
        </div>

        <div class="ui blue button floated right">Login</div>
    </div>

Now we have a basic form

![][4]

It's a little bit basic so lets make it a little nicer, some icons. Lets update the HTML to include icons in the inputs:

    <div class="field">
        <label>Username</label>
        <div class="ui left labeled icon input">
            <input type="text" placeholder="Username">
            <i class="user icon"></i>
        </div>
    </div>

    <div class="field">
        <label>Password</label>
        <div class="ui left labeled icon input">
            <input type="password" placeholder="Password">
            <i class="lock icon"></i>
        </div>
    </div>

For the Username we use a little `user` icon, and for the Password we use a little `lock` icon.

![][5]

Looking a little nicer now. But it uses too much space. If we made it smaller then the right hand side may look empty. 

A common approach is to decide the page up into two sections. A login on the left and register on the right. 

Lets link to a registration page rather than putting another form there, since another form would make this demo complicated, but should give you enough to knowledge to replace it with a form.

Using Semantic we will wrap our current form with a `div` and declare it as a grid: 

    <div class="ui two column grid basic segment">
      //columns go here...
	</div>

Now we want to add two columns:

    <div class="ui two column grid basic segment">

        <div class="column">
		  //Login Form
        </div>

        <div class="column">
          //Register Link
        </div>

    </div>

We already have our form, so for our register link we can add an anchor tag as a `huge` button like so:

	<a class="huge purple ui labeled icon button" href="#">
	    <i class="signup icon"></i>
	    Register
	</a>

The `href` would point to the register page.

Putting this all together now.

![][6]

Lets tidy it up even more and make it look more spaced out.

On the grid, lets make it middle aligned. Update the CSS to be `middle aligned`

	<div class="ui two column middle aligned grid basic segment">

And on the register button column, lets make that `center aligned`

Middle is the vertical alignment and center is the horizontal alignment.

	<div class="center aligned column">

And now we should have something that looks more aligned.

<span class="note">**Note:** I updated the body css to have a light grey background so you can easily see the alignment.</span>

![][7]

To make this even nicer again, I got this tip from the Semantic-UI website, is to use their `divider` to put a line between the two columns. 

Between the two columns we can add:

	<div class="ui vertical divider">
	    Or
	</div>

And also add the class `relaxed` to the grid:

	<div class="ui two column middle aligned relaxed grid basic segment">

Super simple, and now when we refresh the page:

![][8]

That's it! With very little effort we have a nice looking login form.

In part 2 we will wire up Angular and get some data being sent to the server, and then in part 3 we will unit test the Angular codez. 



 [0]: http://semantic-ui.com
 [1]: http://angularjs.org
 [2]: http://jquery.com
 [3]: /images/semantic-login-01.png
 [4]: /images/semantic-login-02.png
 [5]: /images/semantic-login-03.png
 [6]: /images/semantic-login-04.png
 [7]: /images/semantic-login-05.png
 [8]: /images/semantic-login-06.png