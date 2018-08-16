# Front End Testing with Jest

## Intro

Front End Testing is difficult. The reason it is difficult is because a typical piece of front end code has to coordinate HTML (the DOM), CSS (styling rules in the DOM), and JavaScript. It is fraught with side-effects, global state, and tenous interfaces by its very nature. It often runs in asynchronous functions making race conditions likely and test setup difficult. Every runtime is different, your CI environment (probably running in nodeJS) will be missing functions that browser have available or vice versa.

Systems like React, have made this story much better by moving everything into one place (JavaScript). But we still have tons of code that is not written in React and we should try to have some basic mental model for testing that type of front end.

## Testing pyramid

There is a useful mental model called the [testing pyramid](https://martinfowler.com/articles/practical-test-pyramid.html). In this model, you imagine a spectrum for your test suite:

```
Fast tests          -----> Slow tests
More isolated tests -----> More integrated tests
```

The idea is that you should have a test suite composed roughly as follows:

1. Have a test suite that covers the entire spectrum
2. Have more tests at the Fast/Isolated side
3. Have fewer tests at the Slow/Integrated side

This spread creates a pyramid shape and you can roughly carve out three main sections:

1. Unit tests (Fast/Isolated)
2. Service tests
3. End to End tests (Slow/Integrated)

## Approach for front end tests

We can use this mental model on a zoomed in scale to think about testing our UI. We will add one type of test for UI components because that is a common thing.

1. Unit tests = Test JS functions that have no side effects. Write as many as we want!
2. UI Component test (Unit) = Test JS that interacts with known DOM structure (UI component). These tests will mostly provide documentation and **won't guarantee safety**.
3. Service tests = Some tests that verify big pieces work together as expected.
4. End to End tests = A few tests that make sure your JS all loads properly and works together with the real DOM and CSS. **adds safety we don't get in Component tests!**

## Gotchas with front end testing

* You might easily introduce flakey tests when testing async functions. For example, a Promise is a value. The work inside the Promise may or may not happen. Tests might be green when they return the Promise object even if the work inside the promise did not happen as expected.
* You might change your HTML breaking the app, and still have a passing UI Component test. This is because UI Component tests often use a DOM fixture to exercise their behaviour.

## Using Jest

Let's look at examples using the [Jest](https://jestjs.io/) testing framework.

* Basics - running a test, where to put the test, assertion style, setup and teardown
  * `yarn test` - full test suite
  * `yarn run jest find.test.js` - run a file. You can use a pattern like `yarn run jest find`
* Using mocks
* Async example
* Demo of Jest snapshots
