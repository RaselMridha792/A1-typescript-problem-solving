# Why `any` is a Type Safety Hole and `unknown` is the Safer Choice

## Introduction

TypeScript exists for one big reason: catching mistakes before the code runs. But the language also gives us an escape hatch called `any`, and using it too freely can quietly remove all the protection TypeScript provides. In this post I want to explain why `any` is often called a "type safety hole", why `unknown` is a better option when we deal with unpredictable data, and how type narrowing helps us work with `unknown` safely.

## The Problem with `any`

When a variable is typed as `any`, TypeScript stops checking it completely. We can call any method on it, access any property, and assign it to anything else. The compiler will not complain, even when the code is clearly wrong.

```typescript
let data: any = "hello";

data.toFixed(2);
data();
const num: number = data;
```

Every line above compiles without a single error, but each one will crash or behave wrongly at runtime. This is why `any` is called a type safety hole: the moment a value becomes `any`, the type system can no longer protect us, and the bugs travel silently into runtime.

This becomes dangerous with unpredictable data, like a response from an API or input parsed from a JSON file. We do not actually know the shape of that data, and typing it as `any` simply pretends the problem does not exist.

## Why `unknown` is Safer

`unknown` is also a type that can hold any value, but with the opposite attitude. Instead of trusting the value blindly, TypeScript refuses to let us do anything with it until we prove what it is.

```typescript
let data: unknown = JSON.parse(someInput);

data.toUpperCase();
```

Here the compiler gives an error: `'data' is of type 'unknown'`. That error is a good thing. It forces us to check the value first, which is exactly what we should do with data that comes from outside our program.

## Type Narrowing

The way we "prove" the type to TypeScript is called type narrowing. We write a runtime check, and inside that check TypeScript narrows the type from `unknown` down to something specific.

The most common tool is the `typeof` guard:

```typescript
function formatValue(value: unknown): string {
  if (typeof value === "string") {
    return value.toUpperCase();
  }
  if (typeof value === "number") {
    return value.toFixed(2);
  }
  return "Unsupported value";
}
```

Inside the first `if` block, TypeScript knows `value` is a `string`, so calling `toUpperCase` is allowed. Inside the second block it is a `number`. Outside both blocks it stays `unknown` and remains locked.

For class instances we can narrow with `instanceof`:

```typescript
function getMessage(error: unknown): string {
  if (error instanceof Error) {
    return error.message;
  }
  return "Something went wrong";
}
```

And for object shapes we can use the `in` operator or write a custom type guard function:

```typescript
interface User {
  name: string;
}

function isUser(value: unknown): value is User {
  return (
    typeof value === "object" &&
    value !== null &&
    "name" in value
  );
}
```

The `value is User` return type tells TypeScript that whenever `isUser` returns `true`, the value can be treated as a `User` from that point on.

## Conclusion

`any` turns off the type checker and lets unsafe operations pass silently, which defeats the purpose of using TypeScript in the first place. `unknown` accepts the same range of values but keeps them locked until we verify them through type narrowing with `typeof`, `instanceof`, `in`, or custom type guards. For any data we cannot predict, like API responses or parsed JSON, `unknown` pushes the validation to compile time thinking instead of runtime surprises, and that is exactly the safety TypeScript was made for.
