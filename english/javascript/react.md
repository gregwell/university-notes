# React

**Created:** 8.02.2021, **last updated:** 11.02.2021

## React

### What is React?

React is a popular library for creating single-page applications (SPAs) that are rendered on the client side. An SPA might have multiple views (aka pages), and unlike conventional multi-page apps, navigating through these views shouldn’t result in the entire page being reloaded.

### What is DOM/ReactDOM

- DOM is a model for structured document
- it applies to all documents (word documents, HTML pages, XML files)
- represents the structure of an document in platform/browser independent way
- For example, the browser downloads the HTML along with any referenced JS and CSS (and images, Flash etc.). The browser constructs the DOM from the HTML and renders it using the rules specified in the CSS. JS may manipulate the DOM when the page loads, when the user does something, or when any other event happens. When the DOM changes the browser updates what is displayed.
- **ReactDOM** is a package that provides DOM specific methods that can be used at the top level of a web app to enable an efficient way of managing DOM elements of the web page.

## Redux

### What is Redux?

Redux is a predictable state container for JavaScript apps

- **Predictable -** helps to write applications that **behave consistently,** run in different environments(client, server, native) and are **easy to test**
- **Centralized -** centralizing application's state and logic enables powerful capabilities like **undo/redo**, **state persistence** and much more.
- **Debuggable -** the Redux DevTools make it easy to trace **when, where and why the application's state changed,** also it enables a possibility do log changes, use "**time-travel debugging",** and even send complete error reports to a server
- **Flexible -** works with any UI layer, and has a large ecosystem of addons to fit everyone needs

### How to control flow happens in redux?

1. Whenever I want to **replace the state** in the store I have to **dispatch** an **action**
2. The **action** is caught by one or more **reducers.**
3. The **reducer/s** create a **new state** that combines the **old state**, and the **dispatched action.**
4. The **store subscribers** are notified that there is a **new state.**

### What are roles of components/containers/actions/action creators/store in redux?

**Store**

- holds the state
- when a new action arrives runs the dispatch → middleware → reducers pipeline
- notifies subscribers when the state is replaced by a new ona

**Components** 

- *dumb* view parts which are not aware of the state directly
- also called *"presentational components"*

**Containers** (components/containers - a way to structure the app)

- pieces of that view that are aware of the state using react-redux
- also called *"smart components"* or "*high order components"*

**Actions**

- carries a payload of information from the application to the store
- are plain JavaScript objects that must have a type attribute to indicate the type of action performed

**Action creators**

- DRY way of creating actions (not strictly necessary)

### What are differences between redux, react-redux, redux-thunk?

**redux** 

- flow with a single store - managing application state
- independent from React - can be used in whatever environment including vanilla js, react, angular etc.

**react-redux** - bindings between redux and react that:

- creates **containers** (*smart components*) that listen to the store's state
- prepares the props for
- rerender the *dumb* **components** (*presentational components)*

**redux-thunk**

- middleware that allows you to write **action creators** that return a function instead of an action
- the thunk can be used to delay the dispatch of an action or to dispatch only if a certain conditon is met
- **used mainly for async calls to api,** that dispatch another action on success/failure
- also logging, crash reporting, routing..

**Additional info:**

- only the minimal data should be put i the state
- we should add to the state only the values that we want to update when an event happens, and for which we want to make the component re-render.

## Hooks

### What are Hooks?

- provide a more direct API to the React concepts you already know(props, state, context, refs, lifecycle)
- allow you to **reuse stateful logic** without changing your component hierarchy (stateful logic, **not state itself**)
- let you "hook into" React state and lifecycle features from function components
- don't work inside classes - they let you use React without classes

### State Hook

- we call it inside a function component to add some local state to it
- React will preserve this state between re-renders
- returns a pair: the current state value and a function that lets you update it

### Effect Hook

- Usual operations performed from React components: data fetching, subscriptions, manually changing the DOM are called "**side effects" (effects)** because **they can affect other components and can't be done during rendering.**
- One of the few React built-in Hooks is **useEffect.**
- It adds the ability to perform side effects from a function component.
- React will remember the function you passed and call it later after performing the DOM updates. (runs after each render). **But there is a way to change it, by passing a second argument that is the array of values that the effect depends on.**
- [a complete guide to useEffect](https://overreacted.io/a-complete-guide-to-useeffect/)

## ReactRouter

### What is Routing?

Routing is the process of keeping the browser URL in sync with what’s being rendered on the page. React Router lets you handle routing declaratively.

### ReactRouter:

ReactRouter is a collection of navigational components that compose declaratively with your application. 

- **BrowserRouter**:
    - is used for doing client side routing with URL segments, so you can load a top level component for each route
    - this helps separate concerns in your app and makes the logic/data more clear
    - this kind of client side routing makes your single page app feel more like a traditional webpage/web app.
    - example: */items/1234* - Load the Item Component and you can get the 1234 which might be an id from react-router and load a resource
- **Switch**
    - renders the first child<Route> that matches the location exclusively
    - using just a bunch of <Route> will render every other component, not worrying that they all belong to other routes
- **Route**
    - renders some UI when its path matches the current URL.

### History

The history library lets you easily manage session history anywhere JavaScript runs.

- Each <Router> component creates a history object that keeps track of the current location (history.location) and also the previous locations in a stack.
- When the current location changes, the view is re-rendered and you get a sense of navigation.
- The `history.push` method is invoked when you click on a `<Link>` component, and `history.replace` is called when you use a `<Redirect>`
- [more reading](https://www.sitepoint.com/react-router-complete-guide/)

## Tricks and tips

### Instead of creating two different components for SingUp/SignIn

we can make a variable and change some Input components depending on what we are doing

```jsx
const isSignup = true;
```

## Sources

### Credits

### Other resources I find useful

[https://stackoverflow.com/users/458193/dan-abramov](https://stackoverflow.com/users/458193/dan-abramov)

[https://overreacted.io/a-complete-guide-to-useeffect/](https://overreacted.io/a-complete-guide-to-useeffect/)

[https://reactjs.org/docs/thinking-in-react.html#step-3-identify-the-minimal-but-complete-representation-of-ui-state](https://reactjs.org/docs/thinking-in-react.html#step-3-identify-the-minimal-but-complete-representation-of-ui-state)

[https://medium.com/@5066aman/thinking-in-react-few-tips-6b32fbe835a3](https://medium.com/@5066aman/thinking-in-react-few-tips-6b32fbe835a3)