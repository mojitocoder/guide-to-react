Beginners' guide to React.JS
===

## Questions
1. Why is `<strong>` tag not rendered correctly in lesson 8?

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

13. Using React hook to maintain state
  + `React.useState()` returns an array of 2 elements, e.g. `const [name, setName] = React.useState('')`
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
