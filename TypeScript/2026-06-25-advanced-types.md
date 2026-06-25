# Advanced TypeScript: Generics, Conditional Types, and Mapped Types

## Overview
TypeScript's type system goes beyond simple interfaces and enums, offering powerful features like generics, conditional types, and mapped types that enable developers to create flexible, reusable, and type-safe abstractions. These advanced type mechanisms allow for expressing complex relationships between types, reducing boilerplate, and catching more errors at compile time.

## Why It Matters
- **Abstraction**: Generics allow creating reusable components that work with a variety of types while maintaining type safety.
- **Flexibility**: Conditional types enable type-level computations, making it possible to create types that depend on other types.
- **Code Reduction**: Mapped types let you transform existing types in a declarative way, reducing the need for manual type definitions.
- **Library Development**: These features are essential for creating robust, type-safe libraries and frameworks that others can use confidently.

## Real-world Usage
- **Form Libraries**: Using generics to create form components that are typed according to the form's data shape.
- **API Clients**: Conditional types to infer return types from API endpoint definitions.
- **State Management Libraries**: Mapped types to create immutable versions of state objects or to generate action creators.
- **Utility Types**: Building custom utility types like `Pick`, `Omit`, `Record`, etc., using mapped and conditional types.

## Code Example
```typescript
// Generic function to merge two objects
function merge<T, U>(objA: T, objB: U): T & U {
  return { ...objA, ...objB };
}

// Conditional type: Extract return type of a function type
type ReturnType<T> = T extends (...args: any[]) => infer R ? R : never;

// Mapped property modifiers: Make all properties optional
type Partial<T> = {
  [P in keyof T]?: T[P];
};

// Example usage
interface User {
  id: number;
  name: string;
  email: string;
}

type UserDto = Pick<User, 'id' | 'name'>; // { id: number; name: string }
type UserPartial = Partial<User>; // All properties optional
type UserIdOnly = Pick<User, 'id'>;

// Advanced: Distributive conditional types
type ToArray<T> = T extends any ? T[] : never;
type StringOrNumberArray = ToArray<string | number>; // string[] | number[]
```

## Common Mistakes
- **Overcomplicating Types**: Using advanced types when simpler interfaces or types would suffice, reducing readability.
- **Losing Type Information**: Not preserving generic types in function returns, leading to `any` or loss of specificity.
- **Misunderstanding Distributive Behavior**: Conditional types distribute over union types, which can cause unexpected results if not accounted for.
- **Excessive Mappings**: Creating overly complex mapped types that are difficult to understand and maintain.
- **Ignoring Constraints**: Forgetting to constrain generics, allowing any type and defeating the purpose of type safety.

## Interview Question
**Q: How does TypeScript's distributive conditional type work, and when might you want to prevent distribution?**  
**A**: When a conditional type uses a naked type parameter (e.g., `T extends U ? X : Y`), TypeScript distributes the condition over each member of a union type `T`. For example, `T extends U ? X : Y` where `T` is `A | B` becomes `(A extends U ? X : Y) | (B extends U ? X : Y)`. To prevent distribution, wrap the type parameter in a tuple or a custom wrapper: `[T] extends [U] ? X : Y` or `T extends any ? (U extends T ? X : Y) : never` (though the latter is less common). This is useful when you want to treat the union as a single entity, such as when mapping over a tuple type where distribution would break the intended structure.

## Key Takeaways
- Generics, conditional types, and mapped types are pillars of TypeScript's advanced type system.
- They enable writing highly reusable and type-safe code, especially for library authors.
- Understanding distributive conditional types is crucial to avoid unexpected type transformations.
- Always consider readability; sometimes simpler types are better than overly clever advanced types.
- These features allow you to catch more errors at build time, improving overall code quality.