---
title: If you're going to use repositories, don't have generic or base repositories...
layout: post
categories: Rant, Repositories
---

Repositories are one of those patterns we hate to love or love to hate, but seems to be getting more hate lately than love. 

Personally I don't have any real problem with repositories themselves, I just have a problem with the way it is used. 

If you run the following search in Google:

> BaseRepository OR GenericRepository OR IRepository site:stackoverflow.com

You will get about 10k results on Stack Overflow for questions that contain something related to a generic repository or base repository. You end up with code snippets such as:

	public interface IRepository<TEntity> : IDisposable where TEntity : class
	{
	    IUnitOfWork Session { get; }
	    IList<TEntity> GetAll();
	    IList<TEntity> GetAll(Expression<Func<TEntity, bool>> predicate);
	    bool Add(TEntity entity);
	    bool Delete(TEntity entity);
	    bool Update(TEntity entity);
	    bool IsValid(TEntity entity);
	}

<!--excerpt-->

Or

	public class GenericRepository<TEntity> : IRepository<TEntity> where TEntity : class
	{
	    public virtual IEnumerable<TEntity> FindAll(Expression<Func<TEntity, bool>> where = null)
	    {
	        // implementation ...
	    }
	
	    public virtual TEntity FindOne(Expression<Func<TEntity, bool>> where = null)
	    {
	        // implementation
	    }
	
	    public void Update(TEntity entity)
	    {
	        // update your entity ...
	    }
	
	    // etc...
	}

These are just bad bad bad...

## Generic Repositories make assumptions about your domain ##

Generic Repositories is that they make assumptions about your domain. It makes assumptions such as:

- All entities should contain Get/List/Update/Add/Delete... etc
- All entities come from the same location (i.e a database)
- All entities have a single primary key of some sort...

The problem with this, is that these are all false assumptions, generic repositories seem to never take into account that entities can have composite keys or no key identifier at all. Or they require some, but not all implementations defined in the base class or interface. 

Often people assume only a single kind of persistence, when you could load some data from a flat file, database, a web service, etc.

These isn't a problem is you do away with the base implementation or interface and just have explicit interfaces such as `IProductRepository` or `IUserRepository`, but people get so court up on the whole idea of having lots of interfaces, and instead try to opt for the generic `IRepository<T>` until they need to implement other stuff.

## Generic Repositories don't prevent code duplication ##

Generic repositories don't prevent duplication because there's no duplication to begin with! What you end up doing is abstracting an abstraction, for the sake if feeling like you're writing less code or getting implementation for free, when you're actually adding unnecessary code that may never be used!

If for example you have a `User` / `UserRepository`, you may find that your generic repository asks you to implement `Delete`...

But when if you are never required to delete a user, and instead you Deactivate a user, or disable a users login, but keep the user because hes actually attached to data in the system...

You end up implementing methods, or overriding existing methods to have no implementation or throw a NotImplementedException. This is just bad design. 

You could argue "Oh but when I implement `GetById` I have to duplicate it..."

Except you're not duplicating it, just because two things do similar or the same things, doesn't mean its duplicated.

Lets say you have a `UserRepository` and `OrderRepository`, both have a method defined in a base repository called `GetById`, if you changed `GetById` for a User, you're changing the way the Order one works. You're potentially breaking your application. These two look the same, but they are different. It's not duplication!


## Repositories for everything ##

The next issue is people end up creating repositories for everything, not their aggregate roots. For example, say we have an `Order` / `OrderLine` scenario...

	public class Order
	{
	  public int Id { get; set; }
	  ...
	  
	  public IList<OrderLine> Lines { get; set; }
	}
	
	public class OrderLine
	{
	  public int Id { get; set; }
	  ...
	}

Often enough people will create an `OrderRepository` and `OrderLineRepository`, or use their Generic repositories like `BaseRepository<Order>` and `BaseRepository<OrderLine>`, the problem is, everything to do with an `OrderLine` is actually the responsibility of the `Order` and not anything else. 