# Middleware Patterns in Express

## Overview
Express middleware functions are functions that have access to the request object (req), the response object (res), and the next middleware function in the application’s request‑response cycle. They can execute any code, make changes to the request and response objects, end the request‑response cycle, or call the next middleware in the stack.

## Why It Matters
- **Modularity**: Break complex logic into reusable, independent pieces.
- **Reusability**: Same middleware can be mounted on multiple routes or routers.
- **Control Flow**: Fine‑grained control over request handling, error handling, and response formatting.
- **Ecosystem**: Numerous third‑party middleware (e.g., morgan, helmet, cors) speed up development.
- **Maintainability**: Separation of concerns makes code easier to test and debug.

## Core Concepts
- **Application-level middleware**: Bound to an instance of express app using `app.use()` or `app.METHOD()`.
- **Router-level middleware**: Bound to an instance of `express.Router()`.
- **Error‑handling middleware**: Defined with four parameters `(err, req, res, next)`.
- **Built‑in middleware**: `express.json()`, `express.urlencoded()`, `express.static()`.
- **Third‑party middleware**: Installed via npm, e.g., `cookie-session`, `compression`.
- **Mounting**: Middleware can be mounted at a specific path prefix, affecting only routes under that path.

## Code Example
```javascript
const express = require('express');
const app = express();

// Custom logger middleware
function requestLogger(req, res, next) {
  console.log(`${new Date().toISOString()} ${req.method} ${req.url}`);
  next();
}

// Apply globally
app.use(requestLogger);

// Router‑level middleware for authentication
function authenticate(req, res, next) {
  const token = req.headers.authorization;
  if (token === 'valid-token') {
    return next();
  }
  return res.status(401).send({ error: 'Unauthorized' });
}

// Protect a router
const apiRouter = express.Router();
apiRouter.use(authenticate);
apiRouter.get('/data', (req, res) => res.json({ message: 'protected data' }));
app.use('/api', apiRouter);

// Error‑handling middleware (must be last)
app.use((err, req, res, next) => {
  console.error(err.stack);
  res.status(500).send({ error: 'Internal Server Error' });
});

app.listen(3000, () => console.log('Server listening on port 3000'));
```

## Real‑World Usage
- **Authentication & Authorization**: JWT verification, role‑based access control.
- **Input Validation**: Libraries like Joi or express‑validator as middleware.
- **Logging & Monitoring**: Request/response logging, performance timing.
- **Security**: Helmet, CORS, rate limiting.
- **Body Parsing**: JSON, URL‑encoded, multipart forms.
- **Compression**: gzip/deflate via compression middleware.
- **Static Assets**: Serving frontend builds with `express.static`.

## Best Practices
- Keep each middleware focused on a single responsibility.
* Place error‑handling middleware at the very end of the stack.
* Use async‑aware wrappers or try/catch inside async middleware to avoid unhandled rejections.
* Avoid synchronous blocking operations in middleware; offload to worker pools or async APIs.
* Document the order of middleware; mounting sequence matters.
* Use router‑level middleware to encapsulate related routes.
* Periodically audit and remove unused middleware to keep the stack lean.

## References
- Express.js Guide: https://expressjs.com/en/guide/using-middleware.html
- Awesome Middleware List: https://github.com/shannonmoeller/awesome-middleware