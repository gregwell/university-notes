# TDD & writing good tests

**created:** 5.03.2021, **last updated:** 5.03.2021

> ***Murphy’s Law of Debugging**: The thing you believe so deeply can’t possibly be wrong so you never bother testing it is definitely where you’ll find the bug after you pound your head on your desk and change it only because you’ve tried everything else you can possibly think of.*

### Set of rules:

1. Write a "single" unit test describing an aspect of the program.
2. Run the test, which should fail because the program lacks that feature.
3. Write "just enough" code, the simplest possible, to make the test pass.
4. "Refactor" the code unit it conforms to the simplicity criteria.
5. Repeat, "accumulating" unit tests over time.

### Typical individual mistakes:

1. Forgetting to run tests frequently.
2. Writing too many tests at once.
3. Writing tests that are too large.
4. Writing overly trivial tests, for instance omitting assertions.
5. Writing tests for trivial code, for instance accessors

### Skills:

1. "Test driven bug fixing". When a defect is found, writing a test exposing the defect before correction.

### Write better code thanks to TDD:

([link](https://medium.com/javascript-scene/tdd-changed-my-life-5af0ce099f80))

- **Testing should be done independently of the rest of the application**
    - unit tests force you to test components in isolation from each other, and from I/O.
    - Given some input, the unit under test should produce some known output. If it doesn’t, the test fails. If it does, it passes. The key is that it should do so independent of the rest of the application.
        - If you’re testing state logic, you should be able to test it without rendering anything to the screen or saving anything to a database.
        - If you’re testing UI rendering, you should be able to test it without loading the page in a browser or hitting the network.
- **Keep display components and container components separate in React**
    - keep UI components as minimal as you can.
    - isolate business logic and side-effects from UI.
    - For **display components**, given some props, always render the same state.
        - those components can be easily unit tested to be sure that props are correctly wired up and that any conditional logic in the UI layout works correctly:
            - for example: maybe a list component shouldn’t render at all if the list is empty, and it should instead render an invitation to add some things to the list

### How unit tests are used:

([link](https://medium.com/javascript-scene/what-every-unit-test-needs-f6cd34d9836d))

- **Design aid:** written during design phase, *prior to implementation*.
- **Feature documentation & test of developer understanding:** The test should provide a *clear description of the feature being tested.*
- **QA/Continuous Delivery:** The tests should halt the delivery pipeline on failure and *produce a good bug report when they fail.*

### The most important assertion

*`equal()`*, by nature answers **the two most important questions every unit test must answer**, but most don’t:

- What is the actual output?
- What is the expected output?

### Assign values to variables called `actual` and `expected`

- it makes tests easier to read

### Questions every unit test should answer

1. What component aspect are you testing?
2. What should it do?
3. What is the actual output?
4. What is the expected output?
5. How can the test be reproduced?
    - this question is answered by the code used to derive the `actual` value.
        - note that the `actual` value must be produced by exercising some of the component’s public API.

```jsx
import test from 'tape';

test('What component aspect are you testing?', assert => {
  const actual = 'What is the actual output?';
  const expected = 'What is the expected output?';

  assert.equal(actual, expected,
    'What should the feature do?');

  assert.end();
});
```

For each unit test you write, answer these questions. 

### A unit test example:

```jsx
import test from 'tape';
import compose from '../source/compose';

test('Compose function output type', assert => {
  const actual = typeof compose();
  const expected = 'function';

  assert.equal(actual, expected,
    'compose() should return a function.');

  assert.end();
```

### Saved links:

[https://medium.com/javascript-scene/rethinking-unit-test-assertions-55f59358253f](https://medium.com/javascript-scene/rethinking-unit-test-assertions-55f59358253f)

[https://medium.com/javascript-scene/mocking-is-a-code-smell-944a70c90a6a](https://medium.com/javascript-scene/mocking-is-a-code-smell-944a70c90a6a)

[https://medium.com/javascript-scene/streamline-code-reviews-with-eslint-prettier-6fb817a6b51d](https://medium.com/javascript-scene/streamline-code-reviews-with-eslint-prettier-6fb817a6b51d)

[https://medium.com/javascript-scene/tdd-the-rite-way-53c9b46f45e3](https://medium.com/javascript-scene/tdd-the-rite-way-53c9b46f45e3)