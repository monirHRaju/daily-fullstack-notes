# Advanced TypeScript Techniques for Full-Stack Development: Utility Types, Conditional Types, and Template Literals

## Introduction

In full-stack development with TypeScript, leveraging advanced type system features can significantly improve type safety and developer experience. This note explores three powerful advanced TypeScript features: utility types, conditional types, and template literal types.

## Utility Types

TypeScript provides a set of built-in utility types to facilitate common type transformations. These are globally available and can be used to manipulate types in useful ways.

### Common Utility Types

- `Partial<T>`: Makes all properties of T optional
- `Required<T>`: Makes all properties of T required
- `Readonly<T>`: Makes all properties of T readonly
- `Record<K, T>`: Constructs an object type with property keys K and values of type T
- `Pick<T, K>`: Creates a type by picking the set of properties K from T
- `Omit<T, K>`: Creates a type by omitting the specified properties K from T
- `Exclude<T, U>`: Excludes from T those types that are assignable to U
- `Extract<T, U>`: Extracts from T those types that are assignable to U
- `NonNullable<T>`: Removes null and undefined from T
- `Parameters<T>`: Obtains the parameters of a function type in a tuple
- `ReturnType<T>`: Obtains the return type of a function type
- `InstanceType<T>`: Obtains the instance type of a constructor function

### Example: API Response Handling

```typescript
// Define a base API response
interface ApiResponse<T> {
  success: boolean;
  data?: T;
  error?: string;
}

// Make all properties optional for partial updates
type PartialApiResponse<T> = Partial<ApiResponse<T>>;

// Make the data property required for successful responses
type SuccessApiResponse<T> = Required<Pick<ApiResponse<T>, 'success' | 'data'>> & Pick<ApiResponse<T>, 'error'>;
```

## Conditional Types

Conditional types allow types to be selected based on a condition, using the `extends` keyword.

### Syntax

```typescript
T extends U ? X : Y
```

Meaning: If T is assignable to U, then the type is X, otherwise Y.

### Distributive Conditional Types

When T is a union type, the conditional type is distributed over each member of the union.

### Example: Mapping API Responses

```typescript
type ApiResponse<T> = 
  T extends { success: true } 
    ? { data: T['data']; error?: never } 
    : { data?: never; error: T['error'] };

// Usage
type SuccessResponse = ApiResponse<{ success: true; data: string }>;
// Result: { data: string; error?: never }

type ErrorResponse = ApiResponse<{ success: false; error: string }>;
// Result: { data?: never; error: string }
```

### Useful Conditional Types

- `Exclude<T, U>`: Exclude from T those types that are assignable to U
- `Extract<T, U>`: Extract from T those types that are assignable to U
- `NonNullable<T>`: Exclude null and undefined from T
- `ReturnType<T>`: Obtain the return type of a function type
- `InstanceType<T>`: Obtain the instance type of a constructor function

## Template Literal Types

Template literal types build on string literal types and have the ability to expand into many strings via unions.

### Syntax

```typescript
\`${Type}\`
```

### Example: Event Emitters

```typescript
type EventName = 'click' | 'scroll' | 'mousemove';
type CallbackMap = {
  [K in \`\${EventName}Change\`]: (value: string) => void;
};
// Result:
// type CallbackMap = {
//   clickChange: (value: string) => void;
//   scrollChange: (value: string) => void;
//   mousemoveChange: (value: string) => void;
// }
```

### Example: CSS Properties in JavaScript

```typescript
type CssProperty = 'color' | 'background' | 'margin' | 'padding';
type CssValue = string | number;
type StyleObject = {
  [K in `\${CssProperty}-\${Capitalize<string>}`]?: CssValue;
};
// This allows for property names like 'colorRed', 'backgroundBlue', etc.
```

## Practical Full-Stack Example

Consider a full-stack application with a Node.js/Express backend and a React frontend. We can use these advanced types to ensure type safety across the boundary.

### Backend (Node.js/Express)

```typescript
import express, { Request, Response } from 'express';
const app = express();

// Define API response types
type ApiResponse<T> = 
  T extends { success: true } 
    ? { data: T['data']; error?: never } 
    : { data?: never; error: T['error'] };

// Handler for fetching user data
app.get('/api/user/:id', async (req: Request<{ id: string }>, res: Response) => {
  try {
    const userId = req.params.id;
    // Fetch user from database
    const user = await db.getUser(userId);
    if (!user) {
      res.status(404).json({ success: false, error: 'User not found' } as const);
    } else {
      res.json({ success: true, data: user } as const);
    }
  } catch (error) {
    res.status(500).json({ success: false, error: 'Internal server error' } as const);
  }
});
```

### Frontend (React)

```typescript
import axios from 'axios';

// Define the response type based on the backend's ApiResponse
type User = { id: string; name: string; email: string };
type UserResponse = ApiResponse<{ success: true; data: User }>;
// UserResponse is now { data: User; error?: never }

// Fetch user hook
async function fetchUser(userId: string): Promise<User> {
  const response = await axios.get<UserResponse>(`/api/user/${userId}`);
  // TypeScript knows that response.data has a `data` property of type User
  return response.data.data;
}
```

## Benefits

1. **Enhanced Type Safety**: Catch errors at compile time rather than runtime.
2. **Improved Developer Experience**: IDE autocompletion and inline documentation boost productivity.
3. **Better Code Maintainability**: Types serve as documentation and reduce the likelihood of breaking changes.
4. **Seamless Integration**: Works seamlessly with popular full-stack stacks like MERN, MEVN, or T3 stack.

## Conclusion

Advanced TypeScript features like utility types, conditional types, and template literal types empower full-stack developers to build more robust and maintainable applications. By leveraging these features, we can ensure type safety across the entire stack, reduce bugs, and improve overall code quality.

## References

- TypeScript Handbook: Utility Types - https://www.typescriptlang.org/docs/handbook/utility-types.html
- TypeScript Handbook: Conditional Types - https://www.typescriptlang.org/docs/handbook/2/conditional-types.html
- TypeScript Handbook: Template Literal Types - https://www.typescriptlang.org/docs/handbook/2/template-literal-types.html