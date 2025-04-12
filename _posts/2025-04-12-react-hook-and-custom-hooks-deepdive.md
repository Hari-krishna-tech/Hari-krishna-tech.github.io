---
layout: post
title: React Hooks Unleashed - From Built-in Magic to Custom Superpowers 
date: 2025-04-12
tags:
  [
    Hooks,
    react,
    react-hook,
    frontend,
    custom hook,
    business-logic,
  ]
---

# Understanding React Hooks

React Hooks are special functions introduced in React 16.8 that allow you to "hook into" React state and lifecycle features from functional components. Before Hooks, state management and lifecycle methods (like `componentDidMount` or `componentDidUpdate`) were only available in class components. Hooks enable functional components to have state, side effects, and other React features, making code more concise, reusable, and easier to understand.

## How do Hooks Work?

Hooks are just JavaScript functions, but there are two important rules:

1. **Only call Hooks at the top level.**
   This means you should never call a Hook inside loops, conditions, or nested functions. This rule ensures that React can consistently preserve the state between renders.

2. **Only call Hooks from React functions.**
   Use Hooks only in functional components or in custom Hooks - not in regular JavaScript functions.

When you call a Hook like `useState`, React internally associates that piece of state with the specific component instance and keeps track of the order in which Hooks are called. This internal tracking is why following the two rules above is so important.

If you want to dive deeper, I highly recommend watching [this video](https://youtu.be/KJP1E-Y-xyo?si=KtgtbLY7Snbsu5dm) where swyx recreates hooks from scratch at a JSConf. It's really insightful, and if you have time, try coding it yourself. Remember the concept of "closures" - it's key to understanding how hooks work internally.

## Common React Hooks and Their Examples

Let's explore some of the most common Hooks with examples and understand their usage scenarios.

### 1. `useState` — Managing State

The `useState` Hook lets you add state to your functional components.

```jsx
import React, { useState } from "react";

function Counter() {
  // Declare a state variable "count" with an initial value of 0
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>You clicked {count} times</p>
      <button onClick={() => setCount(count + 1)}>Click me</button>
    </div>
  );
}

export default Counter;
```

**Usage:**  
Call the `useState` function from the React module and provide an initial value. It returns a pair (array with 2 values) that we typically destructure using JavaScript syntax. The first value is our state variable, and the second is the function to update that state. By convention, we name the state with a descriptive word and the modifier function with `setThatWord`. When updating state, you should provide a new value rather than modifying the original state directly.

### 2. `useEffect` — Handling Side Effects

The `useEffect` Hook lets you perform side effects such as data fetching, subscriptions, or manually manipulating the DOM.

```jsx
import React, { useState, useEffect } from "react";

function Timer() {
  const [seconds, setSeconds] = useState(0);

  useEffect(() => {
    const interval = setInterval(() => {
      setSeconds((prev) => prev + 1);
    }, 1000);

    // Cleanup function to clear the interval when the component unmounts
    return () => clearInterval(interval);
  }, []); // Empty dependency array: effect runs only once after the first render

  return <p>Seconds: {seconds}</p>;
}

export default Timer;
```

**Usage:**  
`useEffect` is more complex than `useState` as it involves multiple parameters. The first parameter is a callback function, and the second is a dependency array.

The callback function runs whenever any element in the dependency array changes. If you provide an empty array, the effect will run only once when the component mounts. If you omit the dependency array entirely, the callback will run after every render.

The callback can optionally return a cleanup function that runs before the component unmounts or before the effect runs again.

### 3. `useMemo` — Memoizing Expensive Calculations

`useMemo` helps you optimize performance by memoizing (caching) an expensive calculation so that it's only recomputed if its dependencies change.

```jsx
import React, { useState, useMemo } from "react";

function ExpensiveCalculation({ number }) {
  const [multiplier, setMultiplier] = useState(1);

  const result = useMemo(() => {
    console.log("Calculating...");
    // Imagine this calculation to be very expensive
    return number * multiplier;
  }, [number, multiplier]);

  return (
    <div>
      <p>Result: {result}</p>
      <button onClick={() => setMultiplier(multiplier + 1)}>
        Increase Multiplier
      </button>
    </div>
  );
}

export default ExpensiveCalculation;
```

**Usage:**  
`useMemo` is straightforward to understand. When you have a function that might be computationally expensive to run on every render, `useMemo` ensures recalculation only happens if the dependencies change. Otherwise, it returns the previously memoized value, saving processing time.

### 4. `useRef` — Accessing DOM Elements or Holding Mutable Values

The `useRef` Hook returns a mutable ref object whose `.current` property is initialized to the passed argument. This reference persists for the full lifetime of the component.

```jsx
import React, { useRef, useEffect } from "react";

function FocusInput() {
  const inputRef = useRef(null);
  const renderCount = useRef(0);

  const handleClick = () => {
    // Directly access the DOM node to focus the input element
    inputRef.current.focus();
  };

  useEffect(() => {
    // Counting number of rerenders without causing the component to rerender
    renderCount.current = renderCount.current + 1;
  });

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={handleClick}>Focus Input</button>
    </div>
  );
}

export default FocusInput;
```

**Usage:**  
Use `useRef` when you need to store a mutable value that persists across renders without causing a re-render. This is common for storing references to DOM elements, timers, previous state or props, and any value that should be retained through the component's lifecycle without triggering re-rendering.

If we stored the input element's DOM reference in state, updating that reference could cause rerenders and potentially trigger infinite loops.

### 5. `useCallback` — Memoizing Functions

`useCallback` is similar to `useMemo` but is specifically for memoizing functions. This is useful to prevent unnecessary re-renders when passing functions to child components via props.

```jsx
import React, { useState, useCallback } from "react";

function CounterButton({ onIncrement }) {
  console.log("CounterButton rendered");
  return <button onClick={onIncrement}>Increment</button>;
}

function ParentComponent() {
  const [count, setCount] = useState(0);

  // Memoize the callback so it doesn't change unless its dependencies change
  const handleIncrement = useCallback(() => {
    setCount((prev) => prev + 1);
  }, []);

  return (
    <div>
      <p>Count: {count}</p>
      <CounterButton onIncrement={handleIncrement} />
    </div>
  );
}

export default ParentComponent;
```

Here's another example showing dependency usage:

```jsx
import { useCallback } from 'react';

export default function ProductPage({ productId, referrer, theme }) {
  const handleSubmit = useCallback((orderDetails) => {
    post('/product/' + productId + '/buy', {
      referrer,
      orderDetails,
    });
  }, [productId, referrer]);
  
  // Component code...
}
```

**Usage:**  
`useCallback` is useful for:
* Skipping re-rendering of components when function references would otherwise change
* Updating state from a memoized callback
* Preventing an Effect from firing too often

Similar to how `useMemo` memoizes a result, `useCallback` memoizes the function itself. The function will only be recreated when the dependencies change.

### 6. `useReducer` — Managing Complex State

Sometimes state logic becomes complex, involving multiple sub-values or tight relationships between state updates. `useReducer` is a good alternative to `useState` for managing such state.

```jsx
import React, { useReducer } from "react";

function reducer(state, action) {
  switch (action.type) {
    case "increment":
      return { count: state.count + 1 };
    case "decrement":
      return { count: state.count - 1 };
    default:
      throw new Error("Unknown action");
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, { count: 0 });

  return (
    <div>
      <p>Count: {state.count}</p>
      <button onClick={() => dispatch({ type: "increment" })}>+</button>
      <button onClick={() => dispatch({ type: "decrement" })}>-</button>
    </div>
  );
}

export default Counter;
```

**Usage:**  
If your component has multiple related states that you need to manage together, or if you prefer the reducer pattern from Redux, then `useReducer` might be the right choice. It centralizes state update logic and makes complex state transitions more predictable.

### 7. `useContext` — Consuming Context

The `useContext` Hook lets you subscribe to React context without introducing nesting in the component tree. It is useful for passing data through the component tree without having to pass props down manually at every level.

```jsx
import React, { useContext } from "react";

// Create a Context with a default value
const ThemeContext = React.createContext("light");

function ThemeButton() {
  const theme = useContext(ThemeContext);
  return (
    <button className={`btn ${theme}`}>
      Current theme: {theme}
    </button>
  );
}

function App() {
  return (
    <ThemeContext.Provider value="dark">
      <div>
        <ThemeButton />
      </div>
    </ThemeContext.Provider>
  );
}

export default App;
```

Here's a more complete example showing how to create and use a context with state:

```jsx
// 1. Create a context provider
import React, { createContext, useState } from 'react';

// Create a context
export const MyContext = createContext();

// Create a provider component
function MyProvider({ children }) {
  const [myState, setMyState] = useState('Initial Value');

  // Function to update the state
  const updateMyState = (newValue) => {
    setMyState(newValue);
  };

  // Pass the state and update function to the context
  return (
    <MyContext.Provider value={{ myState, updateMyState }}>
      {children}
    </MyContext.Provider>
  );
}

export default MyProvider;

// 2. Use the Context in Child Components:
import React, { useContext } from 'react';
import { MyContext } from './MyProvider'; // Import the context

function ChildComponent() {
  const { myState, updateMyState } = useContext(MyContext); // Access the context value and update function

  return (
    <div>
      <p>Current Value: {myState}</p>
      <button onClick={() => updateMyState('New Value')}>Update Context</button>
    </div>
  );
}

export default ChildComponent;
```

## Putting It All Together

Each of these Hooks plays a vital role in modern React development:

- **`useState`** for managing simple state
- **`useEffect`** for managing side effects
- **`useMemo`** and **`useCallback`** for performance optimizations
- **`useRef`** for accessing DOM nodes or holding mutable values
- **`useReducer`** for managing complex state logic
- **`useContext`** for accessing and providing context values across your application

Congratulations! You've now learned about the most frequently used React Hooks. The true power of hooks will be fully realized when you start creating custom hooks that encapsulate and reuse stateful logic across your components.



# Custom Hooks in React

In React, we've seen a significant evolution in how we structure components. While we once relied heavily on class-based components, the introduction of hooks has transformed the landscape, bringing state management and lifecycle functionality to functional components.

Beyond the built-in hooks we've already explored, React allows us to create custom hooks - a powerful pattern for extracting and reusing stateful logic across components.

## What Are Custom Hooks?

A custom hook is simply a JavaScript function that:
- Starts with the word "use" (by convention)
- Can call other hooks (built-in or custom)
- Extracts reusable logic from components

Custom hooks create a clear separation of concerns:
- **Components** = user interface
- **Hooks** = business logic

This separation offers several advantages:
- Portable and shareable logic
- Component-aware abstractions
- Rapid iteration
- Cleaner, more focused components

## Common Use Cases for Custom Hooks

### 1. Portable UI Utilities

#### Example: Dark Mode Implementation

Many applications need a dark mode feature. Let's see how we can implement this with a custom hook:

First, here's how we might implement dark mode directly in a component:

```jsx
function App() {
  const [isDark, setIsDark] = React.useState(false);
  const theme = isDark ? themes.dark : themes.light;

  return (
    <ThemeProvider theme={theme}>
      {/* App content */}
    </ThemeProvider>
  );
}
```

To detect the user's system preference, we could enhance this:

```jsx
const matchDark = '(prefers-color-scheme: dark)';

function App() {
  const [isDark, setIsDark] = React.useState(
    () => window.matchMedia && window.matchMedia(matchDark).matches
  );
  
  React.useEffect(() => {
    const matcher = window.matchMedia(matchDark);
    const onChange = ({matches}) => setIsDark(matches);

    matcher.addListener(onChange);

    return () => {
      matcher.removeListener(onChange);
    };
  }, [setIsDark]);
  
  const theme = isDark ? themes.dark : themes.light;

  return <ThemeProvider theme={theme}>{/* App content */}</ThemeProvider>;
}
```

This works, but the logic takes up significant space in our component. Let's extract it into a custom hook:

```jsx
function useDarkMode() {
  const matchDark = '(prefers-color-scheme: dark)';
  
  const [isDark, setIsDark] = React.useState(
    () => window.matchMedia && window.matchMedia(matchDark).matches
  );
  
  React.useEffect(() => {
    const matcher = window.matchMedia(matchDark);
    const onChange = ({matches}) => setIsDark(matches);

    matcher.addListener(onChange);

    return () => {
      matcher.removeListener(onChange);
    };
  }, [setIsDark]);
  
  return isDark;
}
```

Now our component becomes much cleaner:

```jsx
import useDarkMode from './useDarkMode';

function App() {
  const isDark = useDarkMode();
  const theme = isDark ? themes.dark : themes.light;

  return <ThemeProvider theme={theme}>{/* App content */}</ThemeProvider>;
}
```

#### Example: Detecting Clicks Outside an Element

Another common UI pattern is detecting when a user clicks outside a specific element (useful for dropdowns, modals, etc.):

```jsx
import useClickOutside from './useClickOutside';

function Menu() {
  const menuRef = React.useRef();

  const onClickOutside = () => {
    console.log('Clicked outside');
    // Close menu, modal, etc.
    // e.g., setModalOpen(false);
  };
  
  useClickOutside(menuRef, onClickOutside);

  return (
    <div ref={menuRef}>
      {/* Menu content */}
    </div>
  );
}
```

Here's how we might implement the `useClickOutside` hook:

```jsx
function useClickOutside(elRef, callback) {
  const callbackRef = React.useRef();
  callbackRef.current = callback; // Store the latest callback
  
  React.useEffect(() => {
    const handleClickOutside = e => {
      // If click is outside the element and callback exists
      if (!elRef.current?.contains(e.target) && callbackRef.current) {
        callbackRef.current(e);
      }
    };
    
    document.addEventListener('click', handleClickOutside, true);
    
    return () => {
      document.removeEventListener('click', handleClickOutside, true);
    };
  }, [elRef]); // Only re-run if the ref changes
}
```

Notice how we use `useRef` for the callback to avoid unnecessary effect re-runs while still having access to the latest callback function.

### 2. Global State Management

Custom hooks can create elegant state management solutions. Let's build a simple global store:

```jsx
const context = React.createContext();

export const StoreProvider = ({children, initialState = {}}) => {
  const [store, setStore] = React.useState(() => initialState);
  const contextValue = React.useMemo(() => [store, setStore], [store]);

  return (
    <context.Provider value={contextValue}>
      {children}
    </context.Provider>
  );
};

export default function useStore() {
  return React.useContext(context);
}
```

Using this in an application:

```jsx
import { StoreProvider } from './useStore';

const initialState = {
  todos: []
};

function App() {
  return (
    <StoreProvider initialState={initialState}>
      <Todos />
    </StoreProvider>
  );
}
```

Consuming the store:

```jsx
import useStore from './useStore';

function Todos() {
  const [{ todos }, setStore] = useStore();

  const addTodo = () => {
    setStore(oldStore => ({
      ...oldStore,
      todos: [...oldStore.todos, { name: 'New todo' }]
    }));
  };
  
  // Component JSX
}
```

#### Enhancing with useReducer

Directly manipulating the whole store from components can lead to bugs. Let's improve our implementation with `useReducer`:

```jsx
export const StoreProvider = ({children, reducer, initialState = {}}) => {
  const [store, dispatch] = React.useReducer(reducer, initialState);
  const contextValue = React.useMemo(() => [store, dispatch], [store, dispatch]);

  return (
    <context.Provider value={contextValue}>
      {children}
    </context.Provider>
  );
};
```

Using it with a reducer:

```jsx
const reducer = (state, action) => {
  switch (action.type) {
    case 'addTodo':
      return {
        ...state,
        todos: [...state.todos, action.todo]
      };
    default:
      throw new Error('Unknown action: ' + action.type);
  }
};

function App() {
  return (
    <StoreProvider reducer={reducer} initialState={initialState}>
      <Todos />
    </StoreProvider>
  );
}
```

Consuming with dispatch:

```jsx
function Todos() {
  const [{ todos }, dispatch] = useStore();

  const addTodo = () => dispatch({
    type: 'addTodo',
    todo: { name: 'New todo' }
  });
  
  return (
    <div>
      {todos.map(todo => (
        <Todo key={todo.id} todo={todo} />
      ))}
    </div>
  );
}
```

#### Optimizing with Multiple Contexts

To prevent unnecessary re-renders, we can separate store and dispatch into different contexts:

```jsx
const storeContext = React.createContext();
const dispatchContext = React.createContext();

export const StoreProvider = ({children, reducer, initialState = {}}) => {
  const [store, dispatch] = React.useReducer(reducer, initialState);
  
  return (
    <dispatchContext.Provider value={dispatch}>
      <storeContext.Provider value={store}>
        {children}
      </storeContext.Provider>
    </dispatchContext.Provider>
  );
};

export function useStore() {
  return React.useContext(storeContext);
}

export function useDispatch() {
  return React.useContext(dispatchContext);
}
```

This way, components that only need to dispatch actions won't re-render when the store changes.

#### Creating Reusable Store Factories

We can take this a step further by creating a factory function for stores:

```jsx
export default function makeStore(reducer, initialState) {
  const storeContext = React.createContext();
  const dispatchContext = React.createContext();

  const StoreProvider = ({children}) => {
    const [store, dispatch] = React.useReducer(reducer, initialState);
    
    return (
      <dispatchContext.Provider value={dispatch}>
        <storeContext.Provider value={store}>
          {children}
        </storeContext.Provider>
      </dispatchContext.Provider>
    );
  };

  function useStore() {
    return React.useContext(storeContext);
  }

  function useDispatch() {
    return React.useContext(dispatchContext);
  }

  return [StoreProvider, useStore, useDispatch];
}
```

Creating a specific store:

```jsx
import makeStore from './makeStore';

const todosReducer = (state, action) => {/* reducer logic */};

const [
  TodosProvider,
  useTodos,
  useTodosDispatch
] = makeStore(todosReducer, []);

export { TodosProvider, useTodos, useTodosDispatch };
```

#### Adding Local Storage Persistence

We can enhance our store with persistence:

```jsx
export default function makeStore(userReducer, initialState, key) {
  const storeContext = React.createContext();
  const dispatchContext = React.createContext();

  // Try to load initial state from localStorage
  try {
    initialState = JSON.parse(localStorage.getItem(key)) || initialState;
  } catch {}

  // Wrap the reducer to save to localStorage
  const reducer = (state, action) => {
    const newState = userReducer(state, action);
    localStorage.setItem(key, JSON.stringify(newState));
    return newState;
  };

  // Provider and hooks implementation...
  
  return [StoreProvider, useStore, useDispatch];
}
```

### 3. Server State Management

Custom hooks are excellent for managing server state (remote data):

```jsx
export function useTodos() {
  const [todos, setTodos] = React.useState([]);
  const [isLoading, setIsLoading] = React.useState(false);
  const [error, setError] = React.useState(null);

  const fetchTodos = React.useCallback(async () => {
    setIsLoading(true);
    try {
      const { data: todos } = await axios.get('/todos');
      setTodos(todos);
    } catch (err) {
      setError(err);
    }
    setIsLoading(false);
  }, []);

  React.useEffect(() => {
    fetchTodos();
  }, [fetchTodos]);

  return { todos, isLoading, error };
}
```

Using this hook in a component:

```jsx
function Todos() {
  const { todos, isLoading, error } = useTodos();

  if (isLoading) return <div>Loading...</div>;
  if (error) return <div>Error: {error.message}</div>;

  return (
    <div>
      {todos.map(todo => (
        <Todo key={todo.id} todo={todo} />
      ))}
    </div>
  );
}
```

We can also create hooks for mutations:

```jsx
function useToggleTodo() {
  const [isLoading, setIsLoading] = React.useState(false);
  const [error, setError] = React.useState(null);

  const toggleTodo = React.useCallback(async (todoId) => {
    setIsLoading(true);
    try {
      await axios.post(`/todos/${todoId}/toggle`);
      // Optionally update local state or trigger a refetch
    } catch (err) {
      setError(err);
    }
    setIsLoading(false);
  }, []);

  return [toggleTodo, { isLoading, error }];
}
```

Using the mutation hook:

```jsx
function Todo({ todo }) {
  const [toggleTodo, { isLoading, error }] = useToggleTodo();

  const handleClick = () => {
    toggleTodo(todo.id);
  };

  return (
    <div onClick={handleClick}>
      {isLoading ? 'Updating...' : todo.name}
      {error && <span>Error: {error.message}</span>}
    </div>
  );
}
```

## Conclusion

Custom hooks represent one of React's most powerful patterns for code organization and reuse. By extracting business logic from UI components, we create:

1. More focused, readable components
2. Reusable logic that can be shared across components
3. Better separation of concerns
4. Easier testing and maintenance

The pattern of "make components dumber to make them smarter" is a powerful one - by moving complex logic into custom hooks, our components become simpler, more declarative, and easier to understand, while gaining powerful capabilities through composition.

As you build React applications, look for opportunities to extract repeated logic into custom hooks. This approach will help your codebase scale more effectively and make your components more maintainable over time.
