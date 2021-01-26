---
typora-root-url: ./images/hook-flow.png
---

Beginners' guide to React.JS
===

## Questions
1. Why is `<strong>` tag not rendered correctly in lesson 8?
2. Using `React.useState()` 's `setValue` function, why is the value not set immediately? i.e. if you refer to the `value` right away, it's the old value, not the new one. When will the assignment take place?
3. 

## Reference

1. Video guide from Ken Dodds at EggHead.io: https://egghead.io/courses/the-beginner-s-guide-to-react

2. Code sample: https://github.com/kentcdodds/beginners-guide-to-react/tree/egghead

3. To get Babel standalone from UNPKG: https://unpkg.com/@babel/standalone/

## Lessons

1. `browser-sync` to serve from a local folder, e.g.
```bash
npx browser-sync start --server --files "./*.html" --no-open --no-notify --directory
```

2. Use UNPKG (https://unpkg.com/) to get hold of any npm packages quickly.

3. A typical 3 stages to render a page:
+ get hold of a root element
+ build a React element
+ use ReactDOM to render the React element
```javascript
const rootElement = document.getElementById("root")
const element = React.createElement('h1', {
  children: "Hello World",
  className: "container"
})
console.log(element)
ReactDOM.render(element, rootElement)
```

4. `jsx` is the syntax extension to JS that is used to build UI. Babel is needed to compile `jsx` in to plain Javascript, e.g.
```jsx
const element = <div className="container">Hello</div>
```
is compiled to
```javascript
"use strict";

const element = /*#__PURE__*/React.createElement("div", {
  className: "container"
}, "Hello");
```

5. [Babel](https://babeljs.io) compiler
+ Can be tried out at: https://babeljs.io/repl
+ Can be used on the client side, i.e. right on the browwer, by:
    + Including the Babel [standalone lib](https://www.npmjs.com/package/@babel/standalone) directly onto the page, e.g.
    ```html
    <script src="https://unpkg.com/babel-standalone@6.26.0/babel.min.js"></script>
    ```
    + Use type `text/babel` for the script that needs to be compiled, e.g.
    ```javascript
    <script type="text/babel">
      //jsx code goes here
    </script>
    ```
+ It is best to have the Babel compilation as part of a build pipeline.

6. `jsx`'s tips:
  + Use `{}` to render a Javascript variable
  + Pack properties into an object and spread them, e.g.
  ```jsx
  const rootElement = document.getElementById("root")
  const children = "Hello World"
  const className = "container"
  const props = {children, className}
  const bigHead = <h1 className={className}>{children}</h1>
  const smallHead = <h2 {...props}></h2>
  const element = <div>{smallHead} {bigHead}</div>
  ReactDOM.render(element, rootElement)
  ```

7. Rendering React elements side by side using React.Fragement, e.g.
  ```jsx
  const element = (
    <React.Fragment>

    </React.Fragment>
  )
  ```
  + React Fragment allows elements to be rendered without being nested into something like a div. It is very useful in some scenarios, like building a table.
  + React Fragment can be used in a shorthand just with `<> </>`, e.g.
  ```jsx
  const element = (
    <>

    </>
  )
  ```

8. React component
  + Is a js function
  + Should make use of `.children` property, as it will pick up the value between open and closing tags
  + Should start with a capital letter, by convention
  + Has 3 ways to be used, the last only possible if `.childen` is used to render:
  ```jsx
    const rootElement = document.getElementById("root")
    const Compo = (foo) => (
      <span>{foo.children}</span>
    )
    const element = (
      <h1>
        {Compo({children: "Hello"})}
        <Compo children=" World" />
        <br/>
        <Compo>Goodbye World</Compo>
      </h1>
    )
    ReactDOM.render(element, rootElement)
  ```

9. `propTypes` to validate input parameters
   + `prop-types` npm package
   + can be loaded directly from [UNPKG](https://unpkg.com/prop-types/)
   + Only the non-minimised version of `prop-types` throws the validation errors. The `.min` version will just ignore it, e.g. you need to use this on your page:
   ```html
   <script src="https://unpkg.com/prop-types@15.7.2/prop-types.js"></script>
   ```
   + React is smart enough to not run `prop-types` when using React package in production mode, rather than in development mode => no performance impact at runtime
   + One step further: `prop-types` can be removed from production code using `babel-plugin-transform-react-remove-prop-types` npm package
   + The code should look like this:
   ```jsx
   const rootElement = document.getElementById("root")
   function SayHello({firstName, lastName = "Unknown"})    {
      return (
        <h1>Hello {firstName} {lastName}</h1>
      )
   }
   SayHello.propTypes = {
      firstName: PropTypes.string.isRequired,
      lastName: PropTypes.string
   }
   const element = <SayHello firstName="Quynh"/>
   ReactDOM.render(element, rootElement)
   ```
10. React rendering is efficient. It uses an algorithm to compare the DOM trees to spot the difference and only apply re-rendering on those branches.
  + This example show how React only render one `div` every 10 millisecs
  ```jsx
  const rootElement = document.getElementById("root")
  function showTime() {
    const date = new Date()
    const time = date.toLocaleTimeString()
    const mil = date.getMilliseconds()

    const element = (
      <h1>
        Current time is {time}:{mil}
      </h1>
    )
    ReactDOM.render(element, rootElement)
  }
  setInterval(showTime, 10)
  ```
  + How React renders UI fast:
  > React builds and maintains an internal representation of the rendered UI (Virtual DOM). When a component’s props or state changes, React compares the newly returned element with the previously rendered one. When the two are not equal, React will update the DOM. Therefore, we have to be careful when changing the state.

11. Steps to refactor repeated React code into components
  + Notes:
    + `className` is used in React instead of `class` in HTML, because `class` is a keyword in Javascript
    + `className` expect a string of space separated values
    + `style` expects a Javascript object, with property names in camel convention, e.g. backgroundColor, fontStyle etc.
  + Steps:
    1. Render all props as they are passed in
    ```jsx
    function Box(props) {
      return (
        <div {...props}></div>
      )
    }
    ```
    2. Refactor one property out at a time, keep the rest as is, e.g. `children`
    ```jsx
    function Box({children, ...rest}) {
      return (
        <div {...rest}>{children}</div>
      )
    }
    ```
    3. Extract `style`, spread it last to apply it over any default values
    ```jsx
    function Box({children, style, ...rest}) {
      return (
        <div {...rest} style={{fontStyle: "italic", ...style}}>{children}</div>
      )
    }
    ```
    4. Extract `size` out and use it to apply `className`
    ```jsx
    function Box({children, style, size, ...rest}) {
      return (
        <div className={`box box--${size}`} style={{fontStyle: "italic", ...style}}  {...rest} >
          {children}
        </div>
      )
    }
    ```
    5. Final result:
    ```jsx
    const rootElement = document.getElementById("root")
    function Box({children, style, size, color="red", ...rest}) {
      return (
        <div
          className={`box box--${size}`}
          style={{fontStyle: "italic", backgroundColor: color, ...style}}  {...rest}>
            {children}
        </div>
      )
    }
    Box.propTypes = {
      size: PropTypes.string.isRequired,
      color: PropTypes.string
    }
    const element = (
      <>
        <Box size="small" color="lightblue">
          small lightblue box
        </Box>
        <Box size="medium" color="pink">
          medium pink box
        </Box>
        <Box size="large" color="orange">
          large orange box
        </Box>
      </>
    )
    ReactDOM.render(element, rootElement)
    ```

12. Event handling
  + Event names are in camelCase, e.g. `onChange`, `onFocus`, `onBlur`
  + Full list of supported event is [here](https://reactjs.org/docs/events.html)
  + Each event handler is a function with an input parameter `event`, of type `SyntheticEvent`.
  + The browser's native event is available at: `event.nativeEvent` property

13. Using `React.useState()` hook to maintain state
  + `React.useState()` returns an array of 2 elements, e.g. `const [name, setName] = React.useState('')`
  + Multiple states can be used by calling `React.useState` again
  + Use the first element as a read only variable, i.e. const
  + Use the second element as a function to update the value of the state, i.e. `setName('new value')`
  ```jsx
  const rootElement = document.getElementById("root")
  function Greeting() {
    const [name, setName] = React.useState('')
    const handleChange = event => {
      console.log(event)
      setName(event.target.value)
    }
    return (
      <div>
        <form>
          <label>Name: </label>
          <input id="name" onChange={handleChange}/>
        </form>
        { name ? <strong>Hello {name}</strong> : 'Please type your name'}
      </div>
    )
  }
  const element = <Greeting />
  ReactDOM.render(element, rootElement)
  ```

14. Managing side-effects using `useEffect` hook
  + `React.useEffect(() => {})` will trigger the input lambda everytime the component is rendered
  + `window.localStorage.setItem(key, val)` saves `val` into the browser's Local Storage
  + `window.localStorage.getItem(key)` get the value out
  + Value in Local Storage is persistent across page's reloads
  ```jsx
  const rootElement = document.getElementById("root")
  function Greeting() {
    const [name, setName] = React.useState(window.localStorage.getItem('name') || '')
    React.useEffect(() => {
      window.localStorage.setItem('name', name)
    })
    const handleChange = event => {
      setName(event.target.value)
    }
    return (
      <div>
        <form>
          <label>Name: </label>
          <input value={name} id="name" onChange={handleChange}/>
        </form>
        { name ? <strong>Hello {name}</strong> : 'Please type your name'}
      </div>
    )
  }
  const element = <Greeting />
  ReactDOM.render(element, rootElement)
  ```

15. Use a lazy initializer with `useState`
  + Use can pass a `lazyFunc` into useState, instead of a value, i.e. `React.useState(lazyFunc)`
  + `lazyFunc` will be evaluated once, synchronously on the initial load
  + But it won't be evaluated again per state change
  + This helps optimise performance of the React app

16. Manage the `useEffect` dependency array
  + `React.useEffect` accepts one optional variable: An array of state variables that will trigger the lambda
  ```jsx
  const [name, setName] = React.useState(window.localStorage.getItem('name') || '')
  React.useEffect(() => {
    window.localStorage.setItem('name', name)
  }, [name])
  ```
  + This helps optimise performance, since you may have multiple state variables, not all of them should trigger `useEffect`.
  + `eslint-plugin-react-hooks` npm [package](https://www.npmjs.com/package/eslint-plugin-react-hooks) to help keep track of the dependency array. Look at `exhaustive-deps` rule.

17. Build custom hook
  + Just like any other JS function
  + Should have prefix `use` in name as a convention, e.g. so that `eslint-plugin-react-hooks` can pick it up
  ```jsx
  const rootElement = document.getElementById("root")
  function useCustomHook(key, defaultVal = '') {
    const [name, setName] = React.useState(
      window.localStorage.getItem(key) || defaultVal
    )
    React.useEffect(() => {
      window.localStorage.setItem(key, name)
    }, [key, name])

    return [name, setName]
  }
  function Greeting() {
    const [name, setName] = useCustomHook('name')
    const handleChange = event => {
      setName(event.target.value)
    }
    return (
      <div>
        <form>
          <label>Name: </label>
          <input value={name} id="name" onChange={handleChange}/>
        </form>
        { name ? <strong>Hello {name}</strong> : 'Please type your name'}
      </div>
    )
  }
  const element = <Greeting />
  ReactDOM.render(element, rootElement)
  ```

18. Manipulate the DOM with React refs
  + `React.useRef()` is a hook that can be used to get reference access to, and to manipunate DOM
  + 3 steps to use it:
    + declaration, i.e. `const refDOM = React.useRef()`
    + marking the React component, i.e. `<div ref={refDOM}></div>`
    + accessing the object at rendering time with `.curent` property, i.e.
    ```jsx
    React.useEffect(() => {
      const node = refDOM.current
    })
    ```

19. Understanding React Hook Flow

  + This diagram shows the flow of a Hooks component:
  + ![hook-flow](/../hook-flow.png)

20. Making basic forms with React
  + Each button should have explicit type, because otherwise they will be implicitly assigned the type of `submit` (vs. `button` or `reset`)
  + Use `htmlFor` for label to assign it to an input element. This is good for accessibility.
  + Use `onSubmit` event on the form, rather than `onClick` event on the submit button. Because the form can be submitted by typing enter on the input boxes.
  + For an intput element, `id` and `name` are interchangable.
  + Inside event handler for `onSubmit`:
    + `event.preventDefault()` to stop the default behaviour of doing a POST back to the same URL, i.e. causing a refresh
    + `event.target.elements.<inputElementName>.value` to get the value of an input box
    + You can also use `const refDom = React.useRef()` to point to the element, then do `refDom.current.value`
  
21. Making dynamic forms with React
  
  + Use `React.useState()` to create a state value
  
  + Use `onChange` event of the input element to sync value of the input box into state's value
  
  + You can then evaluate the value as the user types/clicks and give them dynamic feedback, i.e. not having to wait until they click submit.

22. Control form value
  + For an input element, if you specify `value={someVal}`, React will set `someVal` into the input box, regardless of what the user puts in.
  + It's a good place to downcase user's input automatically for example. They will see it in lowercase even if they try to hold down Shift key.

23. Using React Error Boundaries to handler errors in React Components
  + Error boundaries are React components that catch JavaScript errors anywhere in their child component tree, log those errors and display a fallback UI instead of the compomnent tree that crashed.
  + You can build a ReactErrorBoundary by declaring `class ErrorBoundary extends React.Component`
  + But it's best to use `react-error-boundary` [npm package](https://www.npmjs.com/package/react-error-boundary).
  + Notes: Error boundaries do not catch errors for:
    + Event handlers
    
    + Async code, e.g. `setTimeout` of `requestAnimationFrame` callbacks
    
    + Server side rendering
    
    + Errors thrown in the error boundary itself
    
      


