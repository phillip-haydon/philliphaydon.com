---
layout: post
title: Saving / Retrieving data using Marten
categories: dotnetcore, aws, lambda, marten, postgresql
series:
	name: aws-dotnet-core-lambda
	current: 3
	part: Part 1: Creating a good old Hello World AWS C# Lambda
	part: Part 2: Building / Configuring the Lambda into AWS
	part: Part 3: Saving / Retrieving data using Marten
	part: Part 4: Building / Configuring using AWS Tools
	part: Part 5: Configuring the VPC to call the database
---

So far we have created a basic lambda, deployed it into AWS, and executed it. However, it doesn't do anything useful. While there are many things you can do, one of the things you're most likely going to want to do is touch a database. But let's get a little bit fancy and do it with Marten.

If you don't know Marten, you're missing out. It's a LOT of fun. There's two parts to Marten, an event store, and the super awesome fun bit that I love. The Document Database. 

Marten uses PostgreSQL's fancy JSONB data type to store documents in the database, with an API built on top to make working with your domain easier without having to worry about your schema. Check it out.

[http://jasperfx.github.io/marten/](http://jasperfx.github.io/marten/)

Let's get started. 

<!--excerpt-->

# Installing / Configuring Marten

Marten only works with PostgreSQL, so I let's assume you've got that installed. Let's start by installing Marten into the hello world app. We run the dotnet command from the command line / terminal to install the package into the HelloWorldLambda project.

    > dotnet add .\HelloWorldLambda\ package Marten

Now we can add the Marten reference

	using Marten;
Next let's create a constructor and create a document store inside the constructor.

	private readonly IDocumentStore store;
	public Handler()
	{
	    store =DocumentStore.For(_ =>
	    {
	        _.Connection("Host=...;Username=...;Password=...;Database=...");
	        _.AutoCreateSchemaObjects = AutoCreate.CreateOrUpdate;
	    });
	}

Too easy! Now we are ready to use Marten. 

# Implementing Save

In our handler, let's add 2 new methods. `Get` and `Save`

    [LambdaSerializer(typeof(JsonSerializer))]
    public Product Save(Product product){
        return null;
    }
    
    [LambdaSerializer(typeof(JsonSerializer))]
    public Product Get(GetProductRequest request) {
        return null;
    }

Create the two new classes. 

    public class Product
    {
        public int Id { get; set; }
        public string Name { get; set; }      
        public string Description { get; set; }
    }
    
    public class GetProductRequest
    {
        public int Id { get; set; }
    }
Let's start with the save. Using the Marten Store we created in the constructor, let's create a session. The session is effectively a Unit of Work. No you don't need to wrap this up in your own glorified unit of work like everyone attempts to do with Entity Framework or NHibernate. Just leave it alone.

    [LambdaSerializer(typeof(JsonSerializer))]
    public Product Save(Product product) {
        using (var session = store.LightweightSession()) {
            session.Store(product);
            session.SaveChanges();
    
            return product;
        }
    }

So we open a session. I'm using a Lightweight session, this is a session that does not contain any change tracking at all. Refer to the documentation for more information. 

We add the product that is passed in to the session, call Save Changes, then return the product back out. This will populate the Id field and we will know the Id of the newly inserted product.

Next let's work with the Get request.

    [LambdaSerializer(typeof(JsonSerializer))]
    public Product Get(GetProductRequest request) {
        using (var session = store.QuerySession()) {
            return session.Load<Product>(request.Id);
        }
    }

In this scenario we open only a Query session, this is a session which only allows you to query and does not provide change tracking or any ability to store an object. 

We load based on the Id and return the product back out.

BAM! And we are done! That's Marten configured, and saving/retrieving data. Too easy!

-----

It's worth noting that this is purely an example and obviously you would want to do more validation around the requests. 

Next up is building / configuring the new Lambda handler implementations.