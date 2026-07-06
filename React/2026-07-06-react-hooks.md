# React Hooks

## Overview
React Hooks are functions that let you "hook into" React state and lifecycle features from function components. They were introduced in React 16.8, allowing developers to use state and other React features without writing a class.

## Why It Matters
Hooks simplify state management and side effects in functional components, making code more reusable and easier to understand. They eliminate the need for complex class lifecycle methods and enable better code sharing through custom hooks.

## Real-world Usage
In a typical React application, you might use `useState` to manage local state, `useEffect` for data fetching or subscriptions, and `useContext` for consuming context. Custom hooks allow you to encapsulate reusable logic, such as form validation or data fetching logic, and share it across components.

## Code Example
```jsx
import React, { useState, useEffect } from 'react';

function UserProfile({ userId }) {
  const [user, setUser] = useState(null);
  const [loading, setLoading] = useState(true);

  useEffect(() => {
    async function fetchUser() {
      const response = await fetch(`/api/users/${userId}`);
      const userData = await response.json();
      setUser(userData);
      setLoading(false);
    }

    fetchUser();
  }, [userId]);

  if (loading) return <div>Loading...</div>;
  if (!user) return <div>User not found</div>;

  return (
    <div>
      <h2>{user.name}</h2>
      <p>Email: {user.email}</p>
      <p>Role: {user.role}</p>
    </div>
  );
}

export default UserProfile;
```

## Common Mistakes
- Forgetting to include dependencies in the `useEffect` dependency array, leading to stale closures or infinite loops.
- Calling hooks inside loops, conditions, or nested functions, which violates the rules of hooks.
- Overusing `useMemo` and `useCallback` for performance optimization when not necessary, adding unnecessary complexity.

## Interview Question
What are the rules of hooks, and why are they important?

## Key Takeaways
- Hooks must be called at the top level of a React function component or custom hook.
- Hooks can only be called from React function components or custom hooks.
- `useState` adds local state to functional components.
- `useEffect` handles side effects like data fetching, subscriptions, or manual DOM manipulation.
- Custom hooks enable sharing stateful logic between components without changing the component hierarchy.