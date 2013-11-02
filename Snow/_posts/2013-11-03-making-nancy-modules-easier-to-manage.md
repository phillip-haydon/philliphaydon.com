---
title: Making Nancy Modules easier to manage
categories: NancyFX
layout: post
---


One problem I think a lot of developers have, is creating Controllers and Modules that are small and maintainable. Sometimes when you add some querying, bit of validation, pass it off to a repository or service, etc. It begins to become a little bit too big. 

Lets assume our we have an `AccountModule` it Gets a Login route to load the UI, and Posts to a Login route to authenticate the user, and do something.

It also Gets a Register route to load the UI, and Posts to a Register route to register the new user. 

Without an actual implementation, something like this:

    public class AccountModule : NancyModule
    {
        public AccountModule() : base("/account")
        {
            Get["/login"] = _ => "login";
            Post["/login"] = _ => "login";

            Get["/register"] = _ => "register";
            Post["/register"] = _ => "register";
        }
    }

To begin with, we only have 4 things to implement, its rather small, somewhat easy to manage, but what happens when we want to update an account?

<!--excerpt-->

	Get["details"] = _ => "details...";
	Post["details"] = _ => "details...";

Then we want to have a profile...

	Get["profile"] = _ => "profile...";

It just keeps adding up...

At this point it begins to get cluttered. Not only that, I believe this also breaks the Single Responsibility Principal.

## My solution!

*This isn't anything new, I personally haven't seen anyone blog about this stuff but I have absolutely no doubt people most likely create controllers like this in MVC*

So that's why I've decided Modules should be broken up into Functionality rather than Functional Areas.

All these things hang off `Account`, so lets create an `Account` folder in our `Modules` folder. 

Inside this folder we can create 4 new modules.

![][0]

So we have 4 modules rather than 1:

1. `DetailsModule`
2. `LoginModule`
3. `ProfileModule`
4. `RegisterModule`

As you can expect, these are all really small now:

    public class DetailsModule : NancyModule
    {
        public DetailsModule()
            : base("/account/details")
        {
            Get["/"] = _ => "details";
        }
    }

    public class LoginModule : NancyModule
    {
        public LoginModule()
            : base("/account/login")
        {
            Get["/"] = _ => "login";
            Post["/"] = _ => "login";
        }
    }

    public class ProfileModule : NancyModule
    {
        public ProfileModule()
            : base("/account/profile")
        {
            Get["/"] = _ => "profile...";
        }
    }

    public class RegisterModule : NancyModule
    {
        public RegisterModule()
            : base("/account/register")
        {
            Get["/"] = _ => "register";
            Post["/"] = _ => "register";
        }
    }

From our folder perspective we can easily find all Modules under the `/account` route, and easily find the one related to `Login`, or `Register`, etc.

This also makes unit testing easier since we only need to worry about dependencies related to a specific piece of functionality. 

`Login` might use a RavenDB session and Password Hashing Service, while `Profile` may use RavenDB and a 3rd party Image hosting service for Profile pictures. 

Disagree or have a better way? Would love to hear! Share in the comments :)


[0]: /images/small-nancy-module-01.png