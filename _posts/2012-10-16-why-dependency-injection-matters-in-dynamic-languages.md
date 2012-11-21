---
layout: post
title: Why Dependency Injection Matters In Dynamic Languages
tags:
    - Software Design
    - Dependency Injection
---

One of the foundational principles of good software design is that code should be loosely coupled, and there are few better ways to achieve this in the world of object-oriented programming than with dependency injection (DI).  Typically implemented with a *dependency injection container* &mdash; an object which manages and resolves concrete dependencies for client classes &mdash; dependency injection allows classes to be written without tying them to any specific concrete types or instances.

As a pattern, DI is well established in a number of frameworks for statically typed environments (such as Microsoft's [Unity](http://unity.codeplex.com/) container for .NET applications, or the lightweight [Picocontainer](http://picocontainer.codehaus.org/) for Java).  But is there any benefit in using the pattern with dynamic languages, such as JavaScript, when the concept of static types doesn't really apply?

The answer is: almost certainly!  And there are a few frameworks which are aware of this (such as [AngularJS](http://angularjs.org/)) &mdash; but they're still somewhat in the minority.  So let's take a look at the ways in which DI helps us write robust, testable, loosely coupled applications &mdash; and why these techniques are relevant irrespective of the typing system.

## 1. Resolve concrete types at runtime

This is one of the most obvious benefits of a DI container in a statically typed language &mdash; the container is configured to map a particular interface to a specific concrete implementation at runtime.

As an example, suppose we're writing a .NET application, and in order to abstract data access we have the following ```IDataRepository``` interface:

	public interface IDataRepository {
	    IEnumerable<IUser> users { get; }
	    IEnumerable<IAccount> accounts { get; }
	}

Our WPF client application might communicate with our server over some RESTful web services.  So we have an ```HttpRepository``` concrete implementation:

	public class HttpRepository implements IDataRepository {
	    public property IEnumerable<IUser> users
	    {
	        get
	        {
	            // makes a web request and returns the results
	        }
	    }
	    
	    public IEnumerable<IAccount> accounts
	    {
	        get
	        {
	            // ditto
	        }
	    }
	}

We can now map the ```IDataRepository``` interface to the correct class at runtime when we configure the container with the application bootstrapper:

	public class Bootstrapper extends UnityBootstrapper {
	
	    protected override void ConfigureContainer()
	    {
	        base.ConfigureContainer();
	    
	        this.RegisterTypeIfMissing(typeof(IDataRepository), typeof(HttpRepository));
	    }
	
	}

And now when we resolve an instance of the ```IDataRepository``` interface through our container, we'll get back an instance of ```HttpRepository```, as desired.  If we want to configure the container differently for a server application (to map to a ```DbRepository``` to interface with our database, say), then it's one line of code to change in the bootstrapper.

Is this kind of mapping relevant only to statically typed languages?  Not at all.  In the sense that our JavaScript object constructors or CoffeeScript classes can be considered types, we can decouple our concrete implementations from the code which has them as dependencies, retaining exactly the same advantages of loose coupling and runtime decisions about types as we would with a statically typed environment.

## 2. Delegate configuration of the container to the appropriate unit of code

One of the reasons why a tight coupling between one unit of code and its dependencies is disadvantageous is that the setup phase for tests must swap in any necessary test doubles for dependencies of the subject under test (SUT) in a very manual way.  For example, I often see tests which look like this:

	describe "Application", ->
	
	    beforeEach: ->
	        # mock/fake/dummy all the things
	        repository = new TestRepository
	        renderer = new TestRenderer
	        # etc.
	        
	        app = new Application(repository, renderer, ...)
	    
	    it "is awkward to configure the environment for testing", ->
	        app.run()

This is highly brittle: every time you add a dependency to a class you have to locate all the tests which involve that class in some way and add in suitable test doubles; and every time you write a new test, you have to figure out all of the dependencies across all of the classes involved, and ensure they're suitably doubled.  In other words, there's a tight coupling between each test and the dependencies of the SUT.

With code like this, our tests will become increasingly difficult to write and maintain as the complexity of our codebase grows.  A better approach is to delegate configuration of the container to either the application bootstrapper, or the individual application modules.  (The most suitable approach might depend on the requirements and conventions of your codebase, but both will result in tests which are more robust, and quicker and easier to write.)  Here's an example of how we might use our ```RepositoryModule``` to configure a ```DataRepository``` type appropriate to the execution environment:

	class RepositoryModule
	
	    configure_container: (container, env) ->
	        if env == "web"
	            container.register_class "DataRepository", HttpRepository
	        else if env == "nodejs"
	            container.register_class "DataRepository", DbRepository
	        else if env == "test"
	            container.register_class "DataRepository", MemoryRepository

And now, to configure and run our entire application for testing, our code is as simple as this:

	beforeEach: ->
	    bootstrapper = new TestBootstrapper(env: "test")
	    app = new Application
	    
	define "Application", ->
	
	    it "is really easy to run in a test environment", ->
	        app.run(bootstrapper)

This removes the coupling between our tests and the dependencies of the SUT &mdash; which allows us to write much more robust and succinct tests.

## 3. Remove dependencies on multiplicity of a class

Do we want our data source to be shared across the entire application?  Do we want each client unit of code to instantiate its own data source?  Often, the answer is contextual: we might want to share a database connection for performance reasons in one application; but we might want to create a new repository each time we need data access in another, in order to avoid the risk of stale data.

If we use a DI container, then clients of our data repository aren't required to have any knowledge about how and when to instantiate a new instance, or whether to share an existing one.  If we wish to share an instance, we can configure our container to memoize the repository it resolves, so that the same instance will be returned on all subsequent calls to ```resolve```:

	container.register_class "DataRepository", DbRepository, singleton: true
    
This helps us further reduce dependencies in our code.  And removing this particular dependency is likely to help us write more robust tests as well &mdash; because it's awkward to test singletons or monad objects (see [How To Write Testable Code](/blog/2012/10/10/how-to-write-testable-code)) &mdash; and by removing the expectation that a particular dependency should always be a single instance, we can remove that requirement in tests as well.
        
## 4. Use different containers across a single application

It's often useful to be able to configure several distinct containers to resolve mappings differently across different contexts in an application.  As an example, suppose we want each application module to keep its own open connection to the database:

	UsersModule
	
	    @dependency repository: "DataRepository"
	
	    configure_container: (container) ->
	            container.child().register_instance "DataRepository", container.resolve "DataRepository"
	
	    find: (id) ->
	        @repository.find("user", id)

We can continue to configure our application bootstrapper class with the data source appropriate to our environment (```DbRepository```, ```HttpRepository```, etc.) &mdash; but we can also still ensure that, in production and feature test environments, the UsersModule creates a child container which will memoize the data source instance, so that any dependencies resolved with this container will share the same instance.

## 5. Knowledge of how to build dependencies is delegated

This point is a little subtle, and is really a special case of #1, but it's worth highlighting.

Without dependency injection, we may have to pass dependencies to a class when we instantiate it (typically as arguments to the constructor).  But this requires that calling code must have some level of awareness of the nature of the dependencies of the class, increasing the degree of coupling between the class and its clients.

By using DI, not only can we delegate knowledge about *how* to construct dependencies to the container; we can also delegate the knowledge about *what* those dependencies are, thus further reducing coupling in the codebase.

## In conclusion

So to recap: dependency injection allows us to write loosely coupled, testable and reusable code irrespective of the nature of the typing sustem we have.  The one problem is that the number of JavaScript libraries providing robust DI containers is currently very limited.  Hopefully the success of AngularJS will promote greater interest in the pattern within the JavaScript community.  And in the meantime, I'm developing my own for use with a [personal project](/blog/2012/10/21/introducing-frappuccino/) of mine.