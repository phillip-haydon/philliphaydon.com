---
layout: post
title: Configuring the VPC so we can call the database
categories: dotnetcore, aws, lambda, marten, postgresql, vpc
series:
	name: aws-dotnet-core-lambda
	current: 5
	part: Part 1: Creating a good old Hello World AWS C# Lambda
	part: Part 2: Building / Configuring the Lambda into AWS
	part: Part 3: Saving / Retrieving data using Marten
	part: Part 4: Building / Configuring using AWS Tools
	part: Part 5: Configuring the VPC so we can call the database
---

Ahh the final part.

So if you're using AWS you're most likely using RDS. If you're using RDS then you've got a database, and if it's in production then hopefully you've disabled public access and haven't configured it to allow a connection from any IP address.

If this is the case then the moment we test our Lambda, it's going to fail with a timeout. 

    {
      "errorMessage": "2017-03-23T17:12:36.269Z e0474e8b-0feb-11e7-a0fe-276de97e95bf Task timed out after 15.00 seconds"
    }

This happens because it cannot connect to the database, if the lambda execution time is less than your database timeout then this happens. Otherwise if the inverse is true you would get the database timeout exception rather than the lambda timeout exception.

<!--excerpt-->

So RDS instances are created with a VPC by default, assuming that everything is created with default settings and the vpc is named rds-launch-wizard, we can begin configuring the Lambda to have permission to connect to RDS.

# Create a role with VPC support

Let's start by opening up IAM and creating a new role. 

![](/images/part-5-01.png)

Select AWS Lambda from the Role Types.

![](/images/part-5-02.png)

The trust step is skipped and it will go directly to Attach Policy.

Under attach policy you want to add:

1. AWS Lambda Basic Execution Role
2. AWS Lambda VPC Access Execution Role

![](/images/part-5-03.png)

On the final step we can name our role, such as `lambda-with-vpc-access`

![](/images/part-5-04.png)

Hit create and now we should have a new role ready for use in our Lambda.

# Upload / Configure

Let's start by uploading the lambda and configuring it. Let's create a new blank function like we did in part 2.

Give it a name like 'CreateProduct', and select the package we created in part 4.

![](/images/part-5-05.png)

Set the Handler to:

	HelloWorldLambda::HelloWorldLambda.Handler::Save

Remember, this correlates to the dll::namespace.class::function

Under the Role, pick 'Choose an existing role', the role we created above should show:

![](/images/part-5-06.png)

Now, expand out the Advanced settings section.

You can modify the Memory / Timeout now or change it later. To test with I set mine to 512mb and 15 second timeout. Once it's all working I adjust it down.

Under the VPC, select the VPC that your RDS instance uses, and add the subnet's its in, as well as the security group. 

For example, this is my RDS Configuration:

![](/images/part-5-07.png)

Vs the VPC Configuration of the Lambda:

![](/images/part-5-08.png)

Note: Yes I blanked things out because things and stuff don't want people figuring out my account info.

Everything else you can leave default.

Now review, and create that Lambda!

# Testing

Now that it's saved, we can test our lambda, so let's click on Actions and pick 'Configure test event'

![](/images/part-5-09.png)

Now click Save and test, and unlike the start of this post, we should get successful execution result.

![](/images/part-5-10.png)

When testing with Lambda targeting RDS, it usually has a startup time of 2-3 seconds, but every execution after that seems super fast. 

![](/images/part-5-11.png)

Enjoy. 



























