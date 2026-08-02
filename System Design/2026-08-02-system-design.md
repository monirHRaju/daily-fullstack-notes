# System Design Fundamentals

## Overview
System design is the process of defining the architecture, components, modules, interfaces, and data for a system to satisfy specified requirements. It is a critical skill for software engineers, especially for senior roles, as it involves making high-level decisions about system structure, technology choices, and trade-offs.

## Why It Matters
In today's world, applications need to handle millions of users, process large volumes of data, and remain available 24/7. System design enables engineers to build systems that are scalable, reliable, maintainable, and efficient. Mastering system design is crucial for cracking senior engineering interviews and building real-world applications that can grow with user demand.

## Real-world Usage
- Designing a URL shortening service like bit.ly
- Building a social media feed like Twitter or Facebook
- Creating a distributed caching system like Redis
- Architecting a microservices-based e-commerce platform
- Designing a chat application like WhatsApp or Slack

## Code Example
While system design is more about architecture than code, here's a simplified example of a URL shortener's core logic in Node.js:

```javascript
const crypto = require('crypto');
const urlDatabase = new Map();

function generateShortCode(longUrl) {
  // Generate a hash of the URL
  const hash = crypto.createHash('sha256').update(longUrl).digest('hex');
  // Use first 7 characters as the short code
  return hash.substring(0, 7);
}

function shortenUrl(longUrl) {
  const shortCode = generateShortCode(longUrl);
  urlDatabase.set(shortCode, longUrl);
  return `https://short.ly/${shortCode}`;
}

function resolveUrl(shortCode) {
  return urlDatabase.get(shortCode) || null;
}

// Example usage:
const shortUrl = shortenUrl('https://www.example.com/very/long/url/path/here');
console.log(shortUrl); // e.g., https://short.ly/a1b2c3d
console.log(resolveUrl('a1b2c3d')); // Returns the original URL
```

## Common Mistakes
- **Over-engineering**: Building overly complex solutions for simple problems.
- **Ignoring trade-offs**: Not considering the trade-offs between consistency, availability, and partition tolerance (CAP theorem).
- **Neglecting failure scenarios**: Failing to plan for hardware failures, network partitions, or software bugs.
- **Overlooking scalability**: Designing systems that work only for a small number of users.
- **Ignoring security**: Not considering authentication, authorization, data encryption, and input validation.

## Interview Question
Design a URL shortening service like bit.ly or TinyURL. Consider the following requirements:
  - Generate a short, unique alias for a given URL.
  - Redirect users to the original URL when they visit the short link.
  - Handle high traffic and ensure low latency.
  - Ensure the service is highly available and fault-tolerant.
  - Provide analytics on link usage (optional).

## Key Takeaways
- System design is about trade-offs and making informed decisions based on requirements.
- Start with a high-level overview before diving into details.
- Consider scalability, reliability, and efficiency from the beginning.
- Use established patterns and technologies (e.g., load balancers, caching, databases).
- Communicate your thought process clearly during interviews.
- Practice with real-world systems to build intuition.
