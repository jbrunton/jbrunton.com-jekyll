---
layout: post
title: How To Write Effective Unit Tests
tags:
    - Software Testability
    - Unit Tests
---

Following on from my post [How To Write Testable Code](/blog/2012/10/10/how-to-write-testable-code), these are the three most important rules I always try to follow when writing unit tests, to really get the most value from them.

There are other, more subtle principles worth learning about (Steven Sanderson has a [great post here](http://blog.stevensanderson.com/2009/08/24/writing-great-unit-tests-best-and-worst-practises/) which explores the importance of orthogonality, as well as highlighting many of the points that I reiterate below) &mdash; but for the most part, if I adhere to these principles, then I find the rest follow naturally.

## 1. Test each subject in isolation

Well designed units of code can and should be tested in isolation.  Any dependencies should be replaced with appropriate [test doubles](http://www.martinfowler.com/bliki/TestDouble.html), in order that your test asserts nothing about the behavior of other units of code.  Otherwise, your tests will be unnecessarily brittle, with dependencies on large amounts of code beyond the subject under test (SUT) &mdash; and failures elsewhere in the codebase will cause cascading failures across your unit tests, making it hard to determine the source of errors.

## 2. Write atomic tests

A single test which tries to assert everything it can about the SUT isn't very useful &mdash; because if one test fails it can have a domino effect on the assertions which follow it, making it hard to infer anything meaningful from the remainder of the test cases.  More problematic than that, though, is that without being extremely careful to manually tear down and setup variables and object states, it can be difficult to be sure that all passing tests are valid, because, as the state of the test subject changes through the course of the test, it's hard to be sure that the setup step correctly returns it to the state expected by each assertion.

{% highlight ruby %}
describe "#find", ->

    it "retrieves users by id", ->
        user = collection.find(1)
        expect(user).toBeDefined()
        
        user = collection.search("dave")
        expect(user).toBeDefined()
{% endhighlight %}
    
it "does this other thing", ->
    app.other_thing()
    assert(app.other_thing)

For example:

## 3. Test behaviors (not implementation details)

Suppose I'm implementing a collection of some kind:

{% highlight ruby %}
class UsersCollection
    constructor: ->
        @_users = []
 
    add: (user) =>
        @_users.push(user)
 
    remove: (user) =>
        @_users = _.without(@_users, user)
 
    get: (id) =>
        _.find @_users,
            (user) -> user.id == id
{% endhighlight %}
        
You might want to start by testing that the add method works as expected.  Here's one possible implementation:

{% highlight ruby %}
beforeEach ->
    collection = new UsersCollection
 
describe "#add", ->
 
    it "should add an element to the collection", ->
        user = new User( name: "Joe" )
        collection.add(user)
        expect(collection._users).toEqual([user])
{% endhighlight %}

The problem with this is that it tightly couples the test to the implementation of our collection.  We might later decide to use a hashmap internally, for example &mdash; but then the test will fail, because we no longer have a list called ```_users```.

A better implementation will test for the expected *behaviors* of the collection &mdash; e.g. that the element can now be retrieved through the classes interface as expected.

{% highlight ruby %}
describe "#add", ->
    it "should add an element to the collection", ->
        user = new User( id: 1, name: "Joe" )
        collection.add(user)
        expect(collection.get(1)).toBe(user)
{% endhighlight %}
     
With this approach, you can refactor your implementation as you like, safe in the knowledge that your tests will continue to pass or fail according to the intended behavior of the class.

