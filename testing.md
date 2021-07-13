# Testing

## **Simple Testing Can Prevent Most Critical Failures**

> **... for the most catastrophic failures, almost all of them are caused by incorrect error handling, and 58% of them are trivial mistakes or can be exposed by statement coverage testing.**
>
> [**acm.org/citation.cfm?id=2685068**](http://dl.acm.org/citation.cfm?id=2685068)

## Don't use **Mocha's** `this` **context**

> Why? Easier to understand and maintain, especially with more complex tests

```javascript
// bad
describe("createSlackAlertEmitter", function() {
    this.mockA;
    it("should create a new notifier", function() {
        test(this.mockA);
    });
});

// good
describe("createSlackAlertEmitter", function() {
    const mockA;
    it("should create a new notifier", function() {
        test(mockA);
    });
});
```

## Don't mock what you don't own

> It decouples the production code from code that we don't have control over,  
> so when the library changes no test fails although the code does not work anymore.

```javascript
// bad
it("should call amqplib.publish", function(done) {
    const channel = stub();
    channel.publish = stub().callsSecondArgWith(null);

    const connection = stub();
    connection.channel = stub().callsFirstArgWith(null, channel);

    const amqplib = stub();
    amqplib.connect = stub().callsFirstArgWith(null, connection);

    // ... do stuff with amqplib
});
```

> ### Good
>
> 1. Create an adapter for the library and thoroughly test the adapter against an actual RabbitMq instance.
> 2. If you're positive that the adapter works correctly, you own it.
> 3. You can now happily mock it in other tests

## Don't monkey patch

> 🚧 This section is a stub. Please help by expanding it!
>
> If it is necessary to replace just a part of the SUT to properly test,  
> then the design is not very good and should be improved instead of working around obvious limitations.

## Good Fixtures

Fixtures have to be close to what could be expected in production. They have to focus on edge cases for the unit of code under test and it has to be simple to work with them when writing tests, code and debugging.

Bad:

```javascript
const PRODUCT = {
  id: 1,
  brand: 1,  // If values are reused, reading output, error messages and debugging take longer
  name: "foo", // Values must be close to production
  category: "bar"
}
```

Good:

```javascript
const PRODUCT = {
  id: 10001,
  brand: 2441,  // If values are reused, reading output, error messages and debugging take longer
  name: "product name 10001", // Values must be close to production
  category: "my one and only brand"
}
```

## Don't reuse

> 🚧 This section is a stub. Please help by expanding it!
>
> reusing stuff creates side effects that could make tests non deterministic and therefore not very helpful.  
> When testing against a database it can make sense for performance reasons to reuse DB connections.  
> Be careful though to reset database contents.

## Watch every test fail at least once

> 🚧 This section is a stub. Please help by expanding it!
>
> The main goal of tests is to alert if something does not work anymore,  
> so we should make sure that a test is able to fail and fail for a specific reason to make that test meaningful.

## Don't test code, test requirements

> 🚧 This section is a stub. Please help by expanding it!
>
> If you have to change the test when changing the implementation, it is not very helpful  
> to make sure the stuff we forgot about still works.

## If writing tests is not easy and obvious the code needs improvement

If the code in question is coupled to some other functionality or state, then it's not easy to test it, which means we need to first make the code "testable" then write tests for it  
Bad:

```javascript
const a = 12;
const addNumbers = (b) => {
  return a + b;
}

const result = addNumbers(20); // 32
```

addNumbers is not pure and it's bound to the global scope, then we need to make it testable  
Good:

```javascript
const a = 12;
const addNumbers = (b + c) => {
  return b + c;
}

const result = addNumbers(20, a); // 32
```

Now with the refactored function we can write proper test while the functionality of this function no longer depends on the global state

