# JavaScript

**Created:** 10.02.2021, **last updated:** 11.02.2021

Notes includes the most important paragraphs from open-source JS tutorial from this [link](https://github.com/javascript-tutorial/en.javascript.info) and other topics that have interested me.

## An introduction

JavaScript can execute not only in the browser, but also on the server, or actually on any device that has a special program called the JavaScript engine.

The browser has an embedded engine sometimes called a “JavaScript virtual machine”.

### **Different engines have different “codenames”**. For example:

- [V8](https://en.wikipedia.org/wiki/V8_(JavaScript_engine)) – in Chrome and Opera.
- [SpiderMonkey](https://en.wikipedia.org/wiki/SpiderMonkey) – in Firefox.
- …There are other codenames like “Chakra” for IE, “ChakraCore” for Microsoft Edge, “Nitro” and “SquirrelFish” for Safari, etc.

For instance, if **“a feature X is supported by V8”, then it probably works in Chrome and Opera**.

### **How do engines work?**

Engines are complicated. But the basics are easy.

1. The engine (embedded if it’s a browser) reads (“parses”) the script.
2. Then it converts (“compiles”) the script to the machine language.
3. And then the machine code runs, pretty fast.

### **In-browser JavaScript is able to:**

- Add new HTML to the page, change the existing content, modify styles.
- React to user actions, run on mouse clicks, pointer movements, key presses.
- Send requests over the network to remote servers, download and upload files (so-called [AJAX](https://en.wikipedia.org/wiki/Ajax_(programming)) and [COMET](https://en.wikipedia.org/wiki/Comet_(programming)) technologies).
- Get and set cookies, ask questions to the visitor, show messages.
- Remember the data on the client-side (“local storage”).

### **What CAN’T in-browser JavaScript do?**

- JavaScript on a webpage may not read/write arbitrary files on the hard disk, copy them or execute programs. It has no direct access to OS functions. Modern browsers allow it to work with files, but the access is limited and only provided if the user does certain actions, like “dropping” a file into a browser window or selecting it via an <input> tag.
- Different tabs/windows generally do not know about each other. Sometimes they do, for example when one window uses JavaScript to open the other one. But even in this case, JavaScript from one page may not access the other if they come from different sites (from a different domain, protocol or port).

Such limits do not exist if JavaScript is used outside of the browser, for example on a server. Modern browsers also allow plugin/extensions which may ask for extended permissions.

### **Why shouldn't you put scripts into HTML**

As a rule, only the simplest scripts are put into HTML. More complex ones reside in separate files.

The benefit of a separate file is that the browser will download it and store it in its [cache](https://en.wikipedia.org/wiki/Web_cache).

Other pages that reference the same script will take it from the cache instead of downloading it, so the file is actually downloaded only once.

That reduces traffic and makes pages faster.

### Question mark operator "?"

**Single "?":**

`let result = condition ? value1 : value2;`

**Multiple "?" (not "??")**

The condition is evaluated: if it’s truthy then value1 is returned, otherwise – value2.

```jsx
let message = (age < 3) ? 'Hi, baby!' :
  (age < 18) ? 'Hello!' :
  (age < 100) ? 'Greetings!' :
  'What an unusual age!';
```

1. The first question mark checks whether `age < 3`.
2. If true – it returns `'Hi, baby!'`. Otherwise, it continues to the expression after the colon ‘":"’, checking `age < 18`.
3. If that’s true – it returns `'Hello!'`. Otherwise, it continues to the expression after the next colon ‘":"’, checking `age < 100`.
4. If that’s true – it returns `'Greetings!'`. Otherwise, it continues to the expression after the last colon ‘":"’, returning `'What an unusual age!'`.

**Non-traditional use of "?": we can also do some portion of code instead of assigning a variable.**

### ? vs &&

```jsx
InputProps={name === 'password' ? {
                    endAdornment: (
                        <InputAdornment position="end">
                            <IconButton onClick={handleShowPassword}>
                                {type === "password" ? <Visibility/> : <VisibilityOff/>}
                            </IconButton>
                        </InputAdornment>
                    )
                } : null }
```

no need to use " : null"

```jsx
InputProps={name === 'password' && {
                    endAdornment: (
                        <InputAdornment position="end">
                            <IconButton onClick={handleShowPassword}>
                                {type === "password" ? <Visibility/> : <VisibilityOff/>}
                            </IconButton>
                        </InputAdornment>
                    )
                }}
```

### Nullish coalescing operator '??'

The result of `a ?? b` is:

- if `a` is defined, then `a`,
- if `a` isn’t defined, then `b`.

### Arrow functions

The code like below:

```jsx
let func = function(arg1, arg2, ..., argN) {
  return expression;
};
```

can be shortened as follow:

```jsx
let func = (arg1, arg2, ..., argN) => expression
```

## Code quality

### Breakpoints in Chrome

- alike in Visual Studio

![images/Untitled.png](javascript/Untitled.png)

- **F8:** “**Resume**”: continue the execution, hotkey.
- **F9:** “**Step**”: run the next command, hotkey.
- **F10: “Step over”**: run the next command, but don’t go into a function, hotkey.
- **F11**: **“Step into”**: goes into async functions code, waiting for them if necessary. *(“Step” command ignores async actions, such as setTimeout (scheduled function call), that execute later. )*,
- **Shift+F11:** Continue the execution and stop it at the very last line of the current function. That’s handy when we accidentally entered a nested call using "Step", but it does not interest us, and we want to continue to its end as soon as possible.
- **no hotkey -** enable/disable all breakpoints
- **no hotkey** – enable/disable automatic pause in case of an error.

### **Continue to here**

Right click on a line of code opens the context menu with a great option called “Continue to here”.

That’s handy when we want to move multiple steps forward to the line, but we’re too lazy to set a breakpoint.

### Suggested rules

![images/Untitled%201.png](javascript/Untitled%201.png)

### Avoid nesting:

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

### Automated Linters

Linters are tools that can automatically check the style of your code and make improving suggestions.

Example: ESLint

### Good and bad comments

**Comment this:**

- Overall architecture, high-level view.
- Function usage.
- Important solutions, especially when not immediately obvious.

**Avoid comments:**

- That tell “how code works” and “what it does”.
- Put them in only if it’s impossible to make the code so simple and self-descriptive that it doesn’t require them.

### Testing

- [more info](https://javascript.info/testing-mocha)

## Objects

### Accessing a property

- The dot notation: `obj.property`.
- Square brackets notation `obj["property"]`. Square brackets allow to take the key from a variable, like `obj[varWithKey]`.

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

## Handling asynchronous tasks

### Promises

A **callback** is a function that we call inside another function.

A promise is asynchronous callback. However, Promises are more than just callbacks. They are a very mighty abstraction, allow cleaner and better, functional code with less error-prone boilerplate.

- **promises** are objects representing the result of a **single (asynchronous) computation.**
- **promises** implement an observer pattern.
    - you don't have to know the callback that will use the value before the task completes
    - instead of expecting callbacks as arguments to your functions you can easily `return` a Promise object
    - the promise will store the value, and you can transparently add a callback whenever you want, it wil be called when the result is available.
    - "Transparency" implies that when you have a promise and add a callback to it, it doesn't make a difference to your code whether the result has arrived yet - the API and contracts are the same, simplifying caching/memoisation a lot.
    - you can add multiple callbacks easily

**What is the point of promises?**

- providing a direct correspondence between synchronous functions and asynchronous functions.

to be continued..

## Credits:

[https://github.com/javascript-tutorial/en.javascript.info](https://github.com/javascript-tutorial/en.javascript.info)
