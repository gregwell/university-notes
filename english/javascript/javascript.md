# JavaScript

**Created:** 10.02.2021, **last updated:** 17.02.2021

Notes includes the most important paragraphs from open-source JS tutorial from this [link](https://github.com/javascript-tutorial/en.javascript.info): quite a lot of basics, the usage of promises and all other stuff that have interested me.

# Fundamentals

## An introduction

- JavaScript can execute in the browser, on the server, or on any device that has JavaScript engine (browser has an embedded engine)
- engines have their own "codenames" (V8 - Chrome, SpiderMonkey - Firefox)
- engine workflow:
    1. **parse**: read the script
    2. **compile:** convert to the machine language
    3. **run:** the compiled code runs
- scripts should be stored in separate files, then the browser will downloads it and store it in its cache; other pages that reference the same script will take it from the cache instead of downloading it, this makes pages faster.

### "?": Conditional "ternary" operator

**Single "?":**

`let result = condition ? valueIfTrue : valueIfFalse;`

**Multiple "?" (not "??")**

`let result = cond1 ? valueIfTrue : condIfCond1IsFalse ? valueIfTrue : valueIfFalse;`

### "??"Nullish coalescing operator

The result of `a ?? b` is:

- if `a` is defined, then `a`,
- if `a` isn’t defined, then `b`.

### Arrow functions

```jsx
let func = function(arg1, arg2, ..., argN) {
  return expression;
};
```

```jsx
let func = (arg1, arg2, ..., argN) => expression
```

### Using .map() to iterate through array items

- `.map()` creates an array from calling a specific function on each item in the parent array
- is useful for:
1. Calling a function on each item in an array, example:

```jsx
const sweetArray = [2, 3, 4, 5, 35]
const sweeterArray = sweetArray.map(sweetItem => {
    return sweetItem * 2
})
```

2. Converting a string to an array

```jsx
const name = "Sammy"
const map = Array.prototype.map

const newName = map.call(name, eachLetter => {
    return `${eachLetter}a`
})
```

3. Rendering lists in JavaScript libraries

4. Reformatting array objects.

## Code quality

### Debugging in Chrome

- **F8:** “**Resume**”: continue the execution, hotkey.
- **F9:** “**Step**”: run the next command, hotkey.
- **F10: “Step over”**: run the next command, but don’t go into a function, hotkey.
- **F11**: **“Step into”**: goes into async functions code, waiting for them if necessary. *(“Step” command ignores async actions, such as setTimeout (scheduled function call), that execute later. )*,
- **Shift+F11:** Continue the execution and stop it at the very last line of the current function. That’s handy when we accidentally entered a nested call using "Step", but it does not interest us, and we want to continue to its end as soon as possible.
- **no hotkey -** enable/disable all breakpoints
- **no hotkey** – enable/disable automatic pause in case of an error.
- **right click on a line of code - "**continue tu here" -handy when we want to move multiple steps forward to the line, but we’re too lazy to set a breakpoint.

### Avoid nesting:

**Example:**

```jsx
function pow(x, n) {
  if (n < 0) {
    alert("Negative 'n' not supported");
  } else {
    let result = 1;

    for (let i = 0; i < n; i++) {
      result *= x;
    }

    return result;
  }
}
```

vs. (better)

```jsx
function pow(x, n) {
  if (n < 0) {
    alert("Negative 'n' not supported");
    return;
  }

  let result = 1;

  for (let i = 0; i < n; i++) {
    result *= x;
  }

  return result;
}
```

## Objects

### Accessing a property

- The dot notation: `obj.property`.
- Square brackets notation `obj["property"]`.

### Cloning objects

- a variable stores not the “object value”, but a “reference” (address in memory) for the value. So copying such a variable or passing it as a function argument copies that reference, not the object itself.
- All operations via copied references (like adding/removing properties) are performed on the same single object.
- To make a “real copy” (a clone) we can use `Object.assign` for the so-called “shallow copy” (nested objects are copied by reference) or a “deep cloning” function, such as [_.cloneDeep(obj)](https://lodash.com/docs#cloneDeep).
- [more reading](https://javascript.info/object-copy)

### Garbage collection

The main things to know:

- Garbage collection is performed automatically. We cannot force or prevent it.
- Objects are retained in memory while they are reachable.
- Being referenced is not the same as being reachable (from a root): a pack of interlinked objects can become unreachable as a whole.

### "this" in methods

**Functions that are stored in object properties are called “methods”.**

- For instance, the code inside `user.sayHi()` may need the name of the `user`.
- **To access the object, a method can use the `this` keyword.**
- The value of this is the object “before dot”, the one used to call the method.

**In JavaScript, keyword this behaves unlike most other programming languages.** It can be used in any function, even if it’s not a method of an object, example:

```jsx
function sayHi() {
  alert( this.name );
}

// use the same function in two objects
user.f = sayHi;
admin.f = sayHi;
```

- the value of this is defined at run-time.
- **Please note that arrow functions are special: they have no this. When this is accessed inside an arrow function, it is taken from outside.**

### Optional chaining "?."

```jsx
let user = {}; // a user without "address" property
```

example:

```jsx
alert(user.address.street); // Error!
```

As `user.address`is undefined, an attempt to get `user.address.street` fails with an error.

We can write a code like this:

```jsx
alert(user.address ? user.address.street : undefined);
```

But the "?." let us do it simpler: The secure way:

```jsx
alert( user?.address?.street ); // undefined (no error)
```

**The optional chaining ?. stops the evaluation if the value before ?. is undefined or null and returns undefined.**

Other aspects:

1. **Don't overuse the optional chaining!**

write `user.address?.street`, but not `user?.address?.street` to get errors if needed (no user we expect to be error)

2. **Short-circuiting**

As it was said before, the `?.` immediately stops (“short-circuits”) the evaluation if the left part doesn’t exist.

So, if there are any further function calls or side effects, they don’t occur.

```jsx
let user = null;
let x = 0;

user?.sayHi(x++); // no "sayHi", so the execution doesn't reach x++

alert(x); // 0, value not incremented
```

3. Calling functions that may not exist

```jsx
let userAdmin = {
  admin() {
    alert("I am admin");
  }
};

let userGuest = {};

userAdmin.admin?.(); // I am admin

userGuest.admin?.(); // nothing (no such method)
```

### Symbols

`Symbol` is a primitive type for unique identifiers.

Symbols are created with `Symbol()` call with an optional description (name).

- [more reading](https://javascript.info/symbol)

### Object-to-primitive conversion

- The object-to-primitive conversion is called automatically by many built-in functions and operators that expect a primitive as a value.

There are 3 types (hints) of it:

- `"string"` (for `alert` and other operations that need a string)
- `"number"` (for maths)
- `"default"` (few operators)

The specification describes explicitly which operator uses which hint. There are very few operators that “don’t know what to expect” and use the "default" hint. Usually for built-in objects "default" hint is handled the same way as "number", so in practice the last two are often merged together.

**In practice, it’s often enough to implement only obj.toString() as a “catch-all” method for all conversions that return a “human-readable” representation of an object, for logging or debugging purposes.**

- [more reading](https://javascript.info/object-toprimitive)

## Data types and stuff

### To write numbers with many zeroes:

- Append `"e"` with the zeroes count to the number. Like: `123e6` is the same as `123` with 6 zeroes `123000000`.
- A negative number after `"e"` causes the number to be divided by 1 with given zeroes. E.g. `123e-6` means `0.000123` (`123` millionths).

### A cheatsheet of array methods:

- [here](https://javascript.info/array-methods#summary)

# Handling asynchronous tasks

## Callbacks

### Callback-based:

- a function that does something asynchronously (for instance loading a scrypt) should provide a callback argument where we put the function to run after it's complete

```jsx
function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;
  script.onload = () => callback(script);
  document.head.append(script);
}

loadScript('https://cdnjs.cloudflare.com/ajax/libs/lodash.js/3.2.0/lodash.js', script => {
  alert(`Cool, the script ${script.src} is loaded`);
  alert( _ ); // function declared in the loaded script
});
```

### Callback in callback: loading two script sequentially

- after the outer loadScript is complete, the callback initiates the inner one.

```jsx
loadScript('/my/script.js', function(script) {

  alert(`Cool, the ${script.src} is loaded, let's load one more`);

  loadScript('/my/script2.js', function(script) {
    alert(`Cool, the second script is loaded`);
  });

});
```

- that is fine for few actions, but not good for many
- we are not considering any errors

### Handling errors

```jsx
function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;

  script.onload = () => callback(null, script);
  script.onerror = () => callback(new Error(`Script load error for ${src}`));

  document.head.append(script);
}
```

- It calls `callback(null, script)` for successful load and `callback(error)` otherwise.

**"Error-first callback" style:**

```jsx
loadScript('/my/script.js', function(error, script) {
  if (error) {
    // handle error
  } else {
    // script loaded successfully
  }
});
```

The convention is:

1. The first argument of the `callback` is reserved for an error if it occurs. Then `callback(err)` is called.
2. The second argument (and the next ones if needed) are for the successful result. Then `callback(null, result1, result2…)` is called.

So the single callback function is used both for reporting errors and passing back results.

### Pyramid of doom

```jsx
loadScript('1.js', function(error, script) {

  if (error) {
    handleError(error);
  } else {
    // ...
    loadScript('2.js', function(error, script) {
      if (error) {
        handleError(error);
      } else {
        // ...
        loadScript('3.js', function(error, script) {
          if (error) {
            handleError(error);
          } else {
            // ...continue after all scripts are loaded (*)
          }
        });

      }
    });
  }
});
```

- As calls become more nested, the code becomes deeper and increasingly more difficult to manage, especially if we have real code instead of ... that may include more loops, conditional statements and so on.

```jsx
loadScript('1.js', step1);

function step1(error, script) {
  if (error) {
    handleError(error);
  } else {
    // ...
    loadScript('2.js', step2);
  }
}

function step2(error, script) {
  if (error) {
    handleError(error);
  } else {
    // ...
    loadScript('3.js', step3);
  }
}

function step3(error, script) {
  if (error) {
    handleError(error);
  } else {
    // ...continue after all scripts are loaded (*)
  }
}
```

- It does the same, and there’s no deep nesting now because we made every action a separate top-level function.
- The problem is that it is difficult to read, moreover **all of these functions are single use,** created only to avoid the "pyramid of doom".
- We’d like to have something better... one of the best ways is to use "promises"

## Promises

### How does it work

1. A “producing code” that does something and takes time.
2. A “consuming code” that wants the result of the “producing code” once it’s ready.
3. The “producing code” takes whatever time it needs to produce the promised result, and the “promise”(a special JavaScript object that links the “producing code” and the “consuming code” together.) makes that result available to all of the subscribed code when it’s ready.

```jsx
let promise = new Promise(function(resolve, reject) {
  // executor (the producing code, "singer")
});
```

**function -** is called **"executor**" - our code is inside it

**resolve** and **reject -** are callbacks provided by JavaScript itself

### Promise action flow

1. Executing **new Promise. (**Creating promise object)
2. Executor runs automatically, obtains the result (doesn't matter when) and calls one of the callbacks:
    1. Scenario 1. Job successful: calling **resolve(`value`) -** with result **`value`**
    2. Scenario 2. An error occurred: calling **reject(`error`) -**  with the **`error`** object
3. **new Promise constructor** returns the **promise** object. The newly created object has these properties:
    1. `state` - initially "**pending**" 
        1. when resolve is called changes to "**fulfilled**"
        2. when reject is called changes to "**rejected**"
    2. `result` - initially `undefined`
        1. when resolve is called changes to `value` (the argument)
        2. when reject is called changes to `error` (the argument)

### Rules

- the executor should call only one resolve or one reject. **Any state change is final.**
- the resolve/reject expect only one argument (or none).
- it's recommended to use `Error` objects as return argument from **reject.**
- technically, we can **resolve** or **reject** immediately - that's fine
- **the properties `state` and `result` are internal - we cal only access them by .then, .catch, .finally**

### Consumers

- consuming functions can be registered using methods .then, .catch, .finally
- **if the promise is pending handlers wait for it, when has already settled they just run.**

### Consumers: .then

```jsx
promise.then(
  function(result) { /* handle a successful result */ },
  function(error) { /* handle an error */ }
);
```

- the first argument is a function that runs when the promise is resolved and receives the result, the second argument when the promise is rejected and received the error.

```jsx
let promise = new Promise(function(resolve, reject) {
  setTimeout(() => resolve("done!"), 1000);
});

// resolve runs the first function in .then
promise.then(
  result => alert(result), // shows "done!" after 1 second
  error => alert(error) // doesn't run
);
```

- we can provide only one function argument, for instance:

```jsx
let promise = new Promise(resolve => {
  setTimeout(() => resolve("done!"), 1000);
});

promise.then(alert); // shows "done!" after 1 second
```

### Consumers: .catch

If we're interested only in errors, then we can:

1. Possibility one: use `null` as the first argument in **.then**

    ```jsx
    .then(null, errorHandlingFunction)
    ```

2. Possibility two: use **.catch**

    ```jsx
    .catch(alert);
    ```

### Consumers: .finally

- **always runs when the promise is settled (**resolved or rejected - doesn't matter)
- it's a good handler for performing cleanup, e.g. **stopping loading indicators,** as they are not needed anymore, **no matter what the outcome is.**
- .finally **has no arguments** - we don't know whether the promise is successful or not. That makes it ideal to perform "general" finalizing procedures.
- is not meant to process a promise result so it **passes through results and errors to the next handler e.g:**

    ```jsx
    new Promise((resolve, reject) => {
      setTimeout(() => resolve("result"), 2000)
    })
      .finally(() => alert("Promise ready"))
      .then(result => alert(result)); // <-- .then handles the result
    ```

    ```jsx
    new Promise((resolve, reject) => {
      throw new Error("error");
    })
      .finally(() => alert("Promise ready"))
      .catch(err => alert(err));  // <-- .catch handles the error object
    ```

### Example:

Callback-powered:

```jsx
function loadScript(src, callback) {
  let script = document.createElement('script');
  script.src = src;

  script.onload = () => callback(null, script);
  script.onerror = () => callback(new Error(`Script load error for ${src}`));

  document.head.append(script);
}
```

Promise-powered:

```jsx
function loadScript(src) {
  return new Promise(function(resolve, reject) {
    let script = document.createElement('script');
    script.src = src;

    script.onload = () => resolve(script);
    script.onerror = () => reject(new Error(`Script load error for ${src}`));

    document.head.append(script);
  });
}
```

- **The outer code can add handlers (subscribing functions) to it using .then:**

```jsx
let promise = loadScript("https://cdnjs.cloudflare.com/ajax/libs/lodash.js/4.17.11/lodash.js");

promise.then(
  script => alert(`${script.src} is loaded!`),
  error => alert(`Error: ${error.message}`)
);

promise.then(script => alert('Another handler...'));
```

### Few more words..

- **promises are more than just callbacks.** They are a very mighty abstraction, allow cleaner and better, functional code with less error-prone boilerplate.
    - **promises allow us to do things in the natural order, first we run loadScript(script), and .then we write what to do with the result.** When using callbacks we must know what to do with the result before loadScript is called.
    - **we can call .then on a promise as many times as we want.** Each time we're adding a new "fan", a new subscribing function, to the "subscription list". When using callback we don't have this possibility - we can have only one callback.
- the main idea behind promises is providing a direct correspondence between synchronous functions and asynchronous functions.
- promises are objects representing the result of a single (asynchronous) computation.
- promises ****implement an observer pattern.
- instead of expecting callbacks as arguments to your functions you can easily `return` a Promise object
- the promise will store the value, and you can transparently add a callback whenever you want, it will be called when the result is available.
- "Transparency" implies that when you have a promise and add a callback to it, it doesn't make a difference to your code whether the result has arrived yet - the API and contracts are the same, simplifying caching/memoisation a lot.
- you can add multiple callbacks easily

### Promises chaining

- the idea is that the **result** is passed through the chain of **.then** handlers.
- the whole thing works, because a call to promise**.then** returns a promise, so that we can call the next .then on it.
- technically we can also add many **.then** to a single promise. **This is not chaining.** They don’t pass the result to each other; instead they process it independently. In practice we rarely need multiple handlers for one promise. **Chaining is used much more often.**
- in front-end promises are often used for network requests

**Example: fetch**

```jsx
fetch('/article/promise-chaining/user.json')
  .then(function(response) {
    return response.text();
  })
  .then(function(text) {
    alert(text); 
  });
```

1. Fetch method makes a network request to the URL **and returns a promise.**
2. **The promise resolves with a `response` object** when the remote server responds with headers, but **before the full response is downloaded.**
3. When the remote server responds then the first **`.then`** runs
4. **`.then`** calls **`response.text()`** method that returns **a promise which resolves with a `text` object** when the full text is downloaded
5. The next `.then`**,** when receive the `text` object is simply printing it as an alert.

**Doing it better:**

- the response method returned from fetch also includes the method **`response.json()`**that parses the remote data as JSON. We can switch to it.
- we can also use arrow functions for brevity

```jsx
fetch('/article/promise-chaining/user.json')
  .then(response => response.json())
  .then(user => alert(user.name)); 
```

**Using the user name and showing GitHub avatar:**

```jsx
fetch('/article/promise-chaining/user.json')
  .then(response => response.json())
  .then(user => fetch(`https://api.github.com/users/${user.name}`))
  .then(response => response.json())
  .then(githubUser => {
    let img = document.createElement('img');
    img.src = githubUser.avatar_url;
    img.className = "promise-avatar-example";
    document.body.append(img);

    setTimeout(() => img.remove(), 3000); // (*)
  });
```

1. Fetch makes the request for user.json
2. Then `.then` loads it as json
3. Then next `.then` uses fetch to fetch user data from the GitHub API.
4. Then next `.then` loads the response as json
5. Then next `.then` shows the avatar image for 3 seconds
    1. Create a HTML element with the `img` tag
    2. Set `img.src` property as `githubUser.avatar_url`
    3. Set `img.className` as `promise-avatar-example` to apply predefined styling class.
    4. Append this img element to the document body.
    5. Set timeout to 3000 ms to remove this img element.

**The problem:** The current chain is not extendable, it does not return a promise, so we can't do anything after the avatar has finished showing and gets removed.

**The solution:** Return a promise and resolve with a value - `githubUser` object.

```jsx
fetch('/article/promise-chaining/user.json')
  .then(response => response.json())
  .then(user => fetch(`https://api.github.com/users/${user.name}`))
  .then(response => response.json())
  .then(githubUser => new Promise(function(resolve, reject) { // (*)
    let img = document.createElement('img');
    img.src = githubUser.avatar_url;
    img.className = "promise-avatar-example";
    document.body.append(img);

    setTimeout(() => {
      img.remove();
      resolve(githubUser); // (**)
    }, 3000);
  }))
  .then(githubUser => alert(`Finished showing ${githubUser.name}`));
```

1. The `.then` handler used previously to use the githubUser json data to show the image, now is used to create new promise.
2. `resolve` the promise inside `setTimeout` function, so the new promise is settled only after the call of `resolve(githubUser)` 
3. The next `.then` in the chain will wait for that.

**The golden rule:**

> As a good practice, an asynchronous action should always return a promise. That makes it possible to plan actions after it; even if we don’t plan to extend the chain now, we may need it later.

**Splitting the code into reusable functions:**

```jsx
function loadJson(url) {
  return fetch(url)
    .then(response => response.json());
}

function loadGithubUser(name) {
  return fetch(`https://api.github.com/users/${name}`)
    .then(response => response.json());
}

function showAvatar(githubUser) {
  return new Promise(function(resolve, reject) {
    let img = document.createElement('img');
    img.src = githubUser.avatar_url;
    img.className = "promise-avatar-example";
    document.body.append(img);

    setTimeout(() => {
      img.remove();
      resolve(githubUser);
    }, 3000);
  });
}

// Use them:
loadJson('/article/promise-chaining/user.json')
  .then(user => loadGithubUser(user.name))
  .then(showAvatar)
  .then(githubUser => alert(`Finished showing ${githubUser.name}`));
  // ...
```

1.  `loadJson` runs fetch method, this method returns a promise with the response, then `.then` is called and the response is loaded as json.
2. Then user in json format is loaded as an argument to `loadGithubUser(user.name)`, then we run `fetch` method to load github user data and `.then` load it as json.
3. `.then` showAvatar function is executed and returns a new promise, that is resolved after showing the avatar for 3 seconds.
4. `showAvatar`is resolved with `githubUser` value(object).  `.then` uses this object to execute alert.

## Credits:

[https://github.com/javascript-tutorial/en.javascript.info](https://github.com/javascript-tutorial/en.javascript.info)
