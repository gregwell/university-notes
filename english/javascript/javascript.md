# JavaScript

**Created:** 10.02.2021, **last updated:** 22.02.2021

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

### Tagged template literals

- a more advanced form of **template literals**(string literals that allow to embed expressions in them i.e. ****`string text ${expression} string text`**).**
- allow you to **parse template literals with a function.**
- The tag function can then perform whatever operations on these arguments you wish, and return the manipulated string.

**Example:**

```jsx
function highlight(strings, ...values) {
   let str = '';
   strings.forEach((string, i) => {
       str += `${string} <span class='hl'>${values[i] || ''}</span>`;
   });
   return str;
}
```

- **The first argumen**t of a tag function contains **an array of string values**.
- **The remaining arguments** are related to the expressions. (We can use ES6 rest operator to take the rest of the values that are passed to that argument, and put them into an array for us.)

```jsx
const sentence = highlight`My dog's name is ${name} and he is ${age} years old`;
```

- we changed the template literal so any value that gets passed in gets wrapped in a <span> and we can use CSS to highlight it.

```css
.hl {
background: #ffc600
}
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

# ES6 syntax:

read refactors on github to learn what has changed

[https://github.com/michalbe/md-file-tree/commit/41bafe522ca626d7627e66b937e48f3ad3c45a18](https://github.com/michalbe/md-file-tree/commit/41bafe522ca626d7627e66b937e48f3ad3c45a18)

New features:

- arrow functions
- classes
- template strings
- destructing - The **destructuring assignment** syntax is a JavaScript expression that makes it possible to unpack values from arrays, or properties from objects, into distinct variables.
- default value
- spread operator - **Spread syntax** allows an iterable such as an array expression or string to be expanded in places where zero or more arguments (for function calls) or elements (for array literals) are expected, or an object expression to be expanded in places where zero or more key-value pairs (for object literals) are expected. Example: [...iterableObj, '4', 'five', 6];
- let, const, var

# Other

### Hoisting

- Hoisting is a JavaScript mechanism where variables and function declarations are moved to the top of their scope before code execution.
- Function expressions load only when the interpreter reaches that line of code. So if you try to call a function expression before it's loaded, you'll get an error!
- If you call a function declaration instead, it'll always work, because no code can be called until all declarations are loaded.

```jsx
hoistedFunction(); // Hello! I am defined immediately!
notHoistedFunction(); // ReferenceError: notHoistedFunction is not defined

// Function Decalration
function hoistedFunction () {
  console.log('Hello! I am defined immediately!');
}

// Function Expression
const notHoistedFunction = function () {
  console.log('I am not defined immediately.');
}
```

# FP vs OOP

- Both OOP and FP have the shared goal of creating understandable, flexible programs that are free of bugs.

**OOP**:

- says that **bringing together data and its associated behavior in a single locatio**n (called an “object”) makes it easier to understand how a program works.
- Classes often used to generate object instances
- Class defines the attributes with which we want to imbue our object.
- We will give our class "*instance methods*", which will exist within our object.
- These instance methods can be called on the object itself.
- "initialize", is not an instance method, but instead tells the class what attributes it will have at the moment of it's creation.
- Every new instance of our object will contain preset **data** and **behavior**
- As noted above, data will be provided to our instance upon creation
- We then use methods on our instance object to manipulate it's data
- All of the important information our objects contain is stored safely within their classes. If you can imagine being an engineer at a fairly large company, with a lot of pre-written code, you can also see where this would come in handy.
- Core Concepts:
    - **Abstraction (focusing on necessary details)**
        - it literally means to perceive an entity in a system or context from a particular perspective.
        - We take out unnecessary details and only focus on aspects that are necessary to that context or system under consideration.
        - Abstraction is the concept of moving the focus from the details and concrete implementation of things, to the types of things (i.e. classes), the operations available (i.e. methods), etc, thus making the programming simpler, more general, and more abstract.
    - **Encapsulation (hiding details from the outer world)**
        - hiding the details of the object and providing a decent interface for the entities in outer world to interact with that object or entity.
        - For example, if someone want to know my name then he cannot directly access my brain cells to get to know what is my name. Instead that person will either ask my name.
        - encapsulation means to hide the details of implementation and only show the types of things and their more general and abstract definitions.
    - **Inheritance**
    - **Polymorphism (the ability to take on multiple forms.)**
        - the practice of designing objects to share behaviors and to be able to override shared behaviors with specific ones.
        - Polymorphism takes advantage of inheritance in order to make this happen.

**FP**

- says that **data and behavior are distinctively different things** and should be kept separate for clarity.
- Uses a series of small methods that each do their own specific job.
- **Avoiding side effects** is one of the pillars of functional programming (the other is using higher order functions).
- Implements *composition* or building of large tasks with smaller ones, which is also used in OOP languages, but it's pivotal to FP ones.
- **Objects are** **immutable**, can't be changed after being created
- Best fit for data science work
- A function is reusable
- **Core Concepts**
    - **Higher Order Functions**
        - Higher-order functions are functions that take other functions as arguments or return functions as their results.
        - higher-order functions vs callback functions

            A [higher-order function](https://en.wikipedia.org/wiki/Higher-order_function) is a function that takes another function(s) as an argument(s) **and/or** returns a function to its callers.

            A [callback function](https://en.wikipedia.org/wiki/Callback_(computer_programming)) is a function that is passed to another function with the expectation that the other function will call it.

        - **higher-order vs first class functions**
            - The “higher-order” concept can be applied to functions in general, like functions in the mathematical sense. The “first-class” concept only has to do with functions in programming languages.
            - When you say that a language has first-class functions, it means that the language treats functions as values – that you can assign a function into a variable, pass it around etc.
        - example:

            ```jsx
            const double = n => n * 2
            [1, 2, 3, 4].map(double) // [ 2, 4, 6, 8 ]
            ```

            - functions are values ( first-class citizens ). This means that they can be assigned to a variable and/or passed as a value.
        - the same code snippet without higher-order function

            ```jsx
            let array = [1, 2, 3, 4]
            let newArray = []

            for(let i = 0; n < array.length; i++) {
                newArray[i] = array[i] * 2
            }

            newArray // [ 2, 4, 6, 8 ]
            ```

    - **Pure Functions**
        - functions that **accept an input and returns a value without modifying any data outside its scope** (Side Effects).
            - The function does not produce any observable side effects such as network requests, input and output devices, or data mutation.
        - The function **always returns the same result if the same arguments are passed in**
            - It does not depend on any state, or data, change during a program’s execution. It must only depend on its input arguments.
    - **Recursion**
        - the process of defining a problem (or the solution to a problem) in terms of (a simpler version of) itself
        - **Why recursion in FP? Immutable variables**
            - Using recursion we don't need a mutable state while solving some problem, and this make possible to specify a semantic in simpler terms. Thus solutions can be simpler, in a formal sense.
            - Recursion is natural, when you operate on higher levels of abstraction. **Functional programming is not just about coding with functions**; it is about operating on higher levels of abstraction, where you naturally use functions. Using functions, it is only natural to reuse the same function (to call it again), from whatever context where it makes sense.
            - Recursion is everywhere. Any iterative loop is a recursion in disguise anyway, because when you reenter that loop, you reenter that same loop again (just with maybe different loop variables). So it's not like inventing new concepts about computing, it's more like discovering the foundations, and making it explicit.
    - **Strict & Non-Strict Evaluation**
        - the compiler throws some silent errors that were previously not thrown or ignored. Also, doesn't allow you to do certain things. Let's see what things.
        - for example
            - does not let the use of variables which have not been declared.
            - does not let to send duplicate parameters in function
            - Silent Errors are thrown
    - **Type Systems - PropTypes etc.**
    - **Referential Transparency**
        - defined as the fact that **an expression, in a program, may be replaced by its value** (or anything having the same value) without changing the result of the program
        - this implies that methods should always return the same value for a given argument, without having any other effect

**Section sources:**

- [https://dev.to/krtb/functional-vs-object-oriented-programming-357](https://dev.to/krtb/functional-vs-object-oriented-programming-357)
- [https://stackoverflow.com/questions/12659581/functional-programming-lots-of-emphasis-on-recursion-why](https://stackoverflow.com/questions/12659581/functional-programming-lots-of-emphasis-on-recursion-why)

**What people believe**

> Feels like the whole industry is moving to not deeply using OOP and preferring immutable objects and pure functions over data mutations, so this topic maybe just a tribute to old times 🤷 - [https://dev.to/kozlovzxc/js-interview-in-2-minutes-encapsulation-oop-2ico](https://dev.to/kozlovzxc/js-interview-in-2-minutes-encapsulation-oop-2ico)

> functional approaches, which are GREAT for stateless, event driven actions (IE: Your typical microservices web backend) and OOP which are much better suited for stateful, context dependent actions (Like you might see in an application designed to run independently without API support, like a desktop app or a game).

# Closures

([source](https://dmitripavlutin.com/simple-explanation-of-javascript-closures/))

- The **scope** is a space policy that rules the accessibility of variables.
    - *Scopes can be nested*
    - *The variables of the outer scope are accessible inside the inner scope*
- the **lexical scoping** means that inside the inner scope you can access variables of outer scopes.
    - *the engine determines (at lexing time) the nesting of scopes just by looking at the JavaScript source code, without executing it.*

    ```jsx
    function outerFunc() {
      // the outer scope
      let outerVar = 'I am outside!';

      function innerFunc() {
        // the inner scope
        console.log(outerVar); // => logs "I am outside!"
      }

      innerFunc();
    }

    outerFunc();
    ```

    - How engine understands this code snippet:
        1. *I can see you define a function `outerFunc()` that has a variable `outerVar`. Good.*
        - *Inside the `outerFunc()`, I can see you define a function `innerFunc()`.*
        - *Inside the `innerFunc()`, I can see a variable `outerVar` without declaration. Since I use lexical scoping, I consider the variable `outerVar` inside `innerFunc()` to be the same variable as `outerVar` of `outerFunc()`.*
- **The closure** is a function that accesses its lexical scope even executed outside of its lexical scope.

    ```jsx
    function outerFunc() {
      let outerVar = 'I am outside!';

      function innerFunc() {
        console.log(outerVar); // => logs "I am outside!"
      }

      return innerFunc;
    }

    const myInnerFunc = outerFunc();
    myInnerFunc();
    ```

    - in this code snippet **innerFunc()** is executed outside of its lexical scope:
        - innerFunc() still has access to outerVar from its lexical scope, even being executed outside of its lexical scope.
        - In other words, innerFunc() **closes over** (a.k.a. captures, remembers) the variable outerVar from its lexical scope.
        - **In other words, innerFunc() is a closure because it closes over the variable outerVar from its lexical scope.**
        - Simpler, the closure is a function that remembers the variables from the place where it is defined, regardless of where it is executed later.

    **Example (Callback)**

    ```jsx
    const message = 'Hello, World!';

    setTimeout(function callback() {
      console.log(message); // logs "Hello, World!"
    }, 1000);
    ```

    **Example (Event handler)**

    ```jsx
    let countClicked = 0;

    myButton.addEventListener('click', function handleClick() {
      countClicked++;
      myText.innerText = `You clicked ${countClicked} times`;
    });
    ```

    **Example (Functional programming: currying)**

    - **Currying** happens when a function returns another function until the arguments are fully supplied.

    ```jsx
    function multiply(a) {
      return function executeMultiply(b) {
        return a * b;
      }
    }

    const double = multiply(2);
    double(3); // => 6
    double(5); // => 10

    const triple = multiply(3);
    triple(4); // => 12
    ```

    - multiply is a curried function that returns another function.
    - Currying, an important concept of functional programming, is also possible thanks to closures.
    - executeMultiply(b) is a closure that captures a from its lexical scope. When the closure is invoked, the captured variable a and the parameter b are used to calculate a * b.

## Credits:

[https://github.com/javascript-tutorial/en.javascript.info](https://github.com/javascript-tutorial/en.javascript.info)