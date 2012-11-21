---
layout: post
title: How To Write Testable Code
tags:
    - Software Design
    - Software Testability
    - Unit Tests
---

In my post [Principles of Good Software Design](/blog/2012/10/08/principles-of-good-software-design), I outlined the principles which I think underpin all good code.  This post continues the theme (reiterating a few of the same points), but from the point of view of what it takes to write code which is easily testable.

For clarity, I should point out that I'm using *testable* in this post to mean *unit-testable*.  Writing *feature tests* and *integration tests* throws up different challenges &mdash; but if we write our code to be unit testable then we'll be in a much better start place to implement these other kinds of tests later.

## What are we aiming for?

It's worth quickly considering what it means for code to be *easily testable*, so that we're clear about what our goals (and the advantages of testing code) are:

* **High test coverage**: we should be testing every discrete unit of code &mdash; that is, for every problem we solve in code, we want to test that we've solved it correctly.  Our unit tests will therefore give us a good indicator of the health of our whole codebase (not necessarily that it's totally free from bugs, but that each unit of code appears to behave as expected).
* **Robust tests**: failures should highlight incorrect code (not merely changes to code, as is sadly often the case); and a single failure should not cascade across other test suites.
* **Easy to write tests**: writing new tests for new or modified code should be a breeze.  There should be minimal setup, and it should be easy to understand what to test and how to test it.

Here, then, is what I strive for when writing application code, in order that it should meet the above criteria.

## 1. Solve each problem in isolation

In OOP terms, avoid [God classes](http://en.wikipedia.org/wiki/God_object).  In functional terms, prefer small, composable functions over lengthy and complicated ones.  Whatever paradigm you're working with: each unit of code should solve one problem.

The reason for this is simple: god classes (or god functions) are "black box" objects.  They either work or they don't, but the component pieces of their implementations are hidden from site, so can't be tested.  (Neither can they be [reused or easily reasoned about](/blog/2012/10/08/principles-of-good-software-design), but that's not the criticism we're concerned about here.)

## 2. Avoid singletons/monads

It's desirable for us to keep our tests atomic (see [How To Write Effective Unit Tests](/blog/2012/10/12/how-to-write-effective-unit-tests/)).  To see why, suppose we have a single monad instance of our application:

	describe "window.app", ->
	
	    it "does this one thing when initialized", ->
	        app.init()
	        expect(app.one_thing).toBe(whatever)
	        
	    it "does this other thing when initialized", ->
	        app.init() # already initialized
	        expect(app.other_thing).toBe(whatever)
        
Because it's a monad, we can't reset the state of the ```app``` object between tests.  What happens if we try to initialize the app object twice?  It might be safe.  It might error.  It might leave the object in an invalid state.  We can't be sure, so our second test might fail just because our ```init()``` method isn't idempotent (rather than because of a genuine bug).

Better to be able to instantiate an application for each test:

	describe "Application", ->
	
	    beforeEach: ->
	        app = new Application
	        
	    it "does this one thing when initialized", ->
	        app.init()
	        expect(app.one_thing).toBe(whatever)
	        
	    it "does this other thing when initialized", ->
	        app.init() # new instance - safe to initialize
	        expect(app.other_thing).toBe(whatever)

## 3. Make it easy to configure and run your test subjects

What this typically comes down to is: write loosely coupled code, and be careful with how you specify dependencies.

What you want to avoid is the situation where every test requires every dependency of the subject under test (SUT) to be explicitly replaced with some suitable test double.  For example, this is bad:

    beforeEach: ->
        # figure out and double every freaking last dependency
        repository = new TestRepository
        renderer = new TestRenderer        
        # etc.
        
        app = new Application(repository, renderer, ...)
    
    it "is awkward to configure the environment for testing", ->
        app.run()


Code like this typically requires that every time you add a dependency to a class, first you have to locate all the feature tests which involve that class, and then add in suitable test doubles; and every time you write a new test, you have to figure out all of the dependencies across all of the classes involved, and ensure they're suitably doubled too.  In other words, there's a tight coupling between each test and the dependencies of the subject under test (SUT) - not just with the SUT itself.

To avoid this, we should consider:

* Inversion of control and dependency injection patterns (see [Why Dependency Injection Matters In Dynamic Languages](/blog/2012/10/16/why-dependency-injection-matters-in-dynamic-languages/)), so that dependencies can be more easily configured for the appropriate environment.
* Some boilerplate code for our tests, to quickly and DRY-ly setup and tear down test subjects.
