---
layout: post
title: Why Dependency Injection Matters In Dynamic Languages
tags:
    - Software Design
    - Dependency Injection
---

One of the foundational principles of good software design is that code should be loosely coupled, and there are few better ways to achieve this in the world of object-oriented programming than with dependency injection (DI).  Typically implemented with a *dependency injection container* - an object which manages and resolves concrete dependencies for client classes - dependency injection allows classes to be written without tying them to any specific concrete types or instances.

As a pattern, DI is well established in a number of frameworks for statically typed environments (such as Microsoft's [Unity](http://unity.codeplex.com/) container for .NET applications, or the lightweight [Picocontainer](http://picocontainer.codehaus.org/) for Java).  But do we really need to use the pattern in dynamic languages, such as JavaScript, when the concept of static types doesn't really apply?

The answer is: almost definitely.  Let's take a look at the ways in which DI helps us write robust, loosely coupled applications.

## 1. Resolve concrete types at runtime

This is one of the most obvious benefits of a DI container in a statically typed language - the container is configured to map a particular interface to a specific concrete implementation at runtime.

As an example, suppose we're writing a .NET application, and in order to abstract data access we have the following IDataRepository interface:

{% highlight csharp %}
public interface IDataRepository {
    IEnumerable<IUser> users();
    IEnumerable<IAccount> accounts();
}
{% endhighlight %}

Our WPF client application might communicate with our server over some RESTful web services.  So we have an HttpRepository concrete implementation:

{% highlight csharp %}
public class HttpRepository {
    public IEnumerable<IUser> users() {
        // makes a web request and returns the results
    }
    
    public IEnumerable<IAccount> accounts() {
        // ditto
    }
}
{% endhighlight %}

We can now map the IDataRepository interface to the correct class at runtime when we configure the container:

{% highlight csharp %}
public class Bootstrapper extends UnityBootstrapper {

    protected override void ConfigureContainer()
    {
        base.ConfigureContainer();
    
        this.RegisterTypeIfMissing(typeof(IDataRepository),
            typeof(HttpRepository));
    }

}
{% endhighlight %}

And now when we resolve an instance of IDataRepository, we'll get back an instance of HttpRepository, as required.  If we want to configure the container differently for a server application (to map to an OracleRepository, say), then it's one line of code to change.

Obviously this advantage is only relevant to statically typed languages, so we see no benefit here when using dynamic languages.

## 2. Delegate configuration of the container to the appropriate unit of code.

Without dependency injection, every time you add a dependency to a class you have to locate all the feature tests which involve that class in some way and add in suitable test doubles; and every time you write a new test, you have to figure out all of the dependencies across all of the classes involved, and ensure they're suitably mocked.  In other words, there's a tight coupling between each test and the dependencies of the subject under test (SUT) - not just with the SUT itself.  Here's an example of what I mean:

{% highlight ruby %}
describe "Application", ->

    beforeEach: ->
        repository = new TestRepository
        renderer = new TestRenderer        
        # etc.
        
        app = new Application(repository)
    
    it "is awkward to configure the environment for testing", ->
        app.run()
{% endhighlight %}


If we adopt this approach, our tests will become increasingly difficult to write and maintain as the complexity of our codebase grows.

A better approach is to delegate configuration of the container to individual application modules.  Here's an example:

{% highlight ruby %}
class RepositoryModule

    configure_container: (container, env) ->
        if env == "production"
            container.register_class "DataRepository", HttpRepository
        else
            container.register_class "DataRepository", MemoryRepository
{% endhighlight %}

Now, if we implement our application to configure each module before initializing it, we can boot up our application

And now, to configure and run our entire application for testing, our code is as simple as this:

{% highlight ruby %}
beforeEach: ->
    bootstrapper = new Bootstrapper( mode: "test" )
    app = new application
    
define "Application", ->

    it "is really easy to run in a test environment", ->
        app.run(bootstrapper)
{% endhighlight %}

This removes the coupling between our tests and the dependencies of the SUT - which allows us to write much more robust and succinct tests.

## 3. Remove dependencies on multiplicity of a class.

Do we want our data source to be shared across the entire application?  Do we want each client unit of code to instantiate its own instance?  Often, the answer is contextual: we might want to share a database connection for performance reasons in one application; but we might want to create a new repository each time we need data access in another, in order to avoid stale data in our repository class.

If we use dependency injection, then clients of our data repository don't have to know for sure whether to instantiate a new instance, or whether to share an existing one.  If we wish to share an instance, we can configure our container to memoize the repository it resolves, so that the same instance will be returned on subsequent calls to resolve:

    container.register_class "DataRepository", OracleRepository, singleton: true
    
This helps us further reduce dependencies in our code.  And removing this particular dependency is likely to help us write more robust tests as well - because it's awkward to test singletons or monad objects - and by removing the expectation that a particular dependency should always be a single instance, we can remove that requirement in tests as well.
        
## 4. Use different containers across a single application

It's often useful to be able to configure several distinct containers to resolve mappings differently across an application.  As an example, suppose we want each application module to keep its own open connection to the database.



UsersModule

    @dependency repository: "DataRepository"

    configure_container: (container) ->
            container.child().register_instance "DataRepository",
                container.resolve "DataRepository"

    find: (id) ->
        @repository.find("user", id)

    

## 5. Knowledge of how to build dependencies is delegated to 

There are a few obvious benefits to resolving dependencies dynamically:

1.  For testing purposes we can configure our DI container to resolve mappings to [test doubles](http://www.martinfowler.com/bliki/TestDouble.html) (i.e. stubs/mocks/spies/fake objects).

2.  If our code is to be reused across different platforms or frameworks, then we can configure our container appropriately for each.  For example, JavaScript code on the client might have a data repository configured to make HTTP requests to a RESTful API, while server code might have a repository which accesses a database directly.

3.  By allowing the task of resolving dependencies to fall on a separate container, we can reduce dependencies both on specific types, and also the multiplicity of those types (i.e. whether or not we should be referring to singleton/memoized instances) - which makes our code more robust, and easier to test in isolation.

To see why this is useful, consider the following (slightly contrived) class which represent users in an application:

{% highlight ruby %}
class UserCollection

    constructor: ->
        @_repository = new HttpRepository
        @_items = {}

    _success_handler: ( data ) =>
        @items[data.id] = data
        
    _success_handler: ( opts ) =>
        
        
    find: ( id ) ->
        return @_items unless 
        @repository.find "user", id,
            success: ( data ) ->
                self.items[data.id] = new User( data )
                
        
{% endhighlight %}
        
Instantiation of the ```User``` class would look something like this:

{% highlight ruby %}
user = new User(app.api.url, auth_user_id)
{% endhighlight %}

There are some obvious problems here, caused by the tight coupling between the User class and the HttpRepository:

1. The User class has to know which repository class to instantiate, which makes testing awkward (in statically-typed languages more so than in dynamic ones - but even in JavaScript or CoffeeScript we still have to take care to mock 


Here's how we might write the example using a dependency injection container:

{% highlight ruby %}
class User

    @dependency repository: "DataRepository"
    
    constructor: (@id) ->
    
    load: ->
        @repository.load( "user", @id )
        
# configure the container at startup
container.register_class "DataRepository", HttpRepository
container.register_class "User", User

# and here's how we use it:
user = container.resolve "User", auth_user_id
{% endhighlight %}
    
This is much neater.  Now when we come to write our unit tests we can configure the container appropriately - with a fake (in-memory) data repository for feature tests, say, and a mocked repository for unit tests.

Finally, suppose we wish to reuse our User class in our server application.  This time we want to make a connection to the database - but we want to reuse the connection across all instances of User.  With our container, it's easy - we just specify that the repository should be memoized:

{% highlight coffeescript %}
container.register_class "DataRepository", DBRepository, singleton: true
{% endhighlight %}
    
And now, once the repository has been instantiated, the same instance will be returned each and every time we resolve for a DataRepository.
