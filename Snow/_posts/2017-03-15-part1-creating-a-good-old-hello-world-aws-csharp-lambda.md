---
layout: post
title: Creating a good old Hello World AWS C# Lambda
categories: dotnetcore, aws, lambda
series:
	name: aws-dotnet-core-lambda
	current: 1
	part: Part 1: Creating a good old Hello World AWS C# Lambda
	part: Part 2: Building / Configuring the Lambda
	part: Part 3: Saving / Retrieving data using Marten
	part: Part 4: Building / Configuring using AWS Tools
	part: Part 5: Configuring the VPC to call the database
---

So finally .NET Core Tools has reached 1.0 and after playing around... it's actually usable now. I can do all the things easily, that I once found difficult.

This blog series is about creating a basic sample handler for an AWS C# Lambda, and extending it to configure a VPC, and query PostgreSQL using Marten, all using VSCode.

To follow this series you need to install:

* .NET Core SDK from [Microsoft](https://www.microsoft.com/net/core#windowscmd)
* [VSCode](https://code.visualstudio.com/)
* [C#](https://marketplace.visualstudio.com/items?itemName=ms-vscode.csharp) for VSCode
* [C# Extensions](https://marketplace.visualstudio.com/items?itemName=jchannon.csharpextensions)... Extension for VSCode

## Creating the class library 

Let's start by creating a class library, create a folder some where, and navigate to it in terminal or command line. Then run the command: 

> dotnet new classlib -n HelloWorldLambda -f netcoreapp1.0

It's important you target netcoreapp1.0 as this is what's supported on AWS C# Lambda's right now.

    phill@DESKTOP-599M841 D:\CSharpLambdaSample
    $ dotnet new classlib -n HelloWorldLambda -f netcoreapp1.0
    Content generation time: 22.1887 ms
    The template "Class library" created successfully.

Now you can open up the folder in VSCode.

<!--excerpt-->

![](/images/part-1-01.png)

Let's rename the class to Handler, and add 2 new classes, Result and Request. You can use the C# Extensions to right click on the folder and add a new class.

![](/images/part-1-02.png)

After adding the classes we can add a `Handle` method to the Handler class that takes a Request and returns the Result.

    public class Handler
    {
        public Result Handle(Request request) {
            return new Result {
                HelloWorld = request.Name
            };
        }
    }

We can now fill in the Request / Result classes with the 2 properties.

    public class Request
    {
        public string Name { get; set; }
    }
    
    public class Result
    {
        public string HelloWorld { get; set; }
    }

Now if we build this, it should, just build.

    PS D:\CSharpLambdaSample> cd .\HelloWorldLambda\
    PS D:\CSharpLambdaSample\HelloWorldLambda> dotnet restore
      Restoring packages for D:\CSharpLambdaSample\HelloWorldLambda\HelloWorldLambda.cs proj...
      Generating MSBuild file D:\CSharpLambdaSample\HelloWorldLambda\obj\HelloWorldLambda.csproj.nuget.g.props.
      Writing lock file to disk. Path: D:\CSharpLambdaSample\HelloWorldLambda\obj\project.assets.json
      Restore completed in 340.03 ms for D:\CSharpLambdaSample\HelloWorldLambda\HelloWorldLambda.csproj.
    
      NuGet Config files used:
          C:\Users\phill\AppData\Roaming\NuGet\NuGet.Config
          C:\Program Files (x86)\NuGet\Config\Microsoft.VisualStudio.Offline.config
    
      Feeds used:
          https://www.nuget.org/api/v2/
          C:\Program Files (x86)\Microsoft SDKs\NuGetPackages\
    PS D:\CSharpLambdaSample\HelloWorldLambda> dotnet build
    Microsoft (R) Build Engine version 15.1.548.43366
    Copyright (C) Microsoft Corporation. All rights reserved.
    
      HelloWorldLambda -> D:\CSharpLambdaSample\HelloWorldLambda\bin\Debug\netcoreapp1.0\HelloWorldLambda.dll
    
    Build succeeded.
        0 Warning(s)
        0 Error(s)
    
    Time Elapsed 00:00:01.86
    PS D:\CSharpLambdaSample\HelloWorldLambda>

The idea is that we will pass in a JSON Object to the AWS Lambda, it will take the `Name` property and return a new object with a new property called `HelloWorld` containing the value passed in.

Super easy scenario.

## Configuring the Lambda for serialization

So when the lambda is called we need it to deserialize the request to a `Request` object and then serialize the `Result` object when it returns.

To do this we need to install the nuget package [Amazon.Lambda.Serialization.Json](https://www.nuget.org/packages/Amazon.Lambda.Serialization.Json/)

From the terminal/command line, run:

> dotnet add .\HelloWorldLambda.csproj package Amazon.Lambda.Serialization.Json

I like to specify the project that it's to be added to, it's handy if you're in the parent directory and want to add to 1 of many projects.

Running this should output something similar to:


    PS D:\CSharpLambdaSample\HelloWorldLambda> dotnet add .\HelloWorldLambda.csproj package Amazon.Lambda.Serialization.Json
    Microsoft (R) Build Engine version 15.1.548.43366
    Copyright (C) Microsoft Corporation. All rights reserved.
    
    Writing C:\Users\phill\AppData\Local\Temp\tmpFF0F.tmp
    info : Adding PackageReference for package 'Amazon.Lambda.Serialization.Json' into project '.\HelloWorldLambda.csproj'.
    log  : Restoring packages for D:\CSharpLambdaSample\HelloWorldLambda\HelloWorldLambda.csproj...
    info :   GET https://www.nuget.org/api/v2/FindPackagesById()?id='Amazon.Lambda.Serialization.Json'
    info :   OK https://www.nuget.org/api/v2/FindPackagesById()?id='Amazon.Lambda.Serialization.Json' 991ms
    info :   GET https://www.nuget.org/api/v2/package/Amazon.Lambda.Serialization.Json/1.0.1
    info :   OK https://www.nuget.org/api/v2/package/Amazon.Lambda.Serialization.Json/1.0.1 265ms
    info : Package 'Amazon.Lambda.Serialization.Json' is compatible with all the specified frameworks in project '.\HelloWorldLambda.csproj'.
    info : PackageReference for package 'Amazon.Lambda.Serialization.Json' version '1.0.1' added to file 'D:\CSharpLambdaSample\HelloWorldLambda\HelloWorldLambda.csproj'.PS D:\CSharpLambdaSample\HelloWorldLambda>


You can run `dotnet restore` after too.

If you look at the csproj file you should now have a package reference.

    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <TargetFramework>netcoreapp1.0</TargetFramework>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Amazon.Lambda.Serialization.Json" Version="1.0.1" />
      </ItemGroup>
    </Project>

Next you can reference the following into the handler class:

    using Amazon.Lambda.Core;
    using Amazon.Lambda.Serialization.Json;

Now on the handler method we need to add the `LambdaSerializer` attribute to the method:

	[LambdaSerializer(typeof(JsonSerializer))]

Your final file should look like:

    using System;
    using Amazon.Lambda.Core;
    using Amazon.Lambda.Serialization.Json;
    
    namespace HelloWorldLambda
    {
        public class Handler
        {
            [LambdaSerializer(typeof(JsonSerializer))]
            public Result Handle(Request request) {
                return new Result {
                    HelloWorld = request.Name
                };
            }
        }
    }

Running `dotnet build` should be successful.

In part 2 we will publish, deploy, and test the lambda.





