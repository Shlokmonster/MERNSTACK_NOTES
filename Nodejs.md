# 🚀 Complete Node.js Mastery Guide - Zero to Advanced

## 📑 Table of Contents

1. [**Foundation & Basics**](#foundation--basics)
2. [**Core Concepts**](#core-concepts)
3. [**Module System**](#module-system)
4. [**File System & I/O**](#file-system--io)
5. [**HTTP & Web Servers**](#http--web-servers)
6. [**Express.js Framework**](#expressjs-framework)
7. [**Database Integration**](#database-integration)
8. [**Authentication & Security**](#authentication--security)
9. [**Testing**](#testing)
10. [**Performance & Optimization**](#performance--optimization)
11. [**DevOps & Deployment**](#devops--deployment)
12. [**Advanced Patterns**](#advanced-patterns)
13. [**Real-time Applications**](#real-time-applications)
14. [**Microservices**](#microservices)
15. [**Error Handling & Debugging**](#error-handling--debugging)
16. [**Best Practices**](#best-practices)

---

## 🌟 Foundation & Basics

### What is Node.js?
- **JavaScript Runtime**: Built on Chrome's V8 JavaScript engine
- **Server-side JavaScript**: Run JavaScript outside the browser
- **Event-driven**: Non-blocking I/O operations
- **Single-threaded**: With event loop for concurrency
- **Cross-platform**: Windows, macOS, Linux

### Key Characteristics
```javascript
// Non-blocking I/O
console.log('Start');
setTimeout(() => console.log('Timer'), 0);
console.log('End');
// Output: Start, End, Timer

// Event-driven architecture
const EventEmitter = require('events');
const emitter = new EventEmitter();
emitter.on('message', (data) => console.log(data));
emitter.emit('message', 'Hello World!');
```

### Installation & Setup
```bash
# Install Node.js (from nodejs.org)
node --version          # Check Node.js version
npm --version           # Check npm version

# Node Version Manager (NVM)
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.0/install.sh | bash
nvm install node        # Install latest
nvm use 18.17.0         # Use specific version
nvm list                # List installed versions
```

### Node.js REPL (Read-Eval-Print Loop)
```bash
# Start REPL
node

# REPL Commands
.help                   # Show help
.exit                   # Exit REPL
.save filename          # Save session
.load filename          # Load file
.clear                  # Clear context
.break                  # Break multiline input
```

---

## 🔧 Core Concepts

### Global Objects
```javascript
// Global objects available without importing
console.log(__dirname);     // Current directory
console.log(__filename);    // Current file
console.log(process.cwd()); // Current working directory
console.log(process.env);   // Environment variables
console.log(process.argv);  // Command line arguments
console.log(process.pid);   // Process ID
```

### Process Object
```javascript
// Process information
process.version;            // Node.js version
process.platform;           // Operating system
process.arch;               // Architecture
process.memoryUsage();      // Memory usage
process.uptime();           // Uptime in seconds

// Process events
process.on('exit', (code) => {
  console.log(`Process exited with code: ${code}`);
});

process.on('SIGINT', () => {
  console.log('Received SIGINT. Graceful shutdown...');
  process.exit(0);
});

// Command line arguments
process.argv.forEach((val, index) => {
  console.log(`${index}: ${val}`);
});
```

### Buffer
```javascript
// Creating buffers
const buf1 = Buffer.alloc(10);                    // Allocate 10 bytes
const buf2 = Buffer.from('Hello');                // From string
const buf3 = Buffer.from([1, 2, 3, 4, 5]);      // From array

// Buffer operations
buf2.length;                    // Buffer length
buf2.toString();                // Convert to string
buf2.toString('hex');           // Convert to hex
buf2.write('Hi');               // Write to buffer
Buffer.concat([buf2, buf3]);    // Concatenate buffers
buf2.compare(buf3);             // Compare buffers
```

### Streams
```javascript
const fs = require('fs');

// Readable stream
const readStream = fs.createReadStream('input.txt');
readStream.on('data', (chunk) => {
  console.log(`Received ${chunk.length} bytes`);
});
readStream.on('end', () => {
  console.log('Stream ended');
});

// Writable stream
const writeStream = fs.createWriteStream('output.txt');
writeStream.write('Hello ');
writeStream.write('World!');
writeStream.end();

// Transform stream
const { Transform } = require('stream');
const upperCaseTransform = new Transform({
  transform(chunk, encoding, callback) {
    this.push(chunk.toString().toUpperCase());
    callback();
  }
});

// Pipe streams
readStream.pipe(upperCaseTransform).pipe(writeStream);
```

### Event Loop Deep Dive
```
┌───────────────────────────┐
┌─>│           timers          │  ← setTimeout, setInterval
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │     pending callbacks     │  ← I/O callbacks
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │       idle, prepare       │  ← Internal use
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │           poll            │  ← Fetching new I/O events
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
│  │           check           │  ← setImmediate callbacks
│  └─────────────┬─────────────┘
│  ┌─────────────┴─────────────┐
└──┤      close callbacks      │  ← close event callbacks
   └───────────────────────────┘
```

```javascript
// Event loop phases demonstration
console.log('=== START ===');

// Timers phase
setTimeout(() => console.log('Timer 1'), 0);
setTimeout(() => console.log('Timer 2'), 0);

// Check phase
setImmediate(() => console.log('Immediate 1'));
setImmediate(() => console.log('Immediate 2'));

// Poll phase
process.nextTick(() => console.log('Next Tick 1'));
process.nextTick(() => console.log('Next Tick 2'));

// Microtasks
Promise.resolve().then(() => console.log('Promise 1'));
Promise.resolve().then(() => console.log('Promise 2'));

console.log('=== END ===');
```

---

## 📦 Module System

### CommonJS (Default)
```javascript
// math.js - Export methods
function add(a, b) {
  return a + b;
}

function subtract(a, b) {
  return a - b;
}

// Different export styles
module.exports = { add, subtract };
// or
exports.add = add;
exports.subtract = subtract;
// or
module.exports.multiply = (a, b) => a * b;

// app.js - Import methods
const math = require('./math');
const { add, subtract } = require('./math');

console.log(math.add(5, 3));    // 8
console.log(subtract(5, 3));    // 2
```

### ES6 Modules
```javascript
// package.json
{
  "type": "module"
}

// math.mjs or math.js (with "type": "module")
export function add(a, b) {
  return a + b;
}

export function subtract(a, b) {
  return a - b;
}

export default function multiply(a, b) {
  return a * b;
}

// app.mjs or app.js
import multiply, { add, subtract } from './math.js';
import * as math from './math.js';

console.log(add(5, 3));         // 8
console.log(multiply(5, 3));    // 15
```

### Module Resolution
```javascript
// Built-in modules
const fs = require('fs');
const path = require('path');

// File modules
const myModule = require('./myModule');      // .js extension optional
const myModule = require('./myModule.js');
const myModule = require('./folder/');       // Looks for index.js

// Node modules
const express = require('express');          // From node_modules
const lodash = require('lodash');

// Module caching
require('./myModule');   // Loaded and cached
require('./myModule');   // Retrieved from cache

// Clear cache
delete require.cache[require.resolve('./myModule')];
```

### Creating npm Packages
```bash
# Initialize package
npm init

# Package.json structure
{
  "name": "my-awesome-package",
  "version": "1.0.0",
  "description": "An awesome Node.js package",
  "main": "index.js",
  "scripts": {
    "test": "jest",
    "start": "node index.js",
    "dev": "nodemon index.js"
  },
  "keywords": ["nodejs", "awesome"],
  "author": "Your Name <email@example.com>",
  "license": "MIT",
  "dependencies": {
    "express": "^4.18.2"
  },
  "devDependencies": {
    "jest": "^29.5.0",
    "nodemon": "^2.0.22"
  }
}

# Publish package
npm publish
```

---

## 📁 File System & I/O

### File Operations - Synchronous
```javascript
const fs = require('fs');
const path = require('path');

// Read file
try {
  const data = fs.readFileSync('file.txt', 'utf8');
  console.log(data);
} catch (err) {
  console.error('Error reading file:', err.message);
}

// Write file
fs.writeFileSync('output.txt', 'Hello World!');

// Append to file
fs.appendFileSync('log.txt', '\nNew log entry');

// Delete file
fs.unlinkSync('file.txt');

// File stats
const stats = fs.statSync('file.txt');
console.log('File size:', stats.size);
console.log('Is file:', stats.isFile());
console.log('Is directory:', stats.isDirectory());
console.log('Modified:', stats.mtime);

// Copy file
fs.copyFileSync('source.txt', 'destination.txt');

// Check file existence
if (fs.existsSync('file.txt')) {
  console.log('File exists');
}
```

### File Operations - Asynchronous
```javascript
const fs = require('fs');
const { promisify } = require('util');

// Callback-based
fs.readFile('file.txt', 'utf8', (err, data) => {
  if (err) throw err;
  console.log(data);
});

// Promise-based
const readFile = promisify(fs.readFile);
readFile('file.txt', 'utf8')
  .then(data => console.log(data))
  .catch(err => console.error(err));

// Using fs.promises
const fsPromises = require('fs').promises;
async function readFileAsync() {
  try {
    const data = await fsPromises.readFile('file.txt', 'utf8');
    console.log(data);
  } catch (err) {
    console.error('Error:', err.message);
  }
}
```

### Directory Operations
```javascript
const fs = require('fs');
const path = require('path');

// Create directory
fs.mkdirSync('new-folder');
fs.mkdirSync('nested/folder', { recursive: true });

// Read directory
const files = fs.readdirSync('.');
files.forEach(file => {
  console.log(file);
});

// Async directory reading
fs.readdir('.', (err, files) => {
  if (err) throw err;
  files.forEach(file => {
    console.log(file);
  });
});

// Walk directory recursively
function walkDir(dir) {
  const files = fs.readdirSync(dir);
  files.forEach(file => {
    const filePath = path.join(dir, file);
    const stats = fs.statSync(filePath);
    if (stats.isDirectory()) {
      walkDir(filePath);
    } else {
      console.log(filePath);
    }
  });
}
```

### File Watching
```javascript
const fs = require('fs');
const chokidar = require('chokidar'); // npm install chokidar

// Built-in file watching
fs.watchFile('file.txt', (curr, prev) => {
  console.log('File changed');
});

// Directory watching
fs.watch('.', (eventType, filename) => {
  console.log(`Event: ${eventType}, File: ${filename}`);
});

// Chokidar (better alternative)
const watcher = chokidar.watch('**/*.js');
watcher.on('change', path => console.log(`${path} changed`));
watcher.on('add', path => console.log(`${path} added`));
watcher.on('unlink', path => console.log(`${path} removed`));
```

### Working with Paths
```javascript
const path = require('path');

// Path operations
console.log(path.join('/users', 'john', 'documents'));     // /users/john/documents
console.log(path.resolve('file.txt'));                     // Absolute path
console.log(path.extname('file.txt'));                     // .txt
console.log(path.basename('/path/to/file.txt'));           // file.txt
console.log(path.dirname('/path/to/file.txt'));            // /path/to
console.log(path.parse('/path/to/file.txt'));              // Object with path parts

// Cross-platform paths
const filePath = path.join(__dirname, 'data', 'users.json');
```

---

## 🌐 HTTP & Web Servers

### Basic HTTP Server
```javascript
const http = require('http');
const url = require('url');

const server = http.createServer((req, res) => {
  const parsedUrl = url.parse(req.url, true);
  const path = parsedUrl.pathname;
  const method = req.method;
  const query = parsedUrl.query;

  // Set CORS headers
  res.setHeader('Access-Control-Allow-Origin', '*');
  res.setHeader('Access-Control-Allow-Methods', 'GET, POST, PUT, DELETE');
  res.setHeader('Access-Control-Allow-Headers', 'Content-Type');

  // Handle different routes
  if (path === '/' && method === 'GET') {
    res.writeHead(200, { 'Content-Type': 'text/html' });
    res.end('<h1>Welcome to my server!</h1>');
  } else if (path === '/api/users' && method === 'GET') {
    res.writeHead(200, { 'Content-Type': 'application/json' });
    res.end(JSON.stringify([{ id: 1, name: 'John' }]));
  } else {
    res.writeHead(404, { 'Content-Type': 'text/plain' });
    res.end('Not Found');
  }
});

server.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

### HTTPS Server
```javascript
const https = require('https');
const fs = require('fs');

const options = {
  key: fs.readFileSync('private-key.pem'),
  cert: fs.readFileSync('certificate.pem')
};

const server = https.createServer(options, (req, res) => {
  res.writeHead(200, { 'Content-Type': 'text/plain' });
  res.end('Hello HTTPS World!');
});

server.listen(443, () => {
  console.log('HTTPS Server running on port 443');
});
```

### HTTP Client
```javascript
const http = require('http');
const https = require('https');

// GET request
const req = http.get('http://api.example.com/users', (res) => {
  let data = '';
  res.on('data', chunk => data += chunk);
  res.on('end', () => {
    console.log(JSON.parse(data));
  });
});

// POST request
const postData = JSON.stringify({ name: 'John', age: 30 });
const options = {
  hostname: 'api.example.com',
  port: 80,
  path: '/users',
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
    'Content-Length': Buffer.byteLength(postData)
  }
};

const req2 = http.request(options, (res) => {
  console.log(`Status: ${res.statusCode}`);
  res.on('data', chunk => console.log(chunk.toString()));
});

req2.write(postData);
req2.end();
```

---

## 🚀 Express.js Framework

### Basic Express App
```javascript
const express = require('express');
const app = express();

// Middleware
app.use(express.json());                    // Parse JSON bodies
app.use(express.urlencoded({ extended: true })); // Parse URL-encoded bodies
app.use(express.static('public'));          // Serve static files

// Routes
app.get('/', (req, res) => {
  res.send('Hello Express!');
});

app.get('/api/users', (req, res) => {
  res.json([{ id: 1, name: 'John' }]);
});

app.post('/api/users', (req, res) => {
  const user = req.body;
  // Save user logic
  res.status(201).json(user);
});

app.listen(3000, () => {
  console.log('Server running on port 3000');
});
```

### Express Routing
```javascript
const express = require('express');
const router = express.Router();

// Route parameters
app.get('/users/:id', (req, res) => {
  const userId = req.params.id;
  res.json({ id: userId, name: 'John' });
});

// Query parameters
app.get('/search', (req, res) => {
  const query = req.query.q;
  const limit = req.query.limit || 10;
  res.json({ query, limit });
});

// Route handlers
app.get('/users/:id', getUserById, sendUserResponse);

function getUserById(req, res, next) {
  const user = users.find(u => u.id === req.params.id);
  if (!user) {
    return res.status(404).json({ error: 'User not found' });
  }
  req.user = user;
  next();
}

function sendUserResponse(req, res) {
  res.json(req.user);
}

// Router module
const userRouter = express.Router();
userRouter.get('/', getAllUsers);
userRouter.get('/:id', getUserById);
userRouter.post('/', createUser);
userRouter.put('/:id', updateUser);
userRouter.delete('/:id', deleteUser);

app.use('/api/users', userRouter);
```

### Express Middleware
```javascript
const express = require('express');
const morgan = require('morgan');
const helmet = require('helmet');
const cors = require('cors');

const app = express();

// Built-in middleware
app.use(express.json({ limit: '10mb' }));
app.use(express.urlencoded({ extended: true }));
app.use(express.static('public'));

// Third-party middleware
app.use(morgan('combined'));        // Logging
app.use(helmet());                  // Security headers
app.use(cors());                    // CORS

// Custom middleware
app.use((req, res, next) => {
  console.log(`${req.method} ${req.path} - ${new Date().toISOString()}`);
  next();
});

// Error handling middleware
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).json({ error: 'Something went wrong!' });
});

// 404 handler
app.use((req, res) => {
  res.status(404).json({ error: 'Route not found' });
});
```

### Template Engines
```javascript
const express = require('express');
const app = express();

// EJS
app.set('view engine', 'ejs');
app.set('views', './views');

app.get('/', (req, res) => {
  res.render('index', { title: 'My App', users: ['John', 'Jane'] });
});

// Handlebars
const exphbs = require('express-handlebars');
app.engine('handlebars', exphbs());
app.set('view engine', 'handlebars');

// Pug
app.set('view engine', 'pug');
```

---

## 🗄️ Database Integration

### MongoDB with Mongoose
```javascript
const mongoose = require('mongoose');

// Connection
mongoose.connect('mongodb://localhost:27017/myapp', {
  useNewUrlParser: true,
  useUnifiedTopology: true
});

// Schema
const userSchema = new mongoose.Schema({
  name: { type: String, required: true },
  email: { type: String, required: true, unique: true },
  age: { type: Number, min: 0 },
  createdAt: { type: Date, default: Date.now }
});

// Model
const User = mongoose.model('User', userSchema);

// CRUD operations
async function createUser(userData) {
  const user = new User(userData);
  return await user.save();
}

async function getUsers() {
  return await User.find({});
}

async function getUserById(id) {
  return await User.findById(id);
}

async function updateUser(id, updateData) {
  return await User.findByIdAndUpdate(id, updateData, { new: true });
}

async function deleteUser(id) {
  return await User.findByIdAndDelete(id);
}

// Advanced queries
const users = await User.find({ age: { $gte: 18 } })
  .sort({ name: 1 })
  .limit(10)
  .select('name email');
```

### PostgreSQL with Sequelize
```javascript
const { Sequelize, DataTypes } = require('sequelize');

// Connection
const sequelize = new Sequelize('database', 'username', 'password', {
  host: 'localhost',
  dialect: 'postgres'
});

// Model
const User = sequelize.define('User', {
  id: {
    type: DataTypes.INTEGER,
    primaryKey: true,
    autoIncrement: true
  },
  name: {
    type: DataTypes.STRING,
    allowNull: false
  },
  email: {
    type: DataTypes.STRING,
    allowNull: false,
    unique: true
  },
  age: {
    type: DataTypes.INTEGER,
    validate: { min: 0 }
  }
});

// Sync database
await sequelize.sync();

// CRUD operations
const user = await User.create({ name: 'John', email: 'john@example.com', age: 30 });
const users = await User.findAll();
const user = await User.findByPk(1);
await User.update({ age: 31 }, { where: { id: 1 } });
await User.destroy({ where: { id: 1 } });
```

### Redis
```javascript
const redis = require('redis');
const client = redis.createClient();

// Connect
client.connect();

// Basic operations
await client.set('key', 'value');
const value = await client.get('key');

// Hash operations
await client.hSet('user:1', 'name', 'John');
await client.hSet('user:1', 'email', 'john@example.com');
const user = await client.hGetAll('user:1');

// List operations
await client.lPush('tasks', 'task1');
await client.lPush('tasks', 'task2');
const tasks = await client.lRange('tasks', 0, -1);

// Set operations
await client.sAdd('tags', 'nodejs');
await client.sAdd('tags', 'javascript');
const tags = await client.sMembers('tags');

// Expiration
await client.setEx('session:123', 3600, 'user-data');
```

---

## 🔐 Authentication & Security

### JWT Authentication
```javascript
const jwt = require('jsonwebtoken');
const bcrypt = require('bcrypt');

// Generate JWT
function generateToken(user) {
  return jwt.sign(
    { id: user.id, email: user.email },
    process.env.JWT_SECRET,
    { expiresIn: '24h' }
  );
}

// Verify JWT
function verifyToken(token) {
  return jwt.verify(token, process.env.JWT_SECRET);
}

// Authentication middleware
function authenticate(req, res, next) {
  const token = req.header('Authorization')?.replace('Bearer ', '');
  
  if (!token) {
    return res.status(401).json({ error: 'Access denied' });
  }

  try {
    const decoded = verifyToken(token);
    req.user = decoded;
    next();
  } catch (error) {
    res.status(401).json({ error: 'Invalid token' });
  }
}

// Password hashing
async function hashPassword(password) {
  const saltRounds = 10;
  return await bcrypt.hash(password, saltRounds);
}

async function comparePassword(password, hash) {
  return await bcrypt.compare(password, hash);
}

// Login route
app.post('/login', async (req, res) => {
  const { email, password } = req.body;
  
  const user = await User.findOne({ email });
  if (!user) {
    return res.status(400).json({ error: 'Invalid credentials' });
  }

  const isValidPassword = await comparePassword(password, user.password);
  if (!isValidPassword) {
    return res.status(400).json({ error: 'Invalid credentials' });
  }

  const token = generateToken(user);
  res.json({ token });
});
```

### Session-based Authentication
```javascript
const session = require('express-session');
const MongoStore = require('connect-mongo');

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

// Login
app.post('/login', async (req, res) => {
  const { email, password } = req.body;
  
  const user = await User.findOne({ email });
  if (user && await comparePassword(password, user.password)) {
    req.session.userId = user.id;
    res.json({ message: 'Logged in successfully' });
  } else {
    res.status(400).json({ error: 'Invalid credentials' });
  }
});

// Logout
app.post('/logout', (req, res) => {
  req.session.destroy((err) => {
    if (err) {
      return res.status(500).json({ error: 'Could not log out' });
    }
    res.json({ message: 'Logged out successfully' });
  });
});
```

### Security Best Practices
```javascript
const helmet = require('helmet');
const rateLimit = require('express-rate-limit');
const mongoSanitize = require('express-mongo-sanitize');
const xss = require('xss-clean');

// Security middleware
app.use(helmet());                  // Security headers
app.use(mongoSanitize());          // Prevent NoSQL injection
app.use(xss());                    // Prevent XSS attacks

// Rate limiting
const limiter = rateLimit({
  windowMs: 15 * 60 * 1000,        // 15 minutes
  max: 100,                        // Limit each IP to 100 requests per windowMs
  message: 'Too many requests from this IP'
});
app.use('/api/', limiter);

// Input validation
const { body, validationResult } = require('express-validator');

const validateUser = [
  body('email').isEmail().normalizeEmail(),
  body('password').isLength({ min: 6 }),
  body('name').trim().isLength({ min: 2, max: 50 })
];

app.post('/register', validateUser, (req, res) => {
  const errors = validationResult(req);
  if (!errors.isEmpty()) {
    return res.status(400).json({ errors: errors.array() });
  }
  // Registration logic
});
```

---

## 🧪 Testing

### Unit Testing with Jest
```javascript
// math.js
function add(a, b) {
  return a + b;
}

function divide(a, b) {
  if (b === 0) {
    throw new Error('Division by zero');
  }
  return a / b;
}

module.exports = { add, divide };

// math.test.js
const { add, divide } = require('./math');

describe('Math functions', () => {
  test('should add two numbers', () => {
    expect(add(2, 3)).toBe(5);
  });

  test('should divide two numbers', () => {
    expect(divide(10, 2)).toBe(5);
  });

  test('should throw error for division by zero', () => {
    expect(() => divide(10, 0)).toThrow('Division by zero');
  });
});

// Async testing
test('should fetch user data', async () => {
  const user = await fetchUser(1);
  expect(user).toHaveProperty('id');
  expect(user).toHaveProperty('name');
});

// Mock functions
jest.mock('./userService');
const userService = require('./userService');

test('should call userService.getUser', () => {
  userService.getUser.mockReturnValue({ id: 1, name: 'John' });
  
  const user = userService.getUser(1);
  expect(userService.getUser).toHaveBeenCalledWith(1);
  expect(user.name).toBe('John');
});
```

### Integration Testing with Supertest
```javascript
const request = require('supertest');
const app = require('./app');

describe('API endpoints', () => {
  test('GET /api/users', async () => {
    const response = await request(app)
      .get('/api/users')
      .expect(200);
    
    expect(response.body).toBeInstanceOf(Array);
  });

  test('POST /api/users', async () => {
    const userData = { name: 'John', email: 'john@example.com' };
    
    const response = await request(app)
      .post('/api/users')
      .send(userData)
      .expect(201);
    
    expect(response.body).toHaveProperty('id');
    expect(response.body.name).toBe('John');
  });

  test('GET /api/users/:id - not found', async () => {
    await request(app)
      .get('/api/users/999')
      .expect(404);
  });
});

// Test database setup
beforeEach(async () => {
  await User.deleteMany({});
  await User.create({ name: 'Test User', email: 'test@example.com' });
});

afterAll(async () => {
  await mongoose.connection.close();
});
```

### End-to-End Testing with Cypress
```javascript
// cypress/integration/user.spec.js
describe('User Management', () => {
  beforeEach(() => {
    cy.visit('/');
  });

  it('should create a new user', () => {
    cy.get('[data-cy=add-user-btn]').click();
    cy.get('[data-cy=user-name]').type('John Doe');
    cy.get('[data-cy=user-email]').type('john@example.com');
    cy.get('[data-cy=submit-btn]').click();
    
    cy.contains('John Doe').should('be.visible');
  });

  it('should edit user', () => {
    cy.get('[data-cy=edit-user-btn]').first().click();
    cy.get('[data-cy=user-name]').clear().type('Jane Doe');
    cy.get('[data-cy=submit-btn]').click();
    
    cy.contains('Jane Doe').should('be.visible');
  });
});
```

### Test Configuration
```javascript
// jest.config.js
module.exports = {
  testEnvironment: 'node',
  setupFilesAfterEnv: ['<rootDir>/tests/setup.js'],
  testMatch: ['**/__tests__/**/*.js', '**/?(*.)+(spec|test).js'],
  collectCoverageFrom: [
    'src/**/*.js',
    '!src/index.js',
    '!**/node_modules/**'
  ],
  coverageThreshold: {
    global: {
      branches: 80,
      functions: 80,
      lines: 80,
      statements: 80
    }
  }
};

// tests/setup.js
const mongoose = require('mongoose');
const { MongoMemoryServer } = require('mongodb-memory-server');

let mongod;

beforeAll(async () => {
  mongod = await MongoMemoryServer.create();
  const uri = mongod.getUri();
  await mongoose.connect(uri);
});

afterAll(async () => {
  await mongoose.connection.dropDatabase();
  await mongoose.connection.close();
  await mongod.stop();
});
```

---

## ⚡ Performance & Optimization

### Performance Monitoring
```javascript
const cluster = require('cluster');
const os = require('os');

// Cluster setup
if (cluster.isMaster) {
  const numCPUs = os.cpus().length;
  
  for (let i = 0; i < numCPUs; i++) {
    cluster.fork();
  }
  
  cluster.on('exit', (worker, code, signal) => {
    console.log(`Worker ${worker.process.pid} died`);
    cluster.fork();
  });
} else {
  // Worker process
  require('./app');
}

// Memory monitoring
setInterval(() => {
  const memUsage = process.memoryUsage();
  console.log('Memory Usage:', {
    rss: `${Math.round(memUsage.rss / 1024 / 1024 * 100) / 100} MB`,
    heapTotal: `${Math.round(memUsage.heapTotal / 1024 / 1024 * 100) / 100} MB`,
    heapUsed: `${Math.round(memUsage.heapUsed / 1024 / 1024 * 100) / 100} MB`
  });
}, 30000);

// Performance hooks
const { performance, PerformanceObserver } = require('perf_hooks');

const obs = new PerformanceObserver((list) => {
  list.getEntries().forEach((entry) => {
    console.log(`${entry.name}: ${entry.duration}ms`);
  });
});
obs.observe({ entryTypes: ['measure'] });

// Measure performance
performance.mark('start-operation');
// ... some operation
performance.mark('end-operation');
performance.measure('opera
