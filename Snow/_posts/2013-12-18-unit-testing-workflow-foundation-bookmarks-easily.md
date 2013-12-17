---
layout: post
category: Workflow, Unit Testing
title: Unit testing Workflow Foundation Bookmarks... easily...
---

I'm not a fan of Workflow Foundation, but you got to do what you got to do. Usually when using WF, unit testing an activity is rather easy. Invoke with some data, assert the out value, possibly watch some methods on some dependencies...

But when it comes to testing Bookmarks, if the bookmark is created, and resuming it, it's not easy...

Well that is unless you're using [Microsoft.Activities.UnitTesting][workflow-codeplex]

So creating some new projects, a workflow project and a class library for testing...

<!--excerpt-->

![][1]

Next we want to add the MS Libraries:

![][0]

This will add:

`Microsoft.Activities.UnitTesting`

and

`Microsoft.Activities.Extensions`

I've also added `xUnit` for testing :)

Now we can create a basic workflow activity:

    using System.Activities;

    public class SampleActivity : NativeActivity<bool>
    {
        public InArgument<int> ValueOne { get; set; }
        public InArgument<int> ValueTwo { get; set; }

        protected override bool CanInduceIdle
        {
            get { return true; }
        }

        protected override void Execute(NativeActivityContext context)
        {
            var valueOne = context.GetValue(ValueOne);
            var valueTwo = context.GetValue(ValueTwo);

            if (valueTwo % 2 == 0)
            {
                //We do something and return true...
                context.SetValue(Result, true);

                return;
            }

            // Else condition was not met so we sleep and come back later
            context.CreateBookmark("Bookmark_" + valueOne, ResumeBookmarkCallback);
        }

        private void ResumeBookmarkCallback(NativeActivityContext context, Bookmark bookmark, object value)
        {
            // When we wake up, return false and let it try again
            context.SetValue(Result, false);
        }
    }

***Note: Post isn't about workflow, it's about unit testing. This is just a random example. :)***

So completely random, given `valueTwo` mod `2` equals `0` then we should set the return value as `true`, else we create a bookmark... and when the bookmark is woken, it returns false.

The first thing we want to test is the super duper happy path, given `valueTwo` is `10` then we should get `0` remaining and we can assert that the result is `true`.

    [Fact]
    public void Given_10_For_Value_Two_Should_Have_A_Return_Argument_Of_True()
    {
        // Arrange
        var wfTester = WorkflowApplicationTest.Create(new SampleActivity
        {
            ValueOne = 123,
            ValueTwo = 10
        });

        // Act
        wfTester.TestActivity();

        // Assert
        wfTester.AssertOutArgument.IsTrue("Result");
    }

Here we create an instance of the `WorkflowApplicationTest` class, passing in an activity with data. 

Next we call `TestActivity` and then we `Assert` the value. 

![][2]

Sure enough, it passes :)

Now, given the value 9, we should get a bookmark named `Bookmark_123`

    [Fact]
    public void Given_9_For_Value_Two_Should_Have_Bookmark_Using_Value_One()
    {
        // Arrange
        var wfTester = WorkflowApplicationTest.Create(new SampleActivity
        {
            ValueOne = 123,
            ValueTwo = 9
        });

        // Act
        wfTester.TestActivity();
        wfTester.TestWorkflowApplication.GetBookmarks();

        // Assert
        Assert.Equal("Bookmark_123", wfTester.Bookmarks.First());
    }

Like the first test, we start out the same, except after we call `TestActivity` we actually call `GetBookmarks` off the `TestWorkflowApplication`

This is rather important, I couldn't find this in any of the documentation, but without this, the `Bookmarks` property is always empty...

![][3]

So by having the `GetBookmarks` call, ensures the `Bookmarks` property is populated.

Now, lastly we want to resume the bookmark and assert that the result is returned. We are expecting `false` so we create a new test, similar to the last, but we resume...

    [Fact]
    public void When_Resuming_A_Workflow_Should_Get_The_Return_Value_False()
    {
        // Arrange
        var wfTester = WorkflowApplicationTest.Create(new SampleActivity
        {
            ValueOne = 123,
            ValueTwo = 9
        });

        // Act
        wfTester.TestActivity();
        wfTester.TestWorkflowApplication.ResumeBookmark("Bookmark_123", 0);

        // Assert
        wfTester.AssertOutArgument.IsFalse("Result");
    }

So again, it's similar to the second test, except rather than call `GetBookmarks` we called `ResumeBookmark`, passing in the bookmark name, and a value. Although in our case we don't need it, so I passed in 0.

*null is not allowed :(*

Then we assert that the value is false!

![][4]

And that's it! A passing unit test! Easy peasy :)

I've put this test into a github repository that can be found here:

<https://github.com/phillip-haydon/WorkflowBookmarkUnitTest.git>


[0]: /images/workflow-01.png
[1]: /images/workflow-02.png
[2]: /images/workflow-03.png
[3]: /images/workflow-04.png
[4]: /images/workflow-05.png

[workflow-codeplex]: http://wf.codeplex.com