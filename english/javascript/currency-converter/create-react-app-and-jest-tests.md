# Create-react-app config & JEST tests

**created:** 6.03.2021, **last updated:** 6.03.2021

- offers a modern build setup with no configuration
- **You don’t need to install or configure tools like webpack or Babel**. They are preconfigured and hidden so that you can focus on the code.

### Creating an App

- default template

```jsx
npx create-react-app my-app
```

- TypeScript template

```jsx
npx create-react-app my-app --template typescript
```

### Only files inside `src` are processed by webpack

- You need to put any JS and CSS files inside src, otherwise webpack won’t see them.

### Storybook

[https://create-react-app.dev/docs/developing-components-in-isolation](https://create-react-app.dev/docs/developing-components-in-isolation)

### Adding a Stylesheet

- This project setup uses webpack for handling all assets
- webpack offers a custom way of “extending” the concept of import beyond JavaScript
- To express that a JavaScript file depends on a CSS file, you need to import the CSS from the JavaScript file

### Adding other stylesheets, CSS reset etc.

[https://create-react-app.dev/docs/adding-a-css-modules-stylesheet](https://create-react-app.dev/docs/adding-a-css-modules-stylesheet)

[https://create-react-app.dev/docs/adding-a-sass-stylesheet](https://create-react-app.dev/docs/adding-a-sass-stylesheet)

[https://create-react-app.dev/docs/adding-css-reset](https://create-react-app.dev/docs/adding-css-reset)

[https://create-react-app.dev/docs/post-processing-css](https://create-react-app.dev/docs/post-processing-css)

## Tests in create-react-app setup

- Jest is default test runner
- Jest is Node-based runner. This means that the tests always run in a Node environment and not in a real browser.
- Jest is intended to be used for unit tests of your logic and your components rather than the DOM quirks.
- Jest will look for test files with any of the following popular naming conventions:  `.test.js` / `.spec.js` files (or the __tests__ folders) can be located at any depth under the `src` top level folder.

> We recommend to **put the test files** **(or tests folders)** **next to the code they are testing so that relative imports appear shorter.** For example, if App.test.js and App.js are in the same folder, the test only needs to import App from './App' instead of a long relative path. Collocation also helps find tests more quickly in larger projects.

> We recommend **running your tests in watch mode during development.**