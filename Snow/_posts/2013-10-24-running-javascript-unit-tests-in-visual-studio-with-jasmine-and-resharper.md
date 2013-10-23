---
layout: post
title: Running JavaScript Unit Tests in Visual Studio with Jasmine & ReSharper
categories: AngularJS, JavaScript, Unit Testing, Jasmine, ReSharper
series: 
	name: javascript-unit-tests
	current: 1
    part: Running Basic Unit Tests in Visual Studio without a browser
	part: Running Basic Unit Tests with a browser and separate projects
---

There's so much information on the internet in terms of JavaScript Unit Testing and how to run tests etc, but when it comes to running tests in Visual Studio, without a browser, there's very little information. I had to piece information together to figure it out.

So I'm going to do a small series on this.

Things you will need!

1. [Jasmine][0]
2. [PhantomJS][1]

##Setup PhantomJS

To run JavaScript tests without a browser, we need PhantomJS, and we need to wire it up into Resharper.

<!--excerpt-->

Download PhantomJS and unzip it somewhere, I put it in my development folder. Next open up ReSharper Options:

![][2]

Navigate to the Unit Testing > JavaScript Tests and set the PhantomJS path to the `.exe` where you unzipped it to:

![][3]

Save all that and PhantomJS will be all setup and ready to run out tests!

##Folder Setup

In Visual Studio create a new `Empty Project`, or something similar, add some folders, naming isn't important but I've stuck with `dependencies` for things like Jasmine, `sources` for the files we will be testing, and `specs` for the Jasmine specs.

![][4]

Copy the `jasmine.js` file into the dependencies folder, and create two empty files for `Calculator.js` and `CalculatorSpec.js`

We will create a super simple calculator that's only capable of multiplying two numbers. There's lots of Jasmine examples so I'm not going to bore you with those details. 

## Our first basic test

Lets start by adding a basic test in our `CalculatorSpec.js` file:

	
	///<reference path="~/dependencies/jasmine.js"/>
	///<reference path="~/sources/Calculator.js"/>
	
	describe("Calculator", function () {
	    var calculator = new Calculator();
	
	    it("should multiple two positive numbers", function () {
	        var result = calculator.multiple(2, 5);
	
	        expect(result).toBe(10);
	    });
	});

Let's break this down, at the top I have some 'reference path' comments, then I have the actual test. The reference paths is what got me initially when trying to get this working.

Its a Visual Studio thing which gives intellisense, but it also allows ReSharper to find the files when running the unit tests. Without it the tests fail. 

So if we run this test now...

![][5]

We have no implementation, so lets implement!

Lets create a new function for the `Calculator`

	var Calculator = function() {
	
	};

![][6]

Now its undefined on the function call `multiple`, so lets add it in, but lets return a hard coded value of -100

	var Calculator = function() {
	    this.multiple = function() {
	        return -100;
	    };
	};

![][7]

Almost there, now lets multiple two values together!

	var Calculator = function() {
	    this.multiple = function(valueOne, valueTwo) {
	        return valueOne * valueTwo;
	    };
	};

BAM it passes!

![][8]

Our first test without a browser.

Next up I'll show you how you can also test with a browser, and how to reference files in a separate project.


[0]: http://pivotal.github.io/jasmine/
[1]: http://phantomjs.org/
[2]: /images/jasmine-01-01.png
[3]: /images/jasmine-01-02.png
[4]: /images/jasmine-01-03.png
[5]: /images/jasmine-01-04.png
[6]: /images/jasmine-01-05.png
[7]: /images/jasmine-01-06.png
[8]: /images/jasmine-01-07.png