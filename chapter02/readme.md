## Points

- `any` is a special TypeScript type– it does not exist in JavaScript. It accepts any value of any
  type, and is thus a top type, encompassing all other types.
- TypeScript sets `any` as the default type for any value or parameter that is not explicitly typed or can’t be inferred.
- The browser’s JavaScript runtime can’t run TypeScript

> Note that after the compile step you lose all type safety when you run your code: it’s plain JavaScript once it hits browsers.

- TypeScript not only erases all types but also takes modern-day JavaScript features and transpiles them to older ECMAScript versions.
- TypeScript that generates JavaScript is called emitting

### Type Annotations

Declaring type annotations for variables and functions:

```typescript
function addVAT(price: number, vat: number = 0.2): number {
  return price * (1 + vat);
}
```

#

### noEmit flag

```bash
tsc --noEmit
```

- If this flag isn’t set (the default) we always get JavaScript – even when type checks fail.
- If we want to make sure we don’t get any emitted JavaScript output, set noEmitOnError to true in tsconfig.json.

#

### any

any is the default type if we neither specify a type nor let TypeScript infer one.

```typescript
let deliveryAddress; // deliveryAddress is any
```

Explicitly specify the type through an annotation

```typescript
// deliveryAddress is of type any
let deliveryAddress: any = "421 Smashing Hill, 90210";
```

> any can be anything: it’s a top type and, therefore, all other types are part of any .

- Since any can be anything, any also allows us to access properties that might not be there.

> any is a wildcard!

#### Why does something like any even exist?

- due to the nature of JavaScript.

- In JavaScript, you are not bound to a type, and values from any type can appear in your variables and properties.

- any reflects JavaScript’s overarching flexibility

```typescript
// theObject is an object we don’t have a type for,
// but we know exactly what
// we are doing!
(theObject as any).firstLetter.toUppercase();
```

If you want to make sure you don’t have any somewhere in your code you don’t expect it to be, set the compiler flag `noImplicitAny` to `true`.

TypeScript will then make sure that you either assign values to correctly infer types or, in the case of `any`, make sure that you explicitly annotate or cast to `any`.

#

### Typing

- The moment we annotate using the colon syntax, the name is of the type we annotate. We call this technique left-hand typing, as the typing happens before (to the left of) the equals sign.

- Leaving out type annotations and working first with type inference is called right-hand typing.

```typescript
let deliveryAddresses = [
  "421 Smashing Hill, 90210",
  "221b Paw-ker Street",
  "4347 Whiskers-ia Lane",
];

// Type of deliveryAddresses is string[]
let deliveryAddresses: string[] = [];

// OK
deliveryAddresses.push("421 Smashing Hill, 90210");
// Not OK! 2 is not a string
deliveryAddresses.push(2000);
```

- With left-hand typing, you can think a lot about your types before you start coding the rest.

- Right-hand typing allows you to make up types as you go, which might be a little bit more JavaScript-y.

#

#### Type Guards

Perform run-time checks on types, just like the typeof operator makes sure we’re dealing with a number at this point.

#### Control flow analysis

Type guards are used to trigger control flow analysis in TypeScript.

TypeScript can analyze the flow of your program to provide the right types for the next steps.

#

- any is the supertype of all types.

- string is a subtype of any.

- Every value of string can be assigned to its supertype any , but not every value of any can be assigned to its subtype string .

- The same concept applies to classes and objects. HTML Element is the supertype of all HTML elements in the DOM. HTMLAnchorElement is a subtype of HTMLElement. Every HTMLAnchorElement can be assigned to type HTML Element, but not every value of HTMLElement can be assigned to an HTMLAnchorElement .

#

### Unknown

- unknown is compatible with every type in TypeScript.

- It's also a top type.

- any is both carefree and careless, putting type safety into the developer’s hands; unknown requires much more caution and concern.

#

### Composite Types

- virtually any combination of property names and other types, both primitive types and additional composite types.

- Objects are composite types.

```typescript
const book = {
  title: "Form Design Patterns by Adam Silver",
  price: 32.77,
  vat: 0.19,
  stock: 1000,
  description: "A practical book on accessibility and forms",
};

// To define a type for this object, we can use the type alias syntax:
type Article = {
  title: string;
  price: number;
  vat: number;
  stock: number;
  description: string;
};
```

Annotating the type of book:

```typescript
const book: Article = {
  title: "Form Design Patterns by Adam Silver",
  price: 32.77,
  vat: 0.19,
  stock: 1000,
  description: "A practical book on accessibility and forms",
};
```

#

### Structural Typing and Excess Property Checks

- If we assign a value with properties not in the specified type, TypeScript displays an error

```typescript
// Property 'rating' is not allowed
const movie: Article = {
  title: "Helvetica",
  price: 6.66,
  vat: 0.19,
  stock: 1000,
  description: "90 minutes of gushing about Helvetica",
  rating: 5,
};
```

However, this isn’t the case when we define the value elsewhere:

```typescript
// Property 'rating' is not allowed
const movBackup = {
  title: "Helvetica",
  price: 6.66,
  vat: 0.19,
  stock: 1000,
  description: "90 minutes of gushing about Helvetica",
  rating: 5,
};

const movie: Article = movBackup; // Totally OK!
```

_**Why?**_

TypeScript is a structural type system.

This means that as long as the defined properties of a type are available in an object, the structural contract is fulfilled.

When assigning movBackup to movie of type Article , all relevant properties match: title, price, vat, stock, and description . The extraneous – or excess – rating property is swept under the rug. Literally!

If we look at the autocompletion features that VS Code gives us as soon as we assigned movBackup to movie , we see that rating is not available anymore.

This doesn’t mean that these properties aren’t there at runtime. They are! But during development, our tooling envi-
ronment will make sure we only use the properties that are defined by the type.

We could get values from anywhere, and those values could change over time, but our contract still only cares about the right types of a certain set of properties. This makes our application still valid and typesafe but allows us to be flexible in other parts of our app.

This is also true if we have two different types with a similar enough structure to fulfill the contract:

```typescript
type ShopItem = {
  title: string;
  price: number;
  vat: number;
  stock: number;
  description: string;
  rating: number;
};
const shopitem = {
  title: "Helvetica",
  price: 6.66,
  vat: 0.19,
  stock: 1000,
  description: "90 minutes of gushing about Helvetica",
  rating: 5,
};

const movie: Article = shopitem; // Totally OK!
```

_**But why does a direct value assignment after a type annotation cause an error?**_

This feature is called an excess property check.

Because TypeScript is kind to us as structures could change across our application, it will point us to things that might be deliberate mistakes.

Assigning a value right after a type annotation that doesn’t completely match is most likely an unintentional error.

```typescript
const missingProperties = {
  title: "Helvetica",
  price: 6.66,
};

// Boom! This breaks
const anotherMovie: Article = missingProperties;
```

The structural contract is not fulfilled.
