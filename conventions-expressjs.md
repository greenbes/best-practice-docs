# Express.js best practices and conventions

## General Guidelines

    - Use async/await consistently instead of mixing with callbacks
    - Generate swagger documentation for the API
    - Implement request validation using libraries like Joi or express-validator
    - Use TypeScript for better type safety and developer experience
    - Implement rate limiting for API endpoints
    - Use compression middleware for response optimization
    - Implement proper CORS policies
    - Create a robust service layer to handle business logic
    - Use dependency injection for better testing and modularity
    - Implement proper API versioning strategy
    - Use HTTP status codes consistently
    - Implement request ID tracking for better debugging
    - Use proper security headers (via helmet)
    - Implement proper authentication and authorization middleware
    - Use environment-specific configuration files

## Project Structure and Organization

```
project/
  ├── src/
  │   ├── controllers/    # Route handlers/business logic
  │   ├── models/         # Data models and database interactions
  │   ├── middleware/     # Custom middleware functions
  │   ├── routes/         # Route definitions
  │   ├── services/       # Business logic abstracted from controllers
  │   ├── utils/          # Helper functions and utilities
  │   └── config/         # Configuration files
  ├── tests/             # Test files mirroring src structure
  ├── logs/              # Application logs
  └── app.js            # Application entry point
```

## Middleware Organization

```javascript
// app.js
const express = require('express');
const helmet = require('helmet');
const morgan = require('morgan');

// Security middleware first
app.use(helmet());

// Logging middleware
app.use(morgan('combined'));

// Body parsing middleware
app.use(express.json());
app.use(express.urlencoded({ extended: true }));

// Route-specific middleware
app.use('/api', authMiddleware);
```

## Route Organization
```javascript
// routes/users.js
const express = require('express');
const router = express.Router();
const UserController = require('../controllers/UserController');

router.get('/', UserController.getUsers);
router.post('/', UserController.createUser);

module.exports = router;
```

## Error Handling

```javascript
// middleware/errorHandler.js
const errorHandler = (err, req, res, next) => {
  const status = err.status || 500;
  const message = err.message || 'Internal Server Error';
  
  // Structured error logging
  logger.error({
    status,
    message,
    stack: err.stack,
    requestId: req.id
  });

  res.status(status).json({
    error: {
      message,
      status,
      requestId: req.id
    }
  });
};

// app.js
app.use(errorHandler);
```

## Environment Configuration

```javascript
// config/config.js
require('dotenv').config();

module.exports = {
  port: process.env.PORT || 3000,
  database: {
    url: process.env.DATABASE_URL,
    options: {
      // database specific options
    }
  },
  logging: {
    level: process.env.LOG_LEVEL || 'info'
  }
};
```

## Controller Structure

```javascript
// controllers/UserController.js
class UserController {
  static async getUsers(req, res, next) {
    try {
      const users = await UserService.getUsers();
      res.json(users);
    } catch (error) {
      next(error);
    }
  }
}

module.exports = UserController;
```

## Testing Setup

```javascript
// tests/integration/users.test.js
const request = require('supertest');
const app = require('../../src/app');

describe('User API', () => {
  beforeEach(async () => {
    // Setup test database
  });

  it('should create a new user', async () => {
    const response = await request(app)
      .post('/api/users')
      .send({
        username: 'testuser',
        email: 'test@example.com'
      });
    expect(response.status).toBe(201);
  });
});
```

## Logging Strategy

```javascript
// utils/logger.js
const winston = require('winston');

const logger = winston.createLogger({
  level: config.logging.level,
  format: winston.format.combine(
    winston.format.timestamp(),
    winston.format.json()
  ),
  transports: [
    new winston.transports.File({ filename: 'logs/error.log', level: 'error' }),
    new winston.transports.File({ filename: 'logs/combined.log' })
  ]
});
```

