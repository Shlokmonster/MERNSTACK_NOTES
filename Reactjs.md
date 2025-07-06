# Complete React.js Guide - Zero to Advanced

## Table of Contents
1. [Introduction to React](#introduction-to-react)
2. [Getting Started](#getting-started)
3. [JSX Fundamentals](#jsx-fundamentals)
4. [Components](#components)
5. [Props](#props)
6. [State Management](#state-management)
7. [Event Handling](#event-handling)
8. [Lists and Keys](#lists-and-keys)
9. [Conditional Rendering](#conditional-rendering)
10. [Forms and Input Handling](#forms-and-input-handling)
11. [Component Lifecycle](#component-lifecycle)
12. [Hooks](#hooks)
13. [Context API](#context-api)
14. [Error Boundaries](#error-boundaries)
15. [Performance Optimization](#performance-optimization)
16. [Testing](#testing)
17. [Advanced Patterns](#advanced-patterns)
18. [State Management Libraries](#state-management-libraries)
19. [Routing](#routing)
20. [Best Practices](#best-practices)

---

## Introduction to React

React is a JavaScript library for building user interfaces, particularly web applications. Created by Facebook (now Meta), React follows a component-based architecture and uses a virtual DOM for efficient updates.

### Key Features
- **Component-Based**: Build encapsulated components that manage their own state
- **Virtual DOM**: Efficient rendering through a virtual representation of the DOM
- **Declarative**: Describe what the UI should look like for any given state
- **Learn Once, Write Anywhere**: Use React for web, mobile (React Native), and desktop apps

### Why React?
- Large ecosystem and community support
- Excellent performance with virtual DOM
- Strong development tools
- Backed by Meta with regular updates
- Great for both simple and complex applications

---

## Getting Started

### Prerequisites
- Basic knowledge of HTML, CSS, and JavaScript
- Understanding of ES6+ features (arrow functions, destructuring, modules)
- Node.js and npm installed on your machine

### Installation Methods

#### 1. Create React App (Recommended for beginners)
```bash
npx create-react-app my-app
cd my-app
npm start
```

#### 2. Vite (Faster alternative)
```bash
npm create vite@latest my-app -- --template react
cd my-app
npm install
npm run dev
```

#### 3. Manual Setup
```bash
npm init -y
npm install react react-dom
npm install --save-dev webpack webpack-cli webpack-dev-server
# Additional configuration required
```

### Project Structure
```
my-app/
├── public/
│   ├── index.html
│   └── favicon.ico
├── src/
│   ├── components/
│   ├── App.js
│   ├── App.css
│   ├── index.js
│   └── index.css
├── package.json
└── README.md
```

---

## JSX Fundamentals

JSX (JavaScript XML) is a syntax extension for JavaScript that looks similar to HTML but is actually JavaScript under the hood.

### Basic JSX Syntax
```jsx
// Basic JSX
const element = <h1>Hello, World!</h1>;

// JSX with variables
const name = 'John';
const element = <h1>Hello, {name}!</h1>;

// JSX with expressions
const element = <h1>2 + 2 = {2 + 2}</h1>;
```

### JSX Rules
```jsx
// 1. Must have one parent element
const element = (
  <div>
    <h1>Title</h1>
    <p>Content</p>
  </div>
);

// 2. Use React.Fragment or shorthand for multiple elements
const element = (
  <React.Fragment>
    <h1>Title</h1>
    <p>Content</p>
  </React.Fragment>
);

// Shorthand
const element = (
  <>
    <h1>Title</h1>
    <p>Content</p>
  </>
);

// 3. Use className instead of class
const element = <div className="container">Content</div>;

// 4. Use camelCase for attributes
const element = <div onClick={handleClick} tabIndex="0">Click me</div>;

// 5. Self-closing tags must be closed
const element = <img src="image.jpg" alt="Description" />;
```

### Embedding JavaScript
```jsx
// Variables
const user = { name: 'John', age: 30 };
const element = <h1>Hello, {user.name}!</h1>;

// Functions
function formatName(user) {
  return user.firstName + ' ' + user.lastName;
}

const element = <h1>Hello, {formatName(user)}!</h1>;

// Conditional expressions
const element = <h1>{user.isLoggedIn ? 'Welcome back!' : 'Please log in'}</h1>;

// Arrays
const numbers = [1, 2, 3, 4, 5];
const listItems = numbers.map(number => <li key={number}>{number}</li>);
```

---

## Components

Components are the building blocks of React applications. They let you split the UI into independent, reusable pieces.

### Function Components
```jsx
// Basic function component
function Welcome(props) {
  return <h1>Hello, {props.name}!</h1>;
}

// Arrow function component
const Welcome = (props) => {
  return <h1>Hello, {props.name}!</h1>;
};

// Shortened arrow function
const Welcome = (props) => <h1>Hello, {props.name}!</h1>;

// Using the component
function App() {
  return (
    <div>
      <Welcome name="Alice" />
      <Welcome name="Bob" />
    </div>
  );
}
```

### Class Components (Legacy)
```jsx
class Welcome extends React.Component {
  render() {
    return <h1>Hello, {this.props.name}!</h1>;
  }
}

// Class component with state
class Counter extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
  }

  increment = () => {
    this.setState({ count: this.state.count + 1 });
  };

  render() {
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={this.increment}>Increment</button>
      </div>
    );
  }
}
```

### Component Composition
```jsx
// Child component
function Button({ children, onClick, variant = 'primary' }) {
  return (
    <button 
      className={`btn btn-${variant}`}
      onClick={onClick}
    >
      {children}
    </button>
  );
}

// Parent component
function App() {
  const handleClick = () => {
    alert('Button clicked!');
  };

  return (
    <div>
      <Button onClick={handleClick}>Primary Button</Button>
      <Button onClick={handleClick} variant="secondary">
        Secondary Button
      </Button>
    </div>
  );
}
```

---

## Props

Props (short for properties) are used to pass data from parent to child components.

### Basic Props
```jsx
// Passing props
function App() {
  return (
    <div>
      <UserProfile name="John" age={30} isActive={true} />
    </div>
  );
}

// Receiving props
function UserProfile(props) {
  return (
    <div>
      <h2>{props.name}</h2>
      <p>Age: {props.age}</p>
      <p>Status: {props.isActive ? 'Active' : 'Inactive'}</p>
    </div>
  );
}
```

### Props Destructuring
```jsx
// Destructuring in parameters
function UserProfile({ name, age, isActive }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <p>Status: {isActive ? 'Active' : 'Inactive'}</p>
    </div>
  );
}

// Destructuring in function body
function UserProfile(props) {
  const { name, age, isActive } = props;
  return (
    <div>
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <p>Status: {isActive ? 'Active' : 'Inactive'}</p>
    </div>
  );
}
```

### Default Props
```jsx
// Using default parameters
function UserProfile({ name = 'Anonymous', age = 0, isActive = false }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <p>Status: {isActive ? 'Active' : 'Inactive'}</p>
    </div>
  );
}

// Using defaultProps (legacy)
function UserProfile({ name, age, isActive }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <p>Status: {isActive ? 'Active' : 'Inactive'}</p>
    </div>
  );
}

UserProfile.defaultProps = {
  name: 'Anonymous',
  age: 0,
  isActive: false
};
```

### PropTypes (Type Checking)
```jsx
import PropTypes from 'prop-types';

function UserProfile({ name, age, isActive }) {
  return (
    <div>
      <h2>{name}</h2>
      <p>Age: {age}</p>
      <p>Status: {isActive ? 'Active' : 'Inactive'}</p>
    </div>
  );
}

UserProfile.propTypes = {
  name: PropTypes.string.isRequired,
  age: PropTypes.number,
  isActive: PropTypes.bool
};
```

---

## State Management

State represents data that can change over time in your component.

### useState Hook
```jsx
import React, { useState } from 'react';

function Counter() {
  // State variable and setter function
  const [count, setCount] = useState(0);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount(count - 1)}>Decrement</button>
      <button onClick={() => setCount(0)}>Reset</button>
    </div>
  );
}
```

### Multiple State Variables
```jsx
function UserForm() {
  const [name, setName] = useState('');
  const [email, setEmail] = useState('');
  const [age, setAge] = useState(0);

  return (
    <form>
      <input
        type="text"
        value={name}
        onChange={(e) => setName(e.target.value)}
        placeholder="Name"
      />
      <input
        type="email"
        value={email}
        onChange={(e) => setEmail(e.target.value)}
        placeholder="Email"
      />
      <input
        type="number"
        value={age}
        onChange={(e) => setAge(parseInt(e.target.value))}
        placeholder="Age"
      />
    </form>
  );
}
```

### State with Objects
```jsx
function UserForm() {
  const [user, setUser] = useState({
    name: '',
    email: '',
    age: 0
  });

  const handleInputChange = (e) => {
    const { name, value } = e.target;
    setUser(prevUser => ({
      ...prevUser,
      [name]: value
    }));
  };

  return (
    <form>
      <input
        type="text"
        name="name"
        value={user.name}
        onChange={handleInputChange}
        placeholder="Name"
      />
      <input
        type="email"
        name="email"
        value={user.email}
        onChange={handleInputChange}
        placeholder="Email"
      />
      <input
        type="number"
        name="age"
        value={user.age}
        onChange={handleInputChange}
        placeholder="Age"
      />
    </form>
  );
}
```

### State with Arrays
```jsx
function TodoList() {
  const [todos, setTodos] = useState([]);
  const [inputValue, setInputValue] = useState('');

  const addTodo = () => {
    if (inputValue.trim()) {
      setTodos(prevTodos => [
        ...prevTodos,
        { id: Date.now(), text: inputValue, completed: false }
      ]);
      setInputValue('');
    }
  };

  const toggleTodo = (id) => {
    setTodos(prevTodos =>
      prevTodos.map(todo =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  };

  const removeTodo = (id) => {
    setTodos(prevTodos => prevTodos.filter(todo => todo.id !== id));
  };

  return (
    <div>
      <input
        type="text"
        value={inputValue}
        onChange={(e) => setInputValue(e.target.value)}
        placeholder="Add a todo"
      />
      <button onClick={addTodo}>Add Todo</button>
      <ul>
        {todos.map(todo => (
          <li key={todo.id}>
            <span
              style={{
                textDecoration: todo.completed ? 'line-through' : 'none'
              }}
              onClick={() => toggleTodo(todo.id)}
            >
              {todo.text}
            </span>
            <button onClick={() => removeTodo(todo.id)}>Remove</button>
          </li>
        ))}
      </ul>
    </div>
  );
}
```

---

## Event Handling

React uses SyntheticEvents, which are wrappers around native events that provide consistent behavior across browsers.

### Basic Event Handling
```jsx
function Button() {
  const handleClick = (e) => {
    e.preventDefault();
    console.log('Button clicked!');
    console.log('Event:', e);
  };

  return <button onClick={handleClick}>Click me</button>;
}
```

### Common Event Types
```jsx
function EventExamples() {
  const handleClick = (e) => console.log('Clicked');
  const handleMouseOver = (e) => console.log('Mouse over');
  const handleKeyPress = (e) => console.log('Key pressed:', e.key);
  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Form submitted');
  };

  return (
    <div>
      <button onClick={handleClick}>Click</button>
      <div onMouseOver={handleMouseOver}>Hover me</div>
      <input onKeyPress={handleKeyPress} placeholder="Type here" />
      <form onSubmit={handleSubmit}>
        <button type="submit">Submit</button>
      </form>
    </div>
  );
}
```

### Passing Arguments to Event Handlers
```jsx
function ItemList() {
  const items = ['Apple', 'Banana', 'Cherry'];

  const handleItemClick = (item, index) => {
    console.log(`Clicked ${item} at index ${index}`);
  };

  return (
    <ul>
      {items.map((item, index) => (
        <li key={index} onClick={() => handleItemClick(item, index)}>
          {item}
        </li>
      ))}
    </ul>
  );
}
```

---

## Lists and Keys

Rendering lists is a common pattern in React applications.

### Basic List Rendering
```jsx
function NumberList() {
  const numbers = [1, 2, 3, 4, 5];
  
  return (
    <ul>
      {numbers.map(number => (
        <li key={number}>{number}</li>
      ))}
    </ul>
  );
}
```

### Complex List Rendering
```jsx
function UserList() {
  const users = [
    { id: 1, name: 'John', email: 'john@example.com' },
    { id: 2, name: 'Jane', email: 'jane@example.com' },
    { id: 3, name: 'Bob', email: 'bob@example.com' }
  ];

  return (
    <div>
      {users.map(user => (
        <div key={user.id} className="user-card">
          <h3>{user.name}</h3>
          <p>{user.email}</p>
        </div>
      ))}
    </div>
  );
}
```

### Keys Best Practices
```jsx
// Good: Using unique IDs
function TodoList({ todos }) {
  return (
    <ul>
      {todos.map(todo => (
        <li key={todo.id}>{todo.text}</li>
      ))}
    </ul>
  );
}

// Avoid: Using array index as key (only if items don't change)
function StaticList() {
  const items = ['Apple', 'Banana', 'Cherry'];
  
  return (
    <ul>
      {items.map((item, index) => (
        <li key={index}>{item}</li>
      ))}
    </ul>
  );
}
```

---

## Conditional Rendering

Different ways to conditionally render elements in React.

### If/Else Statements
```jsx
function UserGreeting({ isLoggedIn, username }) {
  if (isLoggedIn) {
    return <h1>Welcome back, {username}!</h1>;
  } else {
    return <h1>Please log in.</h1>;
  }
}
```

### Ternary Operator
```jsx
function UserGreeting({ isLoggedIn, username }) {
  return (
    <div>
      {isLoggedIn ? (
        <h1>Welcome back, {username}!</h1>
      ) : (
        <h1>Please log in.</h1>
      )}
    </div>
  );
}
```

### Logical AND Operator
```jsx
function Notifications({ notifications }) {
  return (
    <div>
      {notifications.length > 0 && (
        <div className="notifications">
          <h3>You have {notifications.length} notifications</h3>
        </div>
      )}
    </div>
  );
}
```

### Multiple Conditions
```jsx
function UserStatus({ user }) {
  const renderStatus = () => {
    if (!user) return <p>Loading...</p>;
    if (user.isActive) return <p>User is active</p>;
    if (user.isPending) return <p>User is pending approval</p>;
    return <p>User is inactive</p>;
  };

  return (
    <div>
      <h2>{user?.name || 'Unknown User'}</h2>
      {renderStatus()}
    </div>
  );
}
```

---

## Forms and Input Handling

React provides two approaches for handling forms: controlled and uncontrolled components.

### Controlled Components
```jsx
function ContactForm() {
  const [formData, setFormData] = useState({
    name: '',
    email: '',
    message: ''
  });

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prevData => ({
      ...prevData,
      [name]: value
    }));
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    console.log('Form submitted:', formData);
    // Handle form submission
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <label>Name:</label>
        <input
          type="text"
          name="name"
          value={formData.name}
          onChange={handleChange}
          required
        />
      </div>
      <div>
        <label>Email:</label>
        <input
          type="email"
          name="email"
          value={formData.email}
          onChange={handleChange}
          required
        />
      </div>
      <div>
        <label>Message:</label>
        <textarea
          name="message"
          value={formData.message}
          onChange={handleChange}
          required
        />
      </div>
      <button type="submit">Submit</button>
    </form>
  );
}
```

### Form Validation
```jsx
function SignupForm() {
  const [formData, setFormData] = useState({
    username: '',
    email: '',
    password: ''
  });
  const [errors, setErrors] = useState({});

  const validateForm = () => {
    const newErrors = {};
    
    if (!formData.username) {
      newErrors.username = 'Username is required';
    } else if (formData.username.length < 3) {
      newErrors.username = 'Username must be at least 3 characters';
    }
    
    if (!formData.email) {
      newErrors.email = 'Email is required';
    } else if (!/\S+@\S+\.\S+/.test(formData.email)) {
      newErrors.email = 'Email is invalid';
    }
    
    if (!formData.password) {
      newErrors.password = 'Password is required';
    } else if (formData.password.length < 6) {
      newErrors.password = 'Password must be at least 6 characters';
    }
    
    setErrors(newErrors);
    return Object.keys(newErrors).length === 0;
  };

  const handleSubmit = (e) => {
    e.preventDefault();
    if (validateForm()) {
      console.log('Form is valid:', formData);
    }
  };

  const handleChange = (e) => {
    const { name, value } = e.target;
    setFormData(prev => ({ ...prev, [name]: value }));
    
    // Clear error when user starts typing
    if (errors[name]) {
      setErrors(prev => ({ ...prev, [name]: '' }));
    }
  };

  return (
    <form onSubmit={handleSubmit}>
      <div>
        <input
          type="text"
          name="username"
          value={formData.username}
          onChange={handleChange}
          placeholder="Username"
        />
        {errors.username && <span className="error">{errors.username}</span>}
      </div>
      <div>
        <input
          type="email"
          name="email"
          value={formData.email}
          onChange={handleChange}
          placeholder="Email"
        />
        {errors.email && <span className="error">{errors.email}</span>}
      </div>
      <div>
        <input
          type="password"
          name="password"
          value={formData.password}
          onChange={handleChange}
          placeholder="Password"
        />
        {errors.password && <span className="error">{errors.password}</span>}
      </div>
      <button type="submit">Sign Up</button>
    </form>
  );
}
```

---

## Component Lifecycle

Understanding the component lifecycle is crucial for managing side effects and cleanup.

### Lifecycle Methods (Class Components)
```jsx
class LifecycleExample extends React.Component {
  constructor(props) {
    super(props);
    this.state = { count: 0 };
    console.log('Constructor called');
  }

  static getDerivedStateFromProps(nextProps, prevState) {
    console.log('getDerivedStateFromProps called');
    return null;
  }

  componentDidMount() {
    console.log('Component mounted');
    // Good place for:
    // - API calls
    // - Setting up subscriptions
    // - Timers
  }

  componentDidUpdate(prevProps, prevState) {
    console.log('Component updated');
    // Good place for:
    // - API calls based on prop changes
    // - Updating DOM in response to prop changes
  }

  componentWillUnmount() {
    console.log('Component will unmount');
    // Clean up:
    // - Timers
    // - Subscriptions
    // - Event listeners
  }

  render() {
    console.log('Render called');
    return (
      <div>
        <p>Count: {this.state.count}</p>
        <button onClick={() => this.setState({ count: this.state.count + 1 })}>
          Increment
        </button>
      </div>
    );
  }
}
```

### Lifecycle with Hooks
```jsx
import React, { useState, useEffect } from 'react';

function LifecycleWithHooks() {
  const [count, setCount] = useState(0);

  // ComponentDidMount and ComponentDidUpdate
  useEffect(() => {
    console.log('Component mounted or updated');
    document.title = `Count: ${count}`;
  });

  // ComponentDidMount only
  useEffect(() => {
    console.log('Component mounted');
    // Setup subscriptions, timers, etc.
  }, []);

  // ComponentDidUpdate for specific values
  useEffect(() => {
    console.log('Count changed:', count);
  }, [count]);

  // ComponentWillUnmount
  useEffect(() => {
    const timer = setInterval(() => {
      console.log('Timer tick');
    }, 1000);

    return () => {
      console.log('Cleanup timer');
      clearInterval(timer);
    };
  }, []);

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>
        Increment
      </button>
    </div>
  );
}
```

---

## Hooks

Hooks are functions that let you use state and other React features in function components.

### useState
```jsx
function Counter() {
  const [count, setCount] = useState(0);
  
  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>+</button>
      <button onClick={() => setCount(count - 1)}>-</button>
    </div>
  );
}
```

### useEffect
```jsx
function DataFetcher() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        const response = await fetch('/api/data');
        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, []);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  return <div>Data: {JSON.stringify(data)}</div>;
}
```

### useContext
```jsx
const ThemeContext = React.createContext();

function ThemeProvider({ children }) {
  const [theme, setTheme] = useState('light');

  return (
    <ThemeContext.Provider value={{ theme, setTheme }}>
      {children}
    </ThemeContext.Provider>
  );
}

function ThemeToggle() {
  const { theme, setTheme } = useContext(ThemeContext);

  return (
    <button onClick={() => setTheme(theme === 'light' ? 'dark' : 'light')}>
      Current theme: {theme}
    </button>
  );
}
```

### useReducer
```jsx
const initialState = { count: 0 };

function reducer(state, action) {
  switch (action.type) {
    case 'increment':
      return { count: state.count + 1 };
    case 'decrement':
      return { count: state.count - 1 };
    case 'reset':
      return initialState;
    default:
      throw new Error();
  }
}

function Counter() {
  const [state, dispatch] = useReducer(reducer, initialState);

  return (
    <div>
      Count: {state.count}
      <button onClick={() => dispatch({ type: 'increment' })}>+</button>
      <button onClick={() => dispatch({ type: 'decrement' })}>-</button>
      <button onClick={() => dispatch({ type: 'reset' })}>Reset</button>
    </div>
  );
}
```

### useRef
```jsx
function FocusInput() {
  const inputRef = useRef(null);

  const focusInput = () => {
    inputRef.current.focus();
  };

  return (
    <div>
      <input ref={inputRef} type="text" />
      <button onClick={focusInput}>Focus Input</button>
    </div>
  );
}
```

### useMemo and useCallback
```jsx
function ExpensiveComponent({ items, filter }) {
  // Memoize expensive computation
  const filteredItems = useMemo(() => {
    console.log('Filtering items...');
    return items.filter(item => item.includes(filter));
  }, [items, filter]);

  // Memoize callback function
  const handleClick = useCallback((item) => {
    console.log('Clicked:', item);
  }, []);

  return (
    <ul>
      {filteredItems.map(item => (
        <li key={item} onClick={() => handleClick(item)}>
          {item}
        </li>
      ))}
    </ul>
  );
}
```

### Custom Hooks
```jsx
// Custom hook for fetching data
function useApi(url) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        const response = await fetch(url);
        const result = await response.json();
        setData(result);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, [url]);

  return { data, loading, error };
}

// Using the custom hook
function UserProfile({ userId }) {
  const { data: user, loading, error } = useApi(`/api/users/${userId}`);

  if (loading) return <div>Loading...</div>;
  if (error) return <div>Error: {error}</div>;
  return <div>User: {user.name}</div>;
}
```

---

## Context API

Context provides a way to pass data through the component tree without having to pass props down manually at every level.

### Creating and Using Context
```jsx
// Create context
const UserContext = React.createContext();

// Provider component
function UserProvider({ children }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    // Simulate loading user data
    setTimeout(() => {
      setUser({ id: 1, name: 'John Doe', email: 'john@example.com' });
      setLoading(false);
    }, 1000);
  }, []);

  const login = (userData) => {
    setUser(userData);
  };

  const logout = () => {
    setUser(null);
  };

  return (
    <UserContext.Provider value={{ user, loading, login, logout }}>
      {children}
    </UserContext.Provider>
  );
}

// Consumer component
function UserProfile() {
  const { user, loading, logout } = useContext(UserContext);

  if (loading) return <div>Loading user...</div>;
  if (!user) return <div>Please log in</div>;

  return (
    <div>
      <h2>{user.name}</h2>
      <p>{user.email}</p>
      <button onClick={logout}>Logout</button>
    </div>
  );
}

// App component
function App() {
  return (
    <UserProvider>
      <div>
        <h1>My App</h1>
        <UserProfile />
      </div>
    </UserProvider>
  );
}
```

### Multiple Contexts
```jsx
// Theme context
const ThemeContext = React.createContext();

// Language context
const LanguageContext = React.createContext();

function App() {
  return (
    <ThemeProvider>
      <LanguageProvider>
        <MainContent />
      </LanguageProvider>
    </ThemeProvider>
  );
}

function MainContent() {
  const { theme } = useContext(ThemeContext);
  const { language } = useContext(LanguageContext);

  return (
    <div className={`app ${theme}`}>
      <h1>{language === 'en' ? 'Hello' : 'Hola'}</h1>
    </div>
  );
}
```

---

## Error Boundaries

Error boundaries are React components that catch JavaScript errors anywhere in their child component tree.

### Creating an Error Boundary
```jsx
class ErrorBoundary extends React.Component {
  constructor(props) {
    super(props);
    this.state = { hasError: false, error: null, errorInfo: null };
  }

  static getDerivedStateFromError(error) {
    return { hasError: true };
  }

  componentDidCatch(error, errorInfo) {
    console.log('Error caught by boundary:', error, errorInfo);
    this.setState({
      error: error,
      errorInfo: errorInfo
    });
  }

  render() {
    if (this.state.hasError) {
      return (
        <div>
          <h2>Something went wrong.</h2>
          <details style={{ whiteSpace: 'pre-wrap' }}>
            {this.state.error && this.state.error.toString()}
            <br />
            {this.state.errorInfo.componentStack}
          </details>
        </div>
      );
    }

    return this.props.children;
  }
}

// Usage
function App() {
  return (
    <ErrorBoundary>
      <Header />
      <MainContent />
      <Footer />
    </ErrorBoundary>
  );
}
```

### Error Boundary Hook (React 16.8+)
```jsx
import { useState, useEffect } from 'react';

function useErrorBoundary() {
  const [error, setError] = useState(null);

  useEffect(() => {
    if (error) {
      throw error;
    }
  }, [error]);

  return setError;
}

function MyComponent() {
  const throwError = useErrorBoundary();

  const handleClick = () => {
    try {
      // Some operation that might fail
      throw new Error('Something went wrong!');
    } catch (error) {
      throwError(error);
    }
  };

  return <button onClick={handleClick}>Click me</button>;
}
```

---

## Performance Optimization

React provides several tools and techniques for optimizing performance.

### React.memo
```jsx
// Memoize component to prevent unnecessary re-renders
const ExpensiveComponent = React.memo(function ExpensiveComponent({ data, onUpdate }) {
  console.log('ExpensiveComponent rendered');
  
  return (
    <div>
      <h3>Expensive Component</h3>
      <p>{data.name}</p>
      <button onClick={() => onUpdate(data.id)}>Update</button>
    </div>
  );
});

// With custom comparison
const MyComponent = React.memo(function MyComponent({ user, posts }) {
  return (
    <div>
      <h2>{user.name}</h2>
      <p>Posts: {posts.length}</p>
    </div>
  );
}, (prevProps, nextProps) => {
  return prevProps.user.id === nextProps.user.id &&
         prevProps.posts.length === nextProps.posts.length;
});
```

### useMemo and useCallback
```jsx
function SearchableList({ items, searchTerm }) {
  // Memoize expensive computation
  const filteredItems = useMemo(() => {
    console.log('Filtering items...');
    return items.filter(item => 
      item.name.toLowerCase().includes(searchTerm.toLowerCase())
    );
  }, [items, searchTerm]);

  // Memoize callback to prevent child re-renders
  const handleItemClick = useCallback((item) => {
    console.log('Item clicked:', item);
  }, []);

  return (
    <div>
      {filteredItems.map(item => (
        <ItemComponent
          key={item.id}
          item={item}
          onClick={handleItemClick}
        />
      ))}
    </div>
  );
}
```

### Code Splitting with React.lazy
```jsx
import React, { Suspense } from 'react';

// Lazy load components
const LazyComponent = React.lazy(() => import('./LazyComponent'));
const AnotherLazyComponent = React.lazy(() => import('./AnotherLazyComponent'));

function App() {
  return (
    <div>
      <Suspense fallback={<div>Loading...</div>}>
        <LazyComponent />
      </Suspense>
      
      <Suspense fallback={<div>Loading another component...</div>}>
        <AnotherLazyComponent />
      </Suspense>
    </div>
  );
}
```

### Virtualization for Large Lists
```jsx
// Using react-window (external library)
import { FixedSizeList as List } from 'react-window';

function VirtualizedList({ items }) {
  const Row = ({ index, style }) => (
    <div style={style}>
      <div>Item {items[index].name}</div>
    </div>
  );

  return (
    <List
      height={600}
      itemCount={items.length}
      itemSize={50}
      width="100%"
    >
      {Row}
    </List>
  );
}
```

---

## Testing

Testing React components ensures your application works correctly and prevents regressions.

### Testing with Jest and React Testing Library
```jsx
// Component to test
function Counter() {
  const [count, setCount] = useState(0);

  return (
    <div>
      <p data-testid="count">Count: {count}</p>
      <button onClick={() => setCount(count + 1)}>Increment</button>
      <button onClick={() => setCount(count - 1)}>Decrement</button>
    </div>
  );
}

// Test file: Counter.test.js
import { render, screen, fireEvent } from '@testing-library/react';
import Counter from './Counter';

describe('Counter', () => {
  test('renders initial count', () => {
    render(<Counter />);
    expect(screen.getByTestId('count')).toHaveTextContent('Count: 0');
  });

  test('increments count when increment button is clicked', () => {
    render(<Counter />);
    const incrementButton = screen.getByText('Increment');
    
    fireEvent.click(incrementButton);
    expect(screen.getByTestId('count')).toHaveTextContent('Count: 1');
  });

  test('decrements count when decrement button is clicked', () => {
    render(<Counter />);
    const decrementButton = screen.getByText('Decrement');
    
    fireEvent.click(decrementButton);
    expect(screen.getByTestId('count')).toHaveTextContent('Count: -1');
  });
});
```

### Testing Hooks
```jsx
// Custom hook to test
function useCounter(initialValue = 0) {
  const [count, setCount] = useState(initialValue);

  const increment = () => setCount(count + 1);
  const decrement = () => setCount(count - 1);
  const reset = () => setCount(initialValue);

  return { count, increment, decrement, reset };
}

// Test file
import { renderHook, act } from '@testing-library/react';
import useCounter from './useCounter';

describe('useCounter', () => {
  test('should initialize with default value', () => {
    const { result } = renderHook(() => useCounter());
    expect(result.current.count).toBe(0);
  });

  test('should increment count', () => {
    const { result } = renderHook(() => useCounter());
    
    act(() => {
      result.current.increment();
    });
    
    expect(result.current.count).toBe(1);
  });
});
```

### Testing with Context
```jsx
// Test wrapper for context
function TestWrapper({ children }) {
  return (
    <UserProvider>
      {children}
    </UserProvider>
  );
}

test('renders user profile when user is logged in', () => {
  render(<UserProfile />, { wrapper: TestWrapper });
  
  // Test implementation
});
```

---

## Advanced Patterns

### Higher-Order Components (HOCs)
```jsx
// HOC that adds loading state
function withLoading(WrappedComponent) {
  return function WithLoadingComponent(props) {
    const [loading, setLoading] = useState(false);

    const setLoadingState = (isLoading) => {
      setLoading(isLoading);
    };

    if (loading) {
      return <div>Loading...</div>;
    }

    return (
      <WrappedComponent
        {...props}
        setLoading={setLoadingState}
      />
    );
  };
}

// Usage
const MyComponentWithLoading = withLoading(MyComponent);
```

### Render Props
```jsx
// Component with render prop
function DataProvider({ render }) {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    fetchData().then(result => {
      setData(result);
      setLoading(false);
    });
  }, []);

  return render({ data, loading });
}

// Usage
function App() {
  return (
    <DataProvider
      render={({ data, loading }) => (
        <div>
          {loading ? <div>Loading...</div> : <div>{data.name}</div>}
        </div>
      )}
    />
  );
}
```

### Compound Components
```jsx
// Compound component pattern
function Card({ children }) {
  return <div className="card">{children}</div>;
}

function CardHeader({ children }) {
  return <div className="card-header">{children}</div>;
}

function CardBody({ children }) {
  return <div className="card-body">{children}</div>;
}

function CardFooter({ children }) {
  return <div className="card-footer">{children}</div>;
}

// Attach sub-components
Card.Header = CardHeader;
Card.Body = CardBody;
Card.Footer = CardFooter;

// Usage
function App() {
  return (
    <Card>
      <Card.Header>
        <h2>Card Title</h2>
      </Card.Header>
      <Card.Body>
        <p>Card content goes here</p>
      </Card.Body>
      <Card.Footer>
        <button>Action</button>
      </Card.Footer>
    </Card>
  );
}
```

### Controlled vs Uncontrolled Components
```jsx
// Controlled component
function ControlledInput() {
  const [value, setValue] = useState('');

  return (
    <input
      type="text"
      value={value}
      onChange={(e) => setValue(e.target.value)}
    />
  );
}

// Uncontrolled component
function UncontrolledInput() {
  const inputRef = useRef(null);

  const handleSubmit = () => {
    console.log('Input value:', inputRef.current.value);
  };

  return (
    <div>
      <input type="text" ref={inputRef} />
      <button onClick={handleSubmit}>Submit</button>
    </div>
  );
}
```

---

## State Management Libraries

### Redux Setup
```jsx
// store.js
import { createStore } from 'redux';

const initialState = {
  count: 0
};

function counterReducer(state = initialState, action) {
  switch (action.type) {
    case 'INCREMENT':
      return { ...state, count: state.count + 1 };
    case 'DECREMENT':
      return { ...state, count: state.count - 1 };
    default:
      return state;
  }
}

const store = createStore(counterReducer);
export default store;

// App.js
import React from 'react';
import { Provider } from 'react-redux';
import store from './store';
import Counter from './Counter';

function App() {
  return (
    <Provider store={store}>
      <Counter />
    </Provider>
  );
}

// Counter.js
import React from 'react';
import { useSelector, useDispatch } from 'react-redux';

function Counter() {
  const count = useSelector(state => state.count);
  const dispatch = useDispatch();

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={() => dispatch({ type: 'INCREMENT' })}>+</button>
      <button onClick={() => dispatch({ type: 'DECREMENT' })}>-</button>
    </div>
  );
}
```

### Zustand (Lightweight Alternative)
```jsx
import { create } from 'zustand';

// Create store
const useStore = create((set) => ({
  count: 0,
  increment: () => set((state) => ({ count: state.count + 1 })),
  decrement: () => set((state) => ({ count: state.count - 1 })),
  reset: () => set({ count: 0 }),
}));

// Use in component
function Counter() {
  const { count, increment, decrement, reset } = useStore();

  return (
    <div>
      <p>Count: {count}</p>
      <button onClick={increment}>+</button>
      <button onClick={decrement}>-</button>
      <button onClick={reset}>Reset</button>
    </div>
  );
}
```

---

## Routing

### React Router Setup
```jsx
// npm install react-router-dom

import { BrowserRouter as Router, Routes, Route, Link } from 'react-router-dom';

function App() {
  return (
    <Router>
      <nav>
        <Link to="/">Home</Link>
        <Link to="/about">About</Link>
        <Link to="/contact">Contact</Link>
      </nav>

      <Routes>
        <Route path="/" element={<Home />} />
        <Route path="/about" element={<About />} />
        <Route path="/contact" element={<Contact />} />
        <Route path="/users/:id" element={<UserProfile />} />
        <Route path="*" element={<NotFound />} />
      </Routes>
    </Router>
  );
}

// Component with route parameters
function UserProfile() {
  const { id } = useParams();
  const navigate = useNavigate();

  return (
    <div>
      <h2>User Profile: {id}</h2>
      <button onClick={() => navigate('/')}>Go Home</button>
    </div>
  );
}
```

### Protected Routes
```jsx
function ProtectedRoute({ children }) {
  const { user } = useContext(AuthContext);
  
  return user ? children : <Navigate to="/login" />;
}

function App() {
  return (
    <Routes>
      <Route path="/login" element={<Login />} />
      <Route path="/dashboard" element={
        <ProtectedRoute>
          <Dashboard />
        </ProtectedRoute>
      } />
    </Routes>
  );
}
```

---

## Best Practices

### Component Organization
```jsx
// Good: Single responsibility
function UserCard({ user }) {
  return (
    <div className="user-card">
      <Avatar src={user.avatar} />
      <UserInfo user={user} />
      <UserActions user={user} />
    </div>
  );
}

// Good: Descriptive component names
function UserProfileHeader() { /* ... */ }
function UserPostsList() { /* ... */ }
function UserContactForm() { /* ... */ }
```

### Props Best Practices
```jsx
// Good: Destructure props
function Button({ children, variant = 'primary', onClick, disabled = false }) {
  return (
    <button
      className={`btn btn-${variant}`}
      onClick={onClick}
      disabled={disabled}
    >
      {children}
    </button>
  );
}

// Good: Use PropTypes or TypeScript
Button.propTypes = {
  children: PropTypes.node.isRequired,
  variant: PropTypes.oneOf(['primary', 'secondary', 'danger']),
  onClick: PropTypes.func,
  disabled: PropTypes.bool
};
```

### State Management Best Practices
```jsx
// Good: Keep state as simple as possible
function TodoApp() {
  const [todos, setTodos] = useState([]);
  const [filter, setFilter] = useState('all');

  // Good: Use functional updates for complex state
  const addTodo = (text) => {
    setTodos(prevTodos => [
      ...prevTodos,
      { id: Date.now(), text, completed: false }
    ]);
  };

  const toggleTodo = (id) => {
    setTodos(prevTodos =>
      prevTodos.map(todo =>
        todo.id === id ? { ...todo, completed: !todo.completed } : todo
      )
    );
  };

  return (
    <div>
      <TodoForm onAdd={addTodo} />
      <TodoList todos={todos} onToggle={toggleTodo} />
    </div>
  );
}
```

### Performance Best Practices
```jsx
// Good: Memoize expensive computations
const ExpensiveComponent = React.memo(function ExpensiveComponent({ data }) {
  const processedData = useMemo(() => {
    return data.map(item => ({
      ...item,
      processed: expensiveOperation(item)
    }));
  }, [data]);

  return (
    <div>
      {processedData.map(item => (
        <div key={item.id}>{item.processed}</div>
      ))}
    </div>
  );
});

// Good: Use callback memoization
function ParentComponent({ items }) {
  const handleItemClick = useCallback((item) => {
    // Handle click
  }, []);

  return (
    <div>
      {items.map(item => (
        <ChildComponent
          key={item.id}
          item={item}
          onClick={handleItemClick}
        />
      ))}
    </div>
  );
}
```

### Code Organization
```
src/
├── components/
│   ├── common/
│   │   ├── Button/
│   │   │   ├── Button.jsx
│   │   │   ├── Button.test.jsx
│   │   │   └── Button.module.css
│   │   └── Modal/
│   └── features/
│       ├── auth/
│       ├── dashboard/
│       └── profile/
├── hooks/
├── contexts/
├── services/
├── utils/
└── constants/
```

### Error Handling
```jsx
// Good: Proper error handling
function DataComponent() {
  const [data, setData] = useState(null);
  const [loading, setLoading] = useState(true);
  const [error, setError] = useState(null);

  useEffect(() => {
    const fetchData = async () => {
      try {
        setLoading(true);
        setError(null);
        const response = await api.getData();
        setData(response.data);
      } catch (err) {
        setError(err.message);
      } finally {
        setLoading(false);
      }
    };

    fetchData();
  }, []);

  if (loading) return <LoadingSpinner />;
  if (error) return <ErrorMessage message={error} />;
  if (!data) return <EmptyState />;

  return <DataDisplay data={data} />;
}
```

### Accessibility
```jsx
// Good: Accessible component
function Modal({ isOpen, onClose, title, children }) {
  useEffect(() => {
    const handleEscape = (event) => {
      if (event.key === 'Escape') {
        onClose();
      }
    };

    if (isOpen) {
      document.addEventListener('keydown', handleEscape);
      document.body.style.overflow = 'hidden';
    }

    return () => {
      document.removeEventListener('keydown', handleEscape);
      document.body.style.overflow = 'unset';
    };
  }, [isOpen, onClose]);

  if (!isOpen) return null;

  return (
    <div
      className="modal-overlay"
      onClick={onClose}
      role="dialog"
      aria-modal="true"
      aria-labelledby="modal-title"
    >
      <div
        className="modal-content"
        onClick={(e) => e.stopPropagation()}
      >
        <header className="modal-header">
          <h2 id="modal-title">{title}</h2>
          <button
            onClick={onClose}
            aria-label="Close modal"
            className="modal-close"
          >
            ×
          </button>
        </header>
        <div className="modal-body">
          {children}
        </div>
      </div>
    </div>
  );
}
```

---

## Conclusion

This guide covers React.js from basic concepts to advanced patterns and best practices. Remember that React is constantly evolving, so staying updated with the latest features and community practices is important for effective development.

Key takeaways:
- Start with functional components and hooks
- Use proper state management techniques
- Implement performance optimizations when needed
- Write tests for your components
- Follow React best practices and conventions
- Keep learning and experimenting with new features

Happy coding with React!
