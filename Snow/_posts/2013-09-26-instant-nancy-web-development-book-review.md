---
title: Instant Nancy Web Development - Book Review
categories: NancyFX, Book Review
layout: post
---

Just today, [Christian Horsdal][0] released his first book, his first topic to write a book about, only the most awesome Web Framework to exist in .NET! :)

![][3]

*non affiliate links*

 - [Amazon][2]
 - [Packt Publishing][1]

This book is rather short, clocking in at only 74 pages. Not to worry though, it covers quite a bit! 

Usually I don't go near Packt Publishing books after having a bad experience buying a few books, all of which were terrible. But seeing as this was a Nancy book I couldn't pass up the chance to check it out. 

Considering this is the first book Christian has written, it reads very well. I've read books from authors who have done 3 or 4 books and I struggle to read them because their style of writing. So that alone impressed me about this book.

Not that the book is perfect, I took the opportunity to tweet him this image:

![][4]

I shouldn't laugh though... my spelling is absolutely terrible!

Right from Chapter One he gets into things, showing you just how little it takes to go from nothing, to a good old Hello World app, following onto Chapter Two he begins making a Todo Web App that's done doing TDD.

This alone was really valuable to me, because he showed some little tips and tricks that I had never seen other people do before, showing how you can `Post` and then `Get` consecutively and assert the response. 

Mocking is demoed using FakeItEasy which I sometimes find a little hard to read, that's mostly due to having used Moq. But its great to see examples using another framework that I'm not entirely familiar with.

The topics covered in this book are pretty vast. In-fact, here's the table of contents:

 - Building and running your first Nancy application
 - Nancy testing
 - Routes and model binding
 - Taking a dependency - introducing the bootstrapper
 - Content negotiation and more model binding
 - Adding views
 - Adding static content
 - Hosting Nancy on the Cloud
 - Handling cross-cutting concerns - Before, After, and Error hooks
 - Authenticating users
 - Separating applications and hosting
 - Using async handlers

## Complaints...

There isn't many, the only things I wish would change is the View chapter to come before Content Negotiation, this is mainly because I think people coming from MVC or Web Forms will want to view something in their browser quickly, while returning JSON/XML may not be immediately obvious to them, then explaining that Views are just another content type that is negotiated. 

Secondly is the use of Razor, little part of me wishes it showed the build in View Engine - Simple Simple View Engine. Rather than diving directly into Razor. But this is personal opinion, its entirely possible users reading may care more about Razor. :) Granted it does mention at the end of that chapter what SSVE is and that you can implement your own or download others :)

The hosting Nancy in the Cloud chapter doesn't make a lot of sense, its the only chapter that feels kinda out of place, it should focus less on the testing/deployment and more on getting the app up and running on a cloud provider.

Hopefully no one takes my opinions/complaints the wrong way because I don't believe any of the things I've listed are reasons to not get the book!

## Errors

These aren't so much as errors, just things that need to be updated.

 1) The chapter on Authenticating Users uses WorldDomination, which was renamed to SimpleAuthentication, all the rest still applies though.

 2) Async chapter uses the Beta packages and mentions that at the time of writing, it wasn't in release. This has now been released in 0.20.0 of Nancy.

All things considering, those are pretty damn minor! 

## Final Words

This book is downright great, if you're new to Nancy, want to learn it, or have been using it for a while but don't feel 'expert', this is a great book and I highly recommend it!

The book is short that I read it in a few hours, granted I have prior knowledge of Nancy so I didn't create the app while reading, but I did pick up a few little things I didn't know about, especially around Testing with Nancy.

I enjoyed reading the book, so I hope you do too!

## Want a copy? Comment below!

I'm going to gift 3 Kindle Copies of the book, if you would like a copy, comment or tweet the book with the hash-tag #nancyfx and I'll pick 3 people on the 4th of October. 

*(If you comment below, please use your real email address so I can contact you should you win, its only visible to me)*


 [0]: https://twitter.com/chr_horsdal
 [1]: http://www.packtpub.com/nancy-web-development/book
 [2]: http://www.amazon.com/gp/product/B00FF8OKP8
 [3]: /images/nancy-book-review-1.png
 [4]: /images/nancy-book-review-2.png