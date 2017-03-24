---
layout: post
title: Building / Publishing a .NET Core app using AWS Tools
categories: dotnetcore, aws, lambda, marten, postgresql
series:
	name: aws-dotnet-core-lambda
	current: 4
	part: Part 1: Creating a good old Hello World AWS C# Lambda
	part: Part 2: Building / Configuring the Lambda into AWS
	part: Part 3: Saving / Retrieving data using Marten
	part: Part 4: Building / Publishing a .NET Core app using AWS Tools
	part: Part 5: Configuring the VPC to call the database
---

Now that we've got everything, we need to deploy it, however if we build it using dotnet clr it will fail. 

    "cause": {
      "errorType": "TypeInitializationException",
      "errorMessage": "The type initializer for 'Npgsql.TypeHandlerRegistry' threw an exception.",
      "stackTrace": [
        "at Npgsql.TypeHandlerRegistry.Setup(NpgsqlConnector connector, NpgsqlTimeout timeout, Boolean async)",
        "at Npgsql.NpgsqlConnector.<Open>d__137.MoveNext()",

That's because Npgsql need's to load some dependencies but the Lambda's don't understand that. No matter how I publish, restore for a target runtime and publish agains that, it doesn't seem to work. However, AWS have their own toolkit for publishing lambda's. 

<!--excerpt-->

# Installing the toolkit so we can publish

Instead of using the dotnet clr to publish we can install the [AWS Lambda Toolkit](https://www.nuget.org/packages/Amazon.Lambda.Tools). 

	dotnet add .\HelloWorldLambda\ package Amazon.Lambda.Tools
Once this is installed we need to modify our `.csproj` file.

![](/images/part-4-01.png)

As you can see the toolkit installs as a Reference. If we try to run the toolkit command:

	dotnet lambda -help

![](/images/part-4-02.png)

We don't get any help :( what we need to do is modify our `.csproj` file to change the `PackageReference` to `DotNetCliToolReference`. 

Now the `.csproj` file should look like:

    <Project Sdk="Microsoft.NET.Sdk">
      <PropertyGroup>
        <TargetFramework>netcoreapp1.0</TargetFramework>
      </PropertyGroup>
      <ItemGroup>
        <PackageReference Include="Amazon.Lambda.Serialization.Json" Version="1.0.1" />
        <DotNetCliToolReference Include="Amazon.Lambda.Tools" Version="1.4.0" />
        <PackageReference Include="Marten" Version="1.4.1" />
      </ItemGroup>
    </Project>

Now when we save and run the command again:

![](/images/part-4-03.png)

# Publishing

Publishing is very similar to pushing using dotnet clr, except we need to run the command from the project folder we need to publish so that we can have access to the tools. 

Note: this may be different using s solution, but I haven't tested it.

Let's run:

	dotnet lambda package -c Release -o ../HelloWorldLambda.zip -f netcoreapp1.0
	
This should create a zip file in the root directory where we published the first sample.

![](/images/part-4-04.png)

Now we can go back into AWS and upload and configure the Lambda. In part 5 we will configure the VPC so we can connect to RDS and test our queries.


-----
Note: Part 5 will be published on the 4th of April as I am on holiday, and not taking my laptop, because I need a break :)
