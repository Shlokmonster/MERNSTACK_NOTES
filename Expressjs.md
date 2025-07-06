# Express.js Complete Guide: Zero to Advanced

## Table of Contents
1. [Introduction](#introduction)
2. [Installation & Setup](#installation--setup)
3. [Basic Concepts](#basic-concepts)
4. [Routing](#routing)
5. [Middleware](#middleware)
6. [Request & Response Objects](#request--response-objects)
7. [Template Engines](#template-engines)
8. [Error Handling](#error-handling)
9. [Static Files](#static-files)
10. [Database Integration](#database-integration)
11. [Authentication & Authorization](#authentication--authorization)
12. [Security](#security)
13. [Testing](#testing)
14. [Performance & Optimization](#performance--optimization)
15. [Deployment](#deployment)
16. [Advanced Topics](#advanced-topics)

---

## Introduction

Express.js is a minimal and flexible Node.js web application framework that provides a robust set of features for web and mobile applications. It's built on top of Node.js HTTP modules and adds useful functionality for building APIs and web applications.

### Key Features
- Fast, unopinionated, minimalist web framework
- Robust routing system
- Middleware support
- Template engine support
- Content negotiation
- Focus on high performance

---

## Installation & Setup

### Prerequisites
- Node.js (v14 or higher)
- npm or yarn package manager

### Basic Installation
```bash
# Create new project
mkdir my-express-app
cd my-express-app

# Initialize npm
npm init -y

# Install Express
npm install express

# Install development dependencies
npm install --save-dev nodemon
```

### First Express App
```javascript
// app.js
const express = require('express');
const app = express();
const port = 3000;

app.get('/', (req, res) => {
  res.send('Hello World!');
});

app.listen(port, () => {
  console.log(`Server running at http://localhost:${port}`);
});
```

### Package.json Scripts
```json
{
  "scripts": {
    "start": "node app.js",
    "dev": "nodemon app.js"
  }
}
```

---

## Basic Concepts

### Express Application
```javascript
const express = require('express');
const app = express();

// app is an instance of Express application
// It represents the entire application
```

### HTTP Methods
```javascript
// GET request
app.get('/users', (req, res) => {
  res.json({ message: 'Get all users' });
});

// POST request
app.post('/users', (req, res) => {
  res.json({ message: 'Create user' });
});

// PUT request
app.put('/users/:id', (req, res) => {
  res.json({ message: `Update user ${req.params.id}` });
});

// DELETE request
app.delete('/users/:id', (req, res) => {
  res.json({ message: `Delete user ${req.params.id}` });
});

// Handle all HTTP methods
app.all('/users', (req, res) => {
  res.json({ message: `${req.method} request to /users` });
});
```

### Route Parameters
```javascript
// Single parameter
app.get('/users/:id', (req, res) => {
  const userId = req.params.id;
  res.json({ userId });
});

// Multiple parameters
app.get('/users/:userId/posts/:postId', (req, res) => {
  const { userId, postId } = req.params;
  res.json({ userId, postId });
});

// Optional parameters
app.get('/posts/:year/:month?', (req, res) => {
  const { year, month } = req.params;
  res.json({ year, month: month || 'all' });
});
```

---

## Routing

### Basic Routing
```javascript
const express = require('express');
const app = express();

// Route methods
app.get('/', (req, res) => res.send('GET request'));
app.post('/', (req, res) => res.send('POST request'));
app.put('/', (req, res) => res.send('PUT request'));
app.delete('/', (req, res) => res.send('DELETE request'));
```

### Route Patterns
```javascript
// String patterns
app.get('/ab*cd', handler); // matches abcd, abxcd, abRANDOMcd, etc.
app.get('/ab+cd', handler); // matches abcd, abbcd, abbbcd, etc.
app.get('/ab?cd', handler); // matches acd, abcd
app.get('/ab(cd)?e', handler); // matches abe, abcde

// Regular expressions
app.get(/.*fly$/, handler); // matches butterfly, dragonfly, etc.
```

### Route Parameters with Regex
```javascript
// Only numeric IDs
app.get('/users/:id(\\d+)', (req, res) => {
  res.json({ userId: req.params.id });
});

// Custom pattern
app.get('/files/:filename([a-zA-Z0-9_-]+\\.[a-zA-Z0-9]+)', (req, res) => {
  res.json({ filename: req.params.filename });
});
```

### Express Router
```javascript
// routes/users.js
const express = require('express');
const router = express.Router();

// Middleware for all routes in this router
router.use((req, res, next) => {
  console.log('User routes middleware');
  next();
});

// Routes
router.get('/', (req, res) => {
  res.json({ message: 'Get all users' });
});

router.get('/:id', (req, res) => {
  res.json({ message: `Get user ${req.params.id}` });
});

router.post('/', (req, res) => {
  res.json({ message: 'Create user' });
});

module.exports = router;

// app.js
const userRoutes = require('./routes/users');
app.use('/users', userRoutes);
```

### Modular Routing Structure
```javascript
// routes/index.js
const express = require('express');
const router = express.Router();

const userRoutes = require('./users');
const postRoutes = require('./posts');
const authRoutes = require('./auth');

router.use('/users', userRoutes);
router.use('/posts', postRoutes);
router.use('/auth', authRoutes);

module.exports = router;

// app.js
const routes = require('./routes');
app.use('/api', routes);
```

---

## Middleware

### What is Middleware?
Middleware functions have access to the request object (req), response object (res), and the next middleware function in the application's request-response cycle.

### Built-in Middleware
```javascript
// Parse JSON bodies
app.use(express.json());

// Parse URL-encoded bodies
app.use(express.urlencoded({ extended: true }));

// Serve static files
app.use(express.static('public'));

// Parse cookies
app.use(express.cookieParser());
```

### Custom Middleware
```javascript
// Logger middleware
const logger = (req, res, next) => {
  console.log(`${req.method} ${req.path} - ${new Date().toISOString()}`);
  next();
};

app.use(logger);

// Authentication middleware
const authenticate = (req, res, next) => {
  const token = req.header('Authorization');
  if (!token) {
    return res.status(401).json({ error: 'No token provided' });
  }
  
  // Verify token logic here
  req.user = { id: 1, name: 'John' }; // Mock user
  next();
};

// Use middleware on specific routes
app.get('/protected', authenticate, (req, res) => {
  res.json({ message: 'Protected route', user: req.user });
});
```

### Middleware Order
```javascript
// Order matters!
app.use(express.json()); // First: parse JSON
app.use(logger); // Second: log requests
app.use(authenticate); // Third: authenticate

// Routes come after middleware
app.get('/api/users', (req, res) => {
  res.json({ users: [] });
});
```

### Error Handling Middleware
```javascript
// Error handling middleware (must have 4 parameters)
const errorHandler = (err, req, res, next) => {
  console.error(err.stack);
  
  if (err.name === 'ValidationError') {
    return res.status(400).json({ error: err.message });
  }
  
  if (err.name === 'CastError') {
    return res.status(400).json({ error: 'Invalid ID format' });
  }
  
  res.status(500).json({ error: 'Internal server error' });
};

// Must be used after all other middleware and routes
app.use(errorHandler);
```

### Third-party Middleware
```javascript
// Install: npm install cors helmet morgan compression
const cors = require('cors');
const helmet = require('helmet');
const morgan = require('morgan');
const compression = require('compression');

// Security headers
app.use(helmet());

// CORS
app.use(cors({
  origin: ['http://localhost:3000', 'https://myapp.com'],
  credentials: true
}));

// HTTP request logger
app.use(morgan('combined'));

// Compress responses
app.use(compression());
```

---

## Request & Response Objects

### Request Object (req)
```javascript
app.get('/demo', (req, res) => {
  // URL parts
  console.log(req.url);        // Full URL
  console.log(req.path);       // Path portion
  console.log(req.method);     // HTTP method
  console.log(req.protocol);   // http or https
  console.log(req.hostname);   // Domain name
  
  // Parameters
  console.log(req.params);     // Route parameters
  console.log(req.query);      // Query string parameters
  console.log(req.body);       // Request body (needs body parser)
  
  // Headers
  console.log(req.headers);    // All headers
  console.log(req.get('User-Agent')); // Specific header
  
  // Cookies
  console.log(req.cookies);    // Parsed cookies
  
  // IP and security
  console.log(req.ip);         // Client IP
  console.log(req.secure);     // true if HTTPS
  
  res.json({ message: 'Check console for request details' });
});
```

### Response Object (res)
```javascript
app.get('/response-demo', (req, res) => {
  // Status codes
  res.status(200);           // Set status code
  res.sendStatus(404);       // Send status code and message
  
  // Headers
  res.set('X-Custom-Header', 'value');
  res.header('Content-Type', 'application/json');
  
  // Response methods
  res.send('Plain text');                    // Send response
  res.json({ message: 'JSON response' });    // Send JSON
  res.redirect('/login');                    // Redirect
  res.render('index', { title: 'Home' });   // Render template
  
  // File operations
  res.sendFile(__dirname + '/public/file.pdf');
  res.download('/path/to/file.pdf', 'filename.pdf');
  
  // Cookies
  res.cookie('session', 'abc123', { 
    httpOnly: true, 
    secure: true, 
    maxAge: 24 * 60 * 60 * 1000 // 24 hours
  });
  res.clearCookie('session');
});
```

### Request Validation
```javascript
// Install: npm install express-validator
const { body, validationResult } = require('express-validator');

// Validation middleware
const validateUser = [
  body('email').isEmail().normalizeEmail(),
  body('password').isLength({ min: 6 }),
  body('name').trim().isLength({ min: 2 })
];

app.post('/users', validateUser, (req, res) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(400).json({ errors: errors.array() });
  }
  
  // Process valid data
  const { email, password, name } = req.body;
  res.json({ message: 'User created', user: { email, name } });
});
```

---

## Template Engines

### EJS (Embedded JavaScript)
```bash
npm install ejs
```

```javascript
// Set EJS as template engine
app.set('view engine', 'ejs');
app.set('views', './views');

// Route
app.get('/', (req, res) => {
  const data = {
    title: 'Home Page',
    users: [
      { name: 'John', age: 30 },
      { name: 'Jane', age: 25 }
    ]
  };
  res.render('index', data);
});
```

```html
<!-- views/index.ejs -->
<!DOCTYPE html>
<html>
<head>
    <title><%= title %></title>
</head>
<body>
    <h1><%= title %></h1>
    <ul>
        <% users.forEach(user => { %>
            <li><%= user.name %> - <%= user.age %> years old</li>
        <% }); %>
    </ul>
</body>
</html>
```

### Handlebars
```bash
npm install express-handlebars
```

```javascript
const exphbs = require('express-handlebars');

app.engine('handlebars', exphbs());
app.set('view engine', 'handlebars');

app.get('/', (req, res) => {
  res.render('home', { title: 'Home', users: [] });
});
```

### Pug (formerly Jade)
```bash
npm install pug
```

```javascript
app.set('view engine', 'pug');

app.get('/', (req, res) => {
  res.render('index', { title: 'Home', users: [] });
});
```

```pug
// views/index.pug
doctype html
html
  head
    title= title
  body
    h1= title
    ul
      each user in users
        li= user.name + ' - ' + user.age + ' years old'
```

---

## Error Handling

### Basic Error Handling
```javascript
// Synchronous error handling
app.get('/sync-error', (req, res, next) => {
  try {
    // Some operation that might throw
    throw new Error('Something went wrong');
  } catch (error) {
    next(error); // Pass error to error handler
  }
});

// Asynchronous error handling
app.get('/async-error', async (req, res, next) => {
  try {
    await someAsyncOperation();
    res.json({ success: true });
  } catch (error) {
    next(error);
  }
});
```

### Custom Error Classes
```javascript
// errors/CustomError.js
class CustomError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
    this.isOperational = true;
    
    Error.captureStackTrace(this, this.constructor);
  }
}

class ValidationError extends CustomError {
  constructor(message) {
    super(message, 400);
    this.name = 'ValidationError';
  }
}

class NotFoundError extends CustomError {
  constructor(message) {
    super(message, 404);
    this.name = 'NotFoundError';
  }
}

module.exports = { CustomError, ValidationError, NotFoundError };
```

### Global Error Handler
```javascript
const { CustomError } = require('./errors/CustomError');

// Global error handling middleware
const globalErrorHandler = (err, req, res, next) => {
  let error = { ...err };
  error.message = err.message;

  // Log error
  console.error(err);

  // Mongoose bad ObjectId
  if (err.name === 'CastError') {
    const message = 'Resource not found';
    error = new CustomError(message, 404);
  }

  // Mongoose duplicate key
  if (err.code === 11000) {
    const message = 'Duplicate field value entered';
    error = new CustomError(message, 400);
  }

  // Mongoose validation error
  if (err.name === 'ValidationError') {
    const message = Object.values(err.errors).map(val => val.message);
    error = new CustomError(message, 400);
  }

  res.status(error.statusCode || 500).json({
    success: false,
    error: error.message || 'Server Error'
  });
};

// 404 handler
const notFound = (req, res, next) => {
  const error = new NotFoundError(`Not found - ${req.originalUrl}`);
  next(error);
};

// Use error handlers
app.use(notFound);
app.use(globalErrorHandler);
```

### Async Error Wrapper
```javascript
// utils/asyncHandler.js
const asyncHandler = (fn) => (req, res, next) => {
  Promise.resolve(fn(req, res, next)).catch(next);
};

// Usage
app.get('/users/:id', asyncHandler(async (req, res, next) => {
  const user = await User.findById(req.params.id);
  if (!user) {
    throw new NotFoundError('User not found');
  }
  res.json(user);
}));
```

---

## Static Files

### Serving Static Files
```javascript
// Serve static files from 'public' directory
app.use(express.static('public'));

// Multiple static directories
app.use(express.static('public'));
app.use(express.static('uploads'));

// Virtual path prefix
app.use('/static', express.static('public'));
app.use('/uploads', express.static('uploads'));

// Absolute path
app.use('/static', express.static(path.join(__dirname, 'public')));
```

### Static File Options
```javascript
// With options
app.use('/static', express.static('public', {
  dotfiles: 'ignore',
  etag: false,
  extensions: ['htm', 'html'],
  index: false,
  maxAge: '1d',
  redirect: false,
  setHeaders: function (res, path, stat) {
    res.set('x-timestamp', Date.now());
  }
}));
```

### File Upload Handling
```bash
npm install multer
```

```javascript
const multer = require('multer');
const path = require('path');

// Storage configuration
const storage = multer.diskStorage({
  destination: function (req, file, cb) {
    cb(null, 'uploads/');
  },
  filename: function (req, file, cb) {
    cb(null, file.fieldname + '-' + Date.now() + path.extname(file.originalname));
  }
});

// File filter
const fileFilter = (req, file, cb) => {
  if (file.mimetype.startsWith('image/')) {
    cb(null, true);
  } else {
    cb(new Error('Not an image! Please upload only images.'), false);
  }
};

const upload = multer({ 
  storage: storage,
  fileFilter: fileFilter,
  limits: { fileSize: 5 * 1024 * 1024 } // 5MB limit
});

// Single file upload
app.post('/upload', upload.single('photo'), (req, res) => {
  if (!req.file) {
    return res.status(400).json({ error: 'No file uploaded' });
  }
  res.json({ filename: req.file.filename });
});

// Multiple files upload
app.post('/upload-multiple', upload.array('photos', 5), (req, res) => {
  res.json({ files: req.files.map(file => file.filename) });
});
```

---

## Database Integration

### MongoDB with Mongoose
```bash
npm install mongoose
```

```javascript
const mongoose = require('mongoose');

// Connect to MongoDB
mongoose.connect('mongodb://localhost:27017/myapp', {
  useNewUrlParser: true,
  useUnifiedTopology: true,
});

// User schema
const userSchema = new mongoose.Schema({
  name: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  password: { type: String, required: true },
  createdAt: { type: Date, default: Date.now }
});

const User = mongoose.model('User', userSchema);

// CRUD operations
app.get('/users', async (req, res) => {
  try {
    const users = await User.find().select('-password');
    res.json(users);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

app.post('/users', async (req, res) => {
  try {
    const user = new User(req.body);
    await user.save();
    res.status(201).json(user);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
});

app.get('/users/:id', async (req, res) => {
  try {
    const user = await User.findById(req.params.id).select('-password');
    if (!user) {
      return res.status(404).json({ error: 'User not found' });
    }
    res.json(user);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

app.put('/users/:id', async (req, res) => {
  try {
    const user = await User.findByIdAndUpdate(req.params.id, req.body, { 
      new: true, 
      runValidators: true 
    });
    if (!user) {
      return res.status(404).json({ error: 'User not found' });
    }
    res.json(user);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
});

app.delete('/users/:id', async (req, res) => {
  try {
    const user = await User.findByIdAndDelete(req.params.id);
    if (!user) {
      return res.status(404).json({ error: 'User not found' });
    }
    res.json({ message: 'User deleted successfully' });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});
```

### PostgreSQL with Sequelize
```bash
npm install sequelize pg pg-hstore
```

```javascript
const { Sequelize, DataTypes } = require('sequelize');

// Connect to PostgreSQL
const sequelize = new Sequelize('database', 'username', 'password', {
  host: 'localhost',
  dialect: 'postgres'
});

// Define User model
const User = sequelize.define('User', {
  name: {
    type: DataTypes.STRING,
    allowNull: false
  },
  email: {
    type: DataTypes.STRING,
    allowNull: false,
    unique: true
  },
  password: {
    type: DataTypes.STRING,
    allowNull: false
  }
});

// Sync models
sequelize.sync();

// CRUD operations
app.get('/users', async (req, res) => {
  try {
    const users = await User.findAll({ attributes: { exclude: ['password'] } });
    res.json(users);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});
```

---

## Authentication & Authorization

### JWT Authentication
```bash
npm install jsonwebtoken bcryptjs
```

```javascript
const jwt = require('jsonwebtoken');
const bcrypt = require('bcryptjs');

// Register user
app.post('/register', async (req, res) => {
  try {
    const { name, email, password } = req.body;
    
    // Check if user exists
    const existingUser = await User.findOne({ email });
    if (existingUser) {
      return res.status(400).json({ error: 'User already exists' });
    }
    
    // Hash password
    const hashedPassword = await bcrypt.hash(password, 10);
    
    // Create user
    const user = new User({
      name,
      email,
      password: hashedPassword
    });
    
    await user.save();
    
    // Generate token
    const token = jwt.sign(
      { userId: user._id }, 
      process.env.JWT_SECRET, 
      { expiresIn: '24h' }
    );
    
    res.status(201).json({
      message: 'User created successfully',
      token,
      user: { id: user._id, name, email }
    });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// Login user
app.post('/login', async (req, res) => {
  try {
    const { email, password } = req.body;
    
    // Find user
    const user = await User.findOne({ email });
    if (!user) {
      return res.status(401).json({ error: 'Invalid credentials' });
    }
    
    // Check password
    const isMatch = await bcrypt.compare(password, user.password);
    if (!isMatch) {
      return res.status(401).json({ error: 'Invalid credentials' });
    }
    
    // Generate token
    const token = jwt.sign(
      { userId: user._id }, 
      process.env.JWT_SECRET, 
      { expiresIn: '24h' }
    );
    
    res.json({
      message: 'Login successful',
      token,
      user: { id: user._id, name: user.name, email: user.email }
    });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// Auth middleware
const auth = async (req, res, next) => {
  try {
    const token = req.header('Authorization')?.replace('Bearer ', '');
    
    if (!token) {
      return res.status(401).json({ error: 'No token, authorization denied' });
    }
    
    const decoded = jwt.verify(token, process.env.JWT_SECRET);
    const user = await User.findById(decoded.userId).select('-password');
    
    if (!user) {
      return res.status(401).json({ error: 'Token is not valid' });
    }
    
    req.user = user;
    next();
  } catch (error) {
    res.status(401).json({ error: 'Token is not valid' });
  }
};

// Protected route
app.get('/profile', auth, (req, res) => {
  res.json(req.user);
});
```

### Session-based Authentication
```bash
npm install express-session connect-mongo
```

```javascript
const session = require('express-session');
const MongoStore = require('connect-mongo');

// Session configuration
app.use(session({
  secret: process.env.SESSION_SECRET,
  resave: false,
  saveUninitialized: false,
  store: MongoStore.create({
    mongoUrl: 'mongodb://localhost:27017/sessions'
  }),
  cookie: {
    secure: process.env.NODE_ENV === 'production',
    httpOnly: true,
    maxAge: 24 * 60 * 60 * 1000 // 24 hours
  }
}));

// Login with session
app.post('/login', async (req, res) => {
  try {
    const { email, password } = req.body;
    
    // Validate user (same as before)
    const user = await User.findOne({ email });
    if (!user || !await bcrypt.compare(password, user.password)) {
      return res.status(401).json({ error: 'Invalid credentials' });
    }
    
    // Store user in session
    req.session.userId = user._id;
    
    res.json({
      message: 'Login successful',
      user: { id: user._id, name: user.name, email: user.email }
    });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
});

// Session-based auth middleware
const sessionAuth = async (req, res, next) => {
  try {
    if (!req.session.userId) {
      return res.status(401).json({ error: 'Please log in' });
    }
    
    const user = await User.findById(req.session.userId).select('-password');
    if (!user) {
      return res.status(401).json({ error: 'User not found' });
    }
    
    req.user = user;
    next();
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
};

// Logout
app.post('/logout', (req, res) => {
  req.session.destroy(err => {
    if (err) {
      return res.status(500).json({ error: 'Could not log out' });
    }
    res.json({ message: 'Logout successful' });
  });
});
```

### Role-based Authorization
```javascript
// User model with roles
const userSchema = new mongoose.Schema({
  name: String,
  email: String,
  password: String,
  role: {
    type: String,
    enum: ['user', 'admin', 'moderator'],
    default: 'user'
  }
});

// Authorization middleware
const authorize = (...roles) => {
  return (req, res, next) => {
    if (!req.user) {
      return res.status(401).json({ error: 'Please log in' });
    }
    
    if (!roles.includes(req.user.role)) {
      return res.status(403).json({ error: 'Access denied' });
    }
    
    next();
  };
};

// Usage
app.get('/admin', auth, authorize('admin'), (req, res) => {
  res.json({ message: 'Admin only content' });
});

app.get('/moderator', auth, authorize('admin', 'moderator'), (req, res) => {
  res.json({ message: 'Moderator and admin content' });
});
```

---

## Security

### Essential Security Middleware
```bash
npm install helmet cors express-rate-limit express-validator
```

```javascript
const helmet = require('helmet');
const cors = require('cors');
const rateLimit = require('express-rate-limit');

// Security headers
app.use(helmet());

// CORS configuration
app.use(cors({
  origin: process.env.CLIENT_URL,
  credentials: true
}));

// Rate limiting
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000, // 15 minutes
  max: 100, // Limit each IP to 100 requests per windowMs
  message: 'Too many requests from this IP'
});

app.use('/api/', limiter);

// Stricter rate limiting for auth routes
const authLimiter = rateLimit({
  windowMs: 15 * 60 * 1000,
  max: 5,
  message: 'Too many authentication attempts'
});

app.use('/api/auth/', authLimiter);
```

### Input Validation & Sanitization
```javascript
const { body, validationResult } = require('express-validator');
const mongoSanitize = require('express-mongo-sanitize');

// Prevent NoSQL injection
app.use(mongoSanitize());

// Validation middleware
const validateInput = [
  body('email').isEmail().normalizeEmail(),
  body('password').isLength({ min: 6 }).matches(/^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)/),
  body('name').trim().escape().isLength({ min: 2, max: 50 })
];

app.post('/register', validateInput, (req, res) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(400).json({ errors: errors.array() });
  }
  // Process registration
});
```

### Environment Variables
```bash
npm install dotenv
```

```javascript
// .env file
NODE_ENV=development
PORT=3000
JWT_SECRET=your-super-secret-jwt-key
DB_CONNECTION=mongodb://localhost:27017/myapp
SESSION_SECRET=your-session-secret

// app.js
require('dotenv').config();

const port = process.env.PORT || 3000;
const jwtSecret = process.env.JWT_SECRET;
```

### Data Encryption
```javascript
// Encrypt sensitive data
const crypto = require('crypto');

const encrypt = (text) => {
  const cipher = crypto.createCipher('aes-256-cbc', process.env.ENCRYPTION_KEY);
  let encrypted = cipher.update(text, 'utf8', 'hex');
  encrypted += cipher.final('hex');
  return encrypted;
};

const decrypt = (encryptedText) => {
  const decipher = crypto.createDecipher('aes-256-cbc', process.env.ENCRYPTION_KEY);
  let decrypted = decipher.update(encryptedText, 'hex', 'utf8');
  decrypted += decipher.final('utf8');
  return decrypted;
};
```

---

## Testing

### Unit Testing with Jest
```bash
npm install --save-dev jest supertest
```

```javascript
// __tests__/app.test.js
const request = require('supertest');
const app = require('../app');

describe('GET /api/users', () => {
  it('should return all users', async () => {
    const res = await request(app)
      .get('/api/users')
      .expect(200);
    
    expect(res.body).toHaveProperty('users');
    expect(Array.isArray(res.body.users)).toBe(true);
  });
});

describe('POST /api/users', () => {
  it('should create a new user', async () => {
    const userData = {
      name: 'John Doe',
      email: 'john@example.com',
      password: 'password123'
    };
    
    const res = await request(app)
      .post('/api/users')
      .send(userData)
      .expect(201);
    
    expect(res.body.user.name).toBe(userData.name);
    expect(res.body.user.email).toBe(userData.email);
  });
  
  it('should return validation error for invalid email', async () => {
    const userData = {
      name: 'John Doe',
      email: 'invalid-email',
      password: 'password123'
    };
    
    const res = await request(app)
      .post('/api/users')
      .send(userData)
      .expect(400);
    
    expect(res.body.errors).toBeDefined();
  });
});
```

### Integration Testing
```javascript
// __tests__/integration/auth.test.js
const request = require('supertest');
const app = require('../../app');
const User = require('../../models/User');

describe('Auth Integration Tests', () => {
  beforeEach(async () => {
    await User.deleteMany({});
  });
  
  describe('POST /api/auth/register', () => {
    it('should register a new user and return token', async () => {
      const userData = {
        name: 'Test User',
        email: 'test@example.com',
        password: 'password123'
      };
      
      const res = await request(app)
        .post('/api/auth/register')
        .send(userData)
        .expect(201);
      
      expect(res.body.token).toBeDefined();
      expect(res.body.user.email).toBe(userData.email);
      
      // Verify user was created in database
      const user = await User.findOne({ email: userData.email });
      expect(user).toBeTruthy();
    });
  });
  
  describe('POST /api/auth/login', () => {
    it('should login user and return token', async () => {
      // Create user first
      const user = new User({
        name: 'Test User',
        email: 'test@example.com',
        password: await bcrypt.hash('password123', 10)
      });
      await user.save();
      
      const res = await request(app)
        .post('/api/auth/login')
        .send({
          email: 'test@example.com',
          password: 'password123'
        })
        .expect(200);
      
      expect(res.body.token).toBeDefined();
    });
  });
});
```

### Testing with Database
```javascript
// __tests__/setup.js
const mongoose = require('mongoose');

beforeAll(async () => {
  await mongoose.connect('mongodb://localhost:27017/test_db', {
    useNewUrlParser: true,
    useUnifiedTopology: true,
  });
});

afterAll(async () => {
  await mongoose.connection.close();
});

afterEach(async () => {
  const collections = mongoose.connection.collections;
  for (const key in collections) {
    const collection = collections[key];
    await collection.deleteMany();
  }
});
```

---

## Performance & Optimization

### Caching
```bash
npm install redis
```

```javascript
const redis = require('redis');
const client = redis.createClient();

// Cache middleware
const cache = (duration = 300) => {
  return async (req, res, next) => {
    const key = req.originalUrl;
    
    try {
      const cached = await client.get(key);
      if (cached) {
        return res.json(JSON.parse(cached));
      }
      
      // Store original json method
      const originalJson = res.json;
      
      // Override json method to cache response
      res.json = function(data) {
        client.setex(key, duration, JSON.stringify(data));
        return originalJson.call(this, data);
      };
      
      next();
    } catch (error) {
      next();
    }
  };
};

// Usage
app.get('/api/users', cache(600), async (req, res) => {
  const users = await User.find();
  res.json(users);
});
```

### Database Optimization
```javascript
// Pagination
app.get('/api/users', async (req, res) => {
  const page = parseInt(req.query.page) || 1;
  const limit = parseInt(req.query.limit) || 10;
  const skip = (page - 1) * limit;
  
  const users = await User.find()
    .skip(skip)
    .limit(limit)
    .select('-password');
  
  const total = await User.countDocuments();
  
  res.json({
    users,
    pagination: {
      page,
      pages: Math.ceil(total / limit),
      total
    }
  });
});

// Indexing
userSchema.index({ email: 1 });
userSchema.index({ name: 1, email: 1 });

// Populate optimization
app.get('/api/posts', async (req, res) => {
  const posts = await Post.find()
    .populate('author', 'name email')
    .lean(); // Returns plain JavaScript objects
  
  res.json(posts);
});
```

### Compression & Minification
```bash
npm install compression
```

```javascript
const compression = require('compression');

// Compress all responses
app.use(compression({
  level: 6,
  threshold: 1024,
  filter: (req, res) => {
    if (req.headers['x-no-compression']) {
      return false;
    }
    return compression.filter(req, res);
  }
}));
```

### Clustering
```javascript
// cluster.js
const cluster = require('cluster');
const numCPUs = require('os').cpus().length;

if (cluster.isMaster) {
  console.log(`Master ${process.pid} is running`);
  
  // Fork workers
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }
  
  cluster.on('exit', (worker, code, signal) => {
    console.log(`Worker ${worker.process.pid} died`);
    cluster.fork();
  });
} else {
  // Workers share the same server port
  require('./app');
  console.log(`Worker ${process.pid} started`);
}
```

---

## Deployment

### Environment Configuration
```javascript
// config/config.js
module.exports = {
  development: {
    port: process.env.PORT || 3000,
    database: process.env.DB_URL || 'mongodb://localhost:27017/myapp_dev',
    jwtSecret: process.env.JWT_SECRET || 'dev-secret'
  },
  production: {
    port: process.env.PORT || 80,
    database: process.env.DB_URL,
    jwtSecret: process.env.JWT_SECRET
  },
  test: {
    port: process.env.PORT || 3001,
    database: process.env.DB_URL || 'mongodb://localhost:27017/myapp_test',
    jwtSecret: 'test-secret'
  }
};

// Usage
const config = require('./config/config')[process.env.NODE_ENV || 'development'];
```

### Docker Setup
```dockerfile
# Dockerfile
FROM node:16-alpine

WORKDIR /app

COPY package*.json ./
RUN npm ci --only=production

COPY . .

EXPOSE 3000

USER node

CMD ["npm", "start"]
```

```yaml
# docker-compose.yml
version: '3.8'
services:
  app:
    build: .
    ports:
      - "3000:3000"
    environment:
      - NODE_ENV=production
      - DB_URL=mongodb://mongo:27017/myapp
    depends_on:
      - mongo
    
  mongo:
    image: mongo:5.0
    ports:
      - "27017:27017"
    volumes:
      - mongo_data:/data/db

volumes:
  mongo_data:
```

### Process Management with PM2
```bash
npm install -g pm2
```

```javascript
// ecosystem.config.js
module.exports = {
  apps: [{
    name: 'myapp',
    script: './app.js',
    instances: 'max',
    exec_mode: 'cluster',
    env: {
      NODE_ENV: 'development',
      PORT: 3000
    },
    env_production: {
      NODE_ENV: 'production',
      PORT: 80
    },
    error_file: './logs/err.log',
    out_file: './logs/out.log',
    log_file: './logs/combined.log'
  }]
};
```

### Nginx Configuration
```nginx
# /etc/nginx/sites-available/myapp
server {
    listen 80;
    server_name yourdomain.com;
    
    location / {
        proxy_pass http://localhost:3000;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_cache_bypass $http_upgrade;
    }
    
    location /static/ {
        alias /path/to/your/static/files/;
        expires 1y;
        add_header Cache-Control "public, immutable";
    }
}
```

---

## Advanced Topics

### WebSocket Integration
```bash
npm install socket.io
```

```javascript
const http = require('http');
const socketIo = require('socket.io');

const server = http.createServer(app);
const io = socketIo(server, {
  cors: {
    origin: "http://localhost:3000",
    methods: ["GET", "POST"]
  }
});

// Socket.io middleware
io.use((socket, next) => {
  // Authentication logic
  const token = socket.handshake.auth.token;
  if (token) {
    jwt.verify(token, process.env.JWT_SECRET, (err, decoded) => {
      if (err) return next(new Error('Authentication error'));
      socket.userId = decoded.userId;
      next();
    });
  } else {
    next(new Error('Authentication error'));
  }
});

io.on('connection', (socket) => {
  console.log('User connected:', socket.userId);
  
  socket.on('join-room', (roomId) => {
    socket.join(roomId);
    socket.to(roomId).emit('user-joined', socket.userId);
  });
  
  socket.on('send-message', (data) => {
    socket.to(data.room).emit('receive-message', {
      message: data.message,
      userId: socket.userId,
      timestamp: new Date()
    });
  });
  
  socket.on('disconnect', () => {
    console.log('User disconnected:', socket.userId);
  });
});

server.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

### GraphQL Integration
```bash
npm install apollo-server-express graphql
```

```javascript
const { ApolloServer, gql } = require('apollo-server-express');

// GraphQL schema
const typeDefs = gql`
  type User {
    id: ID!
    name: String!
    email: String!
    posts: [Post!]!
  }
  
  type Post {
    id: ID!
    title: String!
    content: String!
    author: User!
  }
  
  type Query {
    users: [User!]!
    user(id: ID!): User
    posts: [Post!]!
  }
  
  type Mutation {
    createUser(name: String!, email: String!): User!
    createPost(title: String!, content: String!, authorId: ID!): Post!
  }
`;

// Resolvers
const resolvers = {
  Query: {
    users: async () => await User.find(),
    user: async (_, { id }) => await User.findById(id),
    posts: async () => await Post.find().populate('author')
  },
  
  Mutation: {
    createUser: async (_, { name, email }) => {
      const user = new User({ name, email });
      return await user.save();
    },
    
    createPost: async (_, { title, content, authorId }) => {
      const post = new Post({ title, content, author: authorId });
      return await post.save();
    }
  },
  
  User: {
    posts: async (user) => await Post.find({ author: user._id })
  }
};

// Create Apollo Server
const server = new ApolloServer({
  typeDefs,
  resolvers,
  context: ({ req }) => {
    // Add authentication context
    const token = req.headers.authorization || '';
    return { token };
  }
});

// Apply middleware
server.applyMiddleware({ app, path: '/graphql' });
```

### Event-Driven Architecture
```javascript
const EventEmitter = require('events');

class AppEventEmitter extends EventEmitter {}
const appEvents = new AppEventEmitter();

// Event handlers
appEvents.on('user:created', async (user) => {
  console.log('New user created:', user.email);
  // Send welcome email
  await sendWelcomeEmail(user);
});

appEvents.on('user:login', async (user) => {
  console.log('User logged in:', user.email);
  // Update last login
  await User.findByIdAndUpdate(user._id, { lastLogin: new Date() });
});

// Emit events in routes
app.post('/register', async (req, res) => {
  try {
    const user = new User(req.body);
    await user.save();
    
    // Emit event
    appEvents.emit('user:created', user);
    
    res.status(201).json(user);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
});
```

### Microservices Architecture
```javascript
// User Service
const express = require('express');
const app = express();

app.get('/users', async (req, res) => {
  const users = await User.find();
  res.json(users);
});

app.listen(3001, () => {
  console.log('User service running on port 3001');
});

// API Gateway
const express = require('express');
const httpProxy = require('http-proxy-middleware');

const app = express();

// Route to user service
app.use('/api/users', httpProxy({
  target: 'http://localhost:3001',
  changeOrigin: true,
  pathRewrite: {
    '^/api/users': '/users'
  }
}));

// Route to post service
app.use('/api/posts', httpProxy({
  target: 'http://localhost:3002',
  changeOrigin: true,
  pathRewrite: {
    '^/api/posts': '/posts'
  }
}));

app.listen(3000, () => {
  console.log('API Gateway running on port 3000');
});
```

### Logging & Monitoring
```bash
npm install winston morgan
```

```javascript
const winston = require('winston');
const morgan = require('morgan');

// Winston logger configuration
const logger = winston.createLogger({
  level: 'info',
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.errors({ stack: true }),
    winston.format.json()
  ),
  defaultMeta: { service: 'myapp' },
  transports: [
    new winston.transports.File({ filename: 'logs/error.log', level: 'error' }),
    new winston.transports.File({ filename: 'logs/combined.log' })
  ]
});

if (process.env.NODE_ENV !== 'production') {
  logger.add(new winston.transports.Console({
    format: winston.format.simple()
  }));
}

// HTTP request logging
app.use(morgan('combined', {
  stream: {
    write: (message) => logger.info(message.trim())
  }
}));

// Use logger in application
app.get('/api/users', async (req, res) => {
  try {
    logger.info('Fetching users');
    const users = await User.find();
    res.json(users);
  } catch (error) {
    logger.error('Error fetching users:', error);
    res.status(500).json({ error: 'Internal server error' });
  }
});
```

---

## Best Practices

### Project Structure
```
myapp/
├── config/
│   ├── database.js
│   ├── config.js
│   └── passport.js
├── controllers/
│   ├── authController.js
│   ├── userController.js
│   └── postController.js
├── middleware/
│   ├── auth.js
│   ├── validation.js
│   └── errorHandler.js
├── models/
│   ├── User.js
│   ├── Post.js
│   └── index.js
├── routes/
│   ├── auth.js
│   ├── users.js
│   ├── posts.js
│   └── index.js
├── services/
│   ├── emailService.js
│   ├── authService.js
│   └── userService.js
├── utils/
│   ├── helpers.js
│   └── constants.js
├── tests/
│   ├── unit/
│   └── integration/
├── public/
├── views/
├── .env
├── .gitignore
├── app.js
└── server.js
```

### Code Organization
```javascript
// controllers/userController.js
const User = require('../models/User');
const { validationResult } = require('express-validator');

exports.getUsers = async (req, res, next) => {
  try {
    const users = await User.find().select('-password');
    res.json(users);
  } catch (error) {
    next(error);
  }
};

exports.createUser = async (req, res, next) => {
  try {
    const errors = validationResult(req);
    if (!errors.isEmpty()) {
      return res.status(400).json({ errors: errors.array() });
    }
    
    const user = new User(req.body);
    await user.save();
    res.status(201).json(user);
  } catch (error) {
    next(error);
  }
};

// routes/users.js
const express = require('express');
const router = express.Router();
const userController = require('../controllers/userController');
const { validateUser } = require('../middleware/validation');
const auth = require('../middleware/auth');

router.get('/', userController.getUsers);
router.post('/', validateUser, userController.createUser);

module.exports = router;
```

### Error Handling Best Practices
```javascript
// utils/AppError.js
class AppError extends Error {
  constructor(message, statusCode) {
    super(message);
    this.statusCode = statusCode;
    this.status = `${statusCode}`.startsWith('4') ? 'fail' : 'error';
    this.isOperational = true;
    
    Error.captureStackTrace(this, this.constructor);
  }
}

module.exports = AppError;

// utils/catchAsync.js
module.exports = (fn) => {
  return (req, res, next) => {
    fn(req, res, next).catch(next);
  };
};

// Usage
const catchAsync = require('../utils/catchAsync');
const AppError = require('../utils/AppError');

exports.getUser = catchAsync(async (req, res, next) => {
  const user = await User.findById(req.params.id);
  
  if (!user) {
    return next(new AppError('No user found with that ID', 404));
  }
  
  res.json(user);
});
```

---

## Useful Resources

### Documentation
- [Express.js Official Documentation](https://expressjs.com/)
- [Node.js Documentation](https://nodejs.org/docs/)
- [MongoDB Documentation](https://docs.mongodb.com/)
- [Mongoose Documentation](https://mongoosejs.com/docs/)

### Tools & Libraries
- **Development**: nodemon, dotenv, debug
- **Security**: helmet, cors, express-rate-limit, bcryptjs
- **Validation**: express-validator, joi
- **Authentication**: passport, jsonwebtoken
- **Testing**: jest, supertest, mocha, chai
- **Logging**: winston, morgan
- **Process Management**: pm2, forever
- **Documentation**: swagger-jsdoc, swagger-ui-express

### Performance Tips
1. Use compression middleware
2. Implement caching strategies
3. Optimize database queries
4. Use connection pooling
5. Implement proper indexing
6. Use CDN for static assets
7. Monitor and profile your application
8. Use clustering for CPU-intensive tasks

### Security Checklist
- [ ] Use HTTPS in production
- [ ] Implement rate limiting
- [ ] Validate and sanitize all inputs
- [ ] Use helmet for security headers
- [ ] Implement proper authentication
- [ ] Use environment variables for secrets
- [ ] Keep dependencies updated
- [ ] Implement proper error handling
- [ ] Use CORS appropriately
- [ ] Implement logging and monitoring

This guide covers Express.js from basic concepts to advanced topics. Practice building projects and gradually implement more advanced features as you become comfortable with the fundamentals.
