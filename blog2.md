# How Generics Help You Build Reusable Components That Stay Strictly Typed

## Introduction

One of the most common tensions in programming is between reusability and type safety. We want to write a function once and use it with many kinds of data, but we also want the compiler to catch our mistakes. Without generics we are forced to pick one side. Generics in TypeScript solve this tension: they let us write reusable code that adapts to whatever type is passed in, without losing a single bit of type checking. In this post I will show how that works and why it matters.

## The Problem Without Generics

Suppose we need a function that returns the first element of an array. Without generics we have two bad options.

Option one is writing a separate function for every type:

```typescript
function getFirstNumber(items: number[]): number {
  return items[0];
}

function getFirstString(items: string[]): string {
  return items[0];
}
```

This is pure duplication. The logic is identical, only the types differ.

Option two is using `any`:

```typescript
function getFirst(items: any[]): any {
  return items[0];
}

const first = getFirst(["a", "b", "c"]);
first.toFixed(2);
```

The function is reusable now, but the return type is `any`, so the connection between input and output is lost. The compiler happily accepts `first.toFixed(2)` even though `first` is actually a string, and the code crashes at runtime.

## Generics: Reusable and Strict at the Same Time

A generic function introduces a type parameter, usually named `T`, which acts as a placeholder that gets filled in when the function is called:

```typescript
function getFirst<T>(items: T[]): T {
  return items[0];
}

const firstNumber = getFirst([10, 20, 30]);
const firstName = getFirst(["Alice", "Bob"]);
```

Here TypeScript infers `T` automatically. `firstNumber` is typed as `number` and `firstName` is typed as `string`. If we now write `firstName.toFixed(2)`, the compiler immediately rejects it. We wrote the logic once, it works for every type, and the type information flows through untouched.

## Generic Interfaces and Classes

Generics are not limited to functions. They make data structures reusable too. A classic example is a typed container:

```typescript
interface ApiResponse<T> {
  success: boolean;
  data: T;
}

interface User {
  id: number;
  name: string;
}

const userResponse: ApiResponse<User> = {
  success: true,
  data: { id: 1, name: "Alice" },
};

const listResponse: ApiResponse<string[]> = {
  success: true,
  data: ["one", "two"],
};
```

The same `ApiResponse` shape now wraps any payload, and `userResponse.data.name` is fully typed. The same idea works with classes:

```typescript
class Stack<T> {
  private items: T[] = [];

  push(item: T): void {
    this.items.push(item);
  }

  pop(): T | undefined {
    return this.items.pop();
  }
}

const numberStack = new Stack<number>();
numberStack.push(5);
```

Pushing a string into `numberStack` is a compile time error. One class definition, infinite typed variations.

## Constraints: Controlling What `T` Can Be

Sometimes a generic function needs to assume something about the type. Constraints with `extends` let us require a minimum shape:

```typescript
function getProperty<T, K extends keyof T>(obj: T, key: K): T[K] {
  return obj[key];
}

const user = { id: 1, name: "John Doe", age: 21 };
const name = getProperty(user, "name");
getProperty(user, "email");
```

The constraint `K extends keyof T` guarantees the key actually exists on the object. The last line fails to compile because `"email"` is not a property of `user`. The function stays generic, but it is impossible to misuse.

## Conclusion

Generics remove the false choice between writing duplicated type-specific code and falling back to `any`. A type parameter lets one function, interface, or class serve every data structure we throw at it, while TypeScript keeps tracking the exact types from input to output. Combined with constraints, generics give us components that are both flexible and impossible to call incorrectly, which is exactly what reusable code should look like in a strictly typed language.
