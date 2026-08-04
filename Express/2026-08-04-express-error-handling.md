# Express Error Handling Best Practices

## Overview
Express error handling middleware is a special type of middleware that has four arguments (err, req, res, next). It is used to catch and handle errors that occur in synchronous and asynchronous route handlers and middleware. Proper error handling ensures that the application responds with appropriate HTTP status codes and error messages, preventing crashes and leaking stack traces to clients.

## Why It Matters
- **Reliability**: Prevents uncaught exceptions from crashing the Node.js process.
- **Security**: Avoids leaking internal details (stack traces, file paths) to users.
- **User Experience**: Provides meaningful error messages and consistent JSON error format.
- **Maintainability**: Centralizes error handling logic, making it easier to update and test.
- **Debugging**: Enables logging of errors for monitoring and alerting.

## Real-world Usage
- **Validation Errors**: Return 400 with details from Joi or express-validator.
- **Authentication Errors**: Return 401 for invalid/missing tokens.
- **Authorization Errors**: Return 403 when user lacks permissions.
- **Not Found**: Return 404 for routes that don’t exist (often handled by a 404 middleware).
- **Database Errors**: Translate unique constraint violations to 409, validation errors to 400.
- **Third‑party Service Failures**: Return 502 or 503 with a generic message while logging the upstream error.
- **Rate Limiting**: Respond with 429 when clients exceed allowed requests.

## Code Example
```javascript
const express = require('express');
const app = express();
const Joi = require('joi');

// Middleware to parse JSON bodies
app.use(express.json());

// Validation helper
function validateSchema(schema) {
  return (req, res, next) => {
    const { error } = schema.validate(req.body);
    if (error) {
      return res.status(400).json({ error: error.details[0].message });
    }
    next();
  }
}

// Example route with validation
app.post(
  '/users',
  validateSchema(Joi.object({
    name: Joi.string().min(2).required(),
    email: Joi.string().email().required(),
    age: Joi.number().min(18).max(100)
  })),
  (req, res) => {
    // Simulate async operation that might throw
    const user = { id: Date.now(), ...req.body };
    // Pretend DB error
    if (Math.random() < 0.1) {
      throw new Error('Database connection failed');
    }
    res.status(201).json(user);
  }
);

// 404 handler (must be after all routes)
app.use((req, res) => {
  res.status(404).json({ error: 'Route not found' });
});

// Central error-handling middleware
app.use((err, req, res, next) => {
  console.error('Error:', err); // In production, use a proper logger
  // Default to 500 if status not set
  const status = err.status || 500;
  // Do not leak error details in production
  const message =
    process.env.NODE_ENV === 'development'
      ? err.message
      : 'Internal Server Error';
  res.status(status).json({ error: message });
});

const PORT = process.env.PORT || 3000;
app.listen(PORT, () => console.log(`Server listening on port ${PORT}`));
```

## Common Mistakes
- **Using regular middleware signature (err, req, res) instead of four arguments** – Express won’t recognize it as an error handler.
- **Calling next() after sending a response** – Can cause “Cannot set headers after they are sent” errors.
- **Throwing errors inside asynchronous callbacks without wrapping them** – Leads to unhandled promise rejections.
- **Sending stack traces to the client in production** – Exposes internal implementation details.
- **Forgetting to handle errors in async route handlers** – Results in pending promises that never resolve.
- **Using next(err) in a route that already sent a partial response** – Causes header errors.
- **Neglecting to set Content-Type to application/json** when sending JSON error responses (though Express sets it automatically when using json()).

## Interview Question
**Question:** How would you implement centralized error handling in an Express application that distinguishes between operational errors (e.g., validation, not found) and programmer errors (e.g., null reference), and ensures that stack traces are logged but not exposed to the client in production?

**Answer:**  
Create a custom error class (e.g., `AppError`) that includes an HTTP status code and a flag indicating whether the error is operational. In route handlers and middleware, instantiate this class for expected operational errors (validation failures, not found, etc.) and pass it to `next(err)`. For unexpected programmer errors, let the thrown exception be caught by an async wrapper or try/catch and passed to `next(err)`. The final error‑handling middleware checks `err.isOperational` (or the status code) to decide whether to send `err.message` to the client or a generic message. In all cases, log the full error stack using a logger (e.g., Winston, Pino) before sending the response. In development, you may send the detailed message; in production, always send a generic message for non‑operational errors.

## Key Takeaways
- Always use four‑argument middleware `(err, req, res, next)` for error handling.
- Centralize error handling to avoid duplication and ensure consistent responses.
- Distinguish between operational and programmer errors; expose only safe messages to the client.
- Log full error details server‑side for monitoring and debugging.
- Remember to place the error‑handling middleware after all routes and middleware.
- Wrap asynchronous route handlers (or use utilities like `express-async-errors`) to ensure thrown errors are passed to `next`.
- Test error paths thoroughly to verify status codes, response format, and logging behavior.
