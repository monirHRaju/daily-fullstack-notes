# React Server Components

## Overview
React Server Components (RSC) are a new feature in React that allows developers to write components that are rendered exclusively on the server. They enable fetching data directly in the component without the need for API routes or useEffect, reducing client-side JavaScript bundle size and improving initial load performance.

## Why It Matters
- **Performance**: By rendering on the server, RSCs eliminate the need for client-side data fetching for certain parts of the UI, reducing waterfall requests.
- **Bundle Size**: Server Components are not included in the client bundle, leading to smaller JavaScript payloads.
- **Data Access**: They have direct access to backend resources (databases, file systems) without exposing API endpoints.
- **Streaming**: React can stream Server Components to the client as they become ready, improving perceived performance.

## Real-world Usage
- **E-commerce product listings**: Fetch product data from a CMS or database on the server and render the list without client-side fetching.
- **Blog posts**: Render markdown content from a headless CMS directly in a Server Component.
- **Dashboard widgets**: Fetch aggregated data from multiple services and render UI components server-side.

## Code Example
```jsx
// server/components/ProductList.server.jsx
import { db } from '@/lib/db' // Assume a Prisma client

export default async function ProductList() {
  const products = await db.product.findMany({
    where: { isPublished: true },
    select: { id: true, name: true, price: true, image: true }
  })

  return (
    <section>
      <h2>Featured Products</h2>
      <ul>
        {products.map(p => (
          <li key={p.id}>
            <img src={p.image} alt={p.name} />
            <h3>{p.name}</h3>
            <p>${p.price}</p>
          </li>
        ))}
      </ul>
    </section>
  )
}

// In a Client Component that uses the Server Component
// app/page.jsx
import ProductList from '@/server/components/ProductList.server'

export default function HomePage() {
  return (
    <main>
      <ProductList />
      {/* Other client-interactive components */}
    </main>
  )
}
```

## Common Mistakes
- **Using state or effects in Server Components**: Server Components cannot use useState, useEffect, or any hooks that rely on client-side lifecycle.
- **Assuming access to browser APIs**: Server Components run in a Node-like environment; they cannot access `window`, `document`, or `navigator`.
- **Passing large objects as props**: Props passed from Server to Client Components are serialized; avoid passing large or complex objects (like database models) directly.
- **Forgetting the `.server.jsx` convention**: In Next.js App Router, Server Components must be marked with `.server.jsx` or `.server.tsx` (or be in a `server` folder) to be treated as such.

## Interview Question
**Q: How do React Server Components differ from traditional Server-Side Rendering (SSR)?**
**A**: Traditional SSR (like in Next.js pages) renders the entire page on the server for each request, sending HTML to the client, which then hydrates with React to become interactive. React Server Components, however, allow selective rendering of components on the server while keeping other parts as Client Components. The server renders only the Server Components and sends a special format (not HTML) that the client uses to merge with Client Component trees, reducing JavaScript bundle size and enabling fine-grained data fetching.

## Key Takeaways
- React Server Components render on the server and are not included in the client bundle.
- They enable direct data fetching without API routes, reducing request waterfalls.
- They cannot use client-side hooks or browser APIs.
- In Next.js App Router, they are identified by `.server.jsx`/`.server.tsx` or placement in a `server` folder.
- They stream to the client, improving performance for content-heavy UIs.
