---
title: The useEffect of React Runs Twice in Development Mode.
date: 2024-10-16 16:23:44
tags: [React, JavaScript]
categories: Frontend
---

Have you noticed that your `useEffect` hook of `React` runs twice when the page first loads in development mode? This occurs because since `React 18`, it can be confusing, especially for new developers. Let’s explore why this happens and what it means.

{% image /assets/images/react/react-hook-useEffect.png, alt=React Hook useEffect, width=800px %}

## What is `useEffect`?
`useEffect` is a hook that allows you to perform side effects in your components. Side effects can be things like **fetching data**, **subscribing to events**, or **changing the DOM**. This hook takes two arguments:

1. A function to run your side effect.
2. An optional array of dependencies that tells React when to run the effect again.

Here’s a simple example of useEffect in action:

``` javascript
import React, { useEffect, useState } from 'react';

function ExampleComponent() {
  const [count, setCount] = useState(0);

  useEffect(() => {
    console.log('Effect has been executed');
    // Side effect logic here
  }, [count]);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}
```

In above code, the log 'Effect has been executed' will be printed to the console twice when the component is first render on development mode.

{% image /assets/images/react/react-useEffect-runs-twice.png, alt=React useEffect runs twice in development mode %}


## Why Does useEffect Run Twice in Development Mode?
When you run your React app in development mode, you might see that the useEffect runs twice when the component loads for the first time. This can be confusing, especially for new developers.

### Reasons for the Double Execution
**Strict Mode:** This behavior is part of React’s `Strict Mode`. It purposely runs certain lifecycle methods and hooks like useEffect twice during development. This helps check if your code can handle side effects correctly.

**Testing Effects:** By running the effect two times, React tests if your side effects can handle being called multiple times without causing bugs. This helps catch problems early.

### What Happens in Production?
{% pbg danger, The double call only happens in development mode. When you make your app for production, useEffect will only run once on the initial load (and again later if the dependencies change).%}

## How to Handle the Double Execution
Here are some tips for dealing with the double execution of useEffect:

**Be Careful with State Updates**: If your effect updates state, make sure it's safe to run the effect multiple times without causing issues.

**Use Cleanup Functions**: Always return a cleanup function from your useEffect to free up resources and avoid memory issues.

``` javascript
useEffect(() => {
  // Your side effect code here

  return () => {
    // Cleanup code here
  };
}, [dependencies]);
```

**Test Your Effects**: Use the extra invocation to ensure that your effects work correctly.

## Disable Strict Mode
It is not recommend this way, but if you want to disable Strict Mode, in `React 18` you can disable Strict Mode by removing the `<React.StrictMode>` tag from the return statement in your root component.

In `Next.js`, you can disable Strict Mode by setting the following parameter in `next.config.js`:

``` javascript
module.exports = {
  reactStrictMode: false,
}
```

## Conclusion
{% pbg green,Seeing useEffect run twice in development mode can be surprising, but it helps you catch potential problems early.%} Understanding this behavior and preparing your code for it will allow you to use React hooks effectively and build better applications.

Even though this might seem confusing at first, it's an important part of the React development experience. Happy coding!
