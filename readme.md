# Advanced Problem Solving with TypeScript & OOP

This repository contains my solutions for the TypeScript problem solving assignment, along with two blog posts on TypeScript topics.

## Files

- `solutions.ts` - Solutions to all 7 problems
- `blog-1.md` - Why `any` is a type safety hole and `unknown` is the safer choice, with type narrowing explained
- `blog-2.md` - How Generics help build reusable components that stay strictly typed

## Problems Solved

1. **filterEvenNumbers** - Filters even numbers from an array
2. **reverseString** - Reverses a given string
3. **checkType** - Uses a union type and type guards to identify string or number
4. **getProperty** - Generic function with a key constraint to get an object property
5. **toggleReadStatus** - Adds an `isRead` property to a `Book` object
6. **Person / Student** - Class inheritance with a `getDetails` method
7. **getIntersection** - Returns common elements of two number arrays

## How to Run

Install TypeScript if not already installed:

```bash
npm install -g typescript
```

Compile the file:

```bash
tsc solutions.ts
```

This generates `solutions.js`, which can be run with Node.js:

```bash
node solutions.js
```
