Any and Unknown Types
Any Type

What is the “unknown” Type in TypeScript?
Introduction
Not so long ago, in version 3.0, TypeScript introduced a new unknown type to its toolkit, but what does this type do, and more specifically, when should we use it?

This article will look at this "new" type to better understand its goal.

We'll also consistently compare it to its sibling, the any type; if you are not familiar with it yet, check out this article, where we have discussed it in more detail.

What is the unknown type?
In Typescript, any value can be assigned to the unknown type, but without a type assertion, unknown can't be assigned to anything but itself and the any type. Similarly, no operations on a value with its type set as unknown are allowed without first asserting or restricting it to a more precise type.

We can assign any value to a variable of the unknown type the same way we can with a variable of the any type. Unlike in's case, we cannot access properties on variables of the unknown type nor call or construct them.

Additionally, unknown values can only be assigned to variables of the types any and unknown.

Assigning values to a variable of type unknown:

let val: unknown;

val = true; // Fine
val = 42; // Fine
val = "hey!"; // Fine
val = []; // Fine
val = {}; // Fine
val = Math.random; // Fine
val = null; // Fine
val = undefined; // Fine
val = () => { console.log('Hey again!'); }; // Fine
Assigning values of various types to a variable of type any:

let val: any;

val = true; // Fine
val = 42; // Fine
val = "hey!"; // Fine
val = []; // Fine
val = {}; // Fine
val = Math.random; // Fine
val = null; // Fine
val = undefined; // Fine
val = () => { console.log('Hey again!'); }; // Fine
Assigning a value of type unknown to variables of other types:

let val: unknown;

const val1: unknown = val; // Fine
const val2: any = val; // Fine
const val3: boolean = val; // Will throw error
const val4: number = val; // Will throw error
const val5: string = val; // Will throw error
const val6: Record<string, any> = val; // Will throw error
const val7: any[] = val; // Will throw error
const val8: (...args: any[]) => void = val; // Will throw error
Making the unknown type more specific
We can narrow down the possible outcomes of an unknown type value.

Let's take a look at the following example:

const isNumbersArray = (val: unknown): val is number[] => (
  Array.isArray(val) && val.every((element) => typeof element === 'number')
);
const unknownValue: unknown = [12, 2, 8, 17, 14];
if (isNumbersArray(unknownValue)) {
  const sum = unknownValue.reduce((accumulator, currentElement) => (accumulator + currentElement) , 0);
  console.log(sum);
}
Previously, the's type was unknown; however, after calling the isNumbersArray with the unknownValue as its argument, we've concluded that the type of the unknownValue is, in fact, that of an array of numbers.

When would we need to use the unknown type?
One particular scenario where we might want to use the unknown type is where we need to grab something from local storage.

All localStorage API's items are being serialized before storage. However, our use case includes the value of the retrieved item after deserialization.

By making use of the unknown type, we'll be able to type out the deserialized item's type rather than simply using the any annotation.

type ResultType =
  | { success: true; value: unknown }
  | { success: false; error: Error };
const retrieveItemFromLocalStorage = (key: string): ResultType => {
  const item = localStorage.getItem(key);
  if (!item) {
    // The item does not exist, thus return an error
    return {
      success: false,
      error: new Error(`Item with key "${key}" does not exist`),
    };
  }
  let value: unknown;
  try {
    value = JSON.parse(item);
  } catch (error) {
    // The item is not a valid JSON value, thus return an error
    return {
      success: false,
      error,
    };
  }
  // Everything's fine, thus return a successful result
  return {
    success: true,
    value,
  };
}
The ResultType is a tagged union type (also known as a discriminated union type). You might come across Maybe, Option, or Optional, which are its equivalent in other languages.

We use ResultType to model the successful or unsuccessful outcomes of the operation cleanly.

And here's an example of how we would use our retrieveItemFromLocalStorage function:

const result = retrieveItemFromLocalStorage("cached-blogs");

if (result.success) {
  // We've narrowed the `success` property to `true`,
  // so we can access the `value` property
  const cachedBlogs: unknown = result.value;

  if (isArrayOfBlogs(cachedBlogs)) {
    // We've narrowed the `unknown` type to `Array<Blog>`,
    // so we can safely use our cached `cachedBlogs` array as we would
    console.log('Cached blogs:', cachedBlogs);
  }
} else {
  // We've narrowed the `success` property to `false`,
  // so we can access the `error` property
  console.error(result.error);
}
Summary
I hope you've enjoyed the read and have gotten a bit more insight into what the unknown type is what makes it different from the any type, and when we are usually using it in real projects.

Feel free to leave your thoughts in the comments section below.

Cheers!

More content at PlainEnglish.io. Sign up for our free weekly newsletter. Follow us on Twitter, LinkedIn, YouTube, and Discord. Interested in Growth Hacking? Check out Circuit.

Typescript
Unknown Type
Web Development
Programming
Coding
7




7


Enjoy the read? Reward the writer.Beta
Your tip will go to Vlad Mihet through a third-party platform of their choice, letting them know you appreciate their story.

Give a tip
If you want to stay up to date with the newest and best Front-End trends, be sure subscribe to my newsletter! 😉
By signing up, you will create a Medium account if you don’t already have one. Review our Privacy Policy for more information about our privacy practices.


Subscribe
More from JavaScript in Plain English
Follow
New JavaScript and Web Development content every day. Follow to join our 3.5M+ monthly readers.

Somnath Singh
Somnath Singh




Member-only

Coding Won’t Exist In 5 Years. This Is Why
Those who won’t adapt would cease to exist. — Once upon a time, in a world not too different from ours, handmade clothing was the norm. Skilled artisans would spend countless hours creating beautiful garments, each one unique and crafted with care. But as technology advanced, machines were invented that could produce clothing at a much faster rate and…

Programming


Coding Won’t Exist In 5 Years. This Is Why
Share your ideas with millions of readers.

Write on Medium
fatfish
fatfish




Member-only

Say Goodbye to 7 Bad JavaScript Practices
I wish I had known about these bad habits earlier. — I’ve written a lot of shitty code in my few years of working life, and I’m experienced at it. My colleagues always think my code is stinking code, but I think they don’t appreciate my art… 1. Write code on one line as much as possible Oh my goodness! Would you like me to be your colleague? I cleared all…

Front End Development


Say Goodbye to 7 Bad JavaScript Practices
Pavel Pogosov
Pavel Pogosov



Stop Using useMemo Now!
Most of the Time It Slows Down Your Application — Over-optimization in React applications is an actual nightmare. Every day we have to face tons of useless optimizations, intended to make code more “mysterious”. Some developers even include useMemo and useCallback into their default style guide, just to simplify things. Don’t fall into that oblivion as useMemo can even slow…

Programming


Stop Using useMemo Now!
Pavel Pogosov
Pavel Pogosov



5 React useState Mistakes That Will Get You Fired
5 Common React useState mistakes and how to avoid them — While useStateis a simple tool to use, many developers still make mistakes with it. During code reviews, I often see these mistakes made even by experienced people. In this article, I’ll show you how to avoid them through simple and practical examples. Table of contents Getting previous value incorrectly Storing global state in…

Programming


5 React useState Mistakes That Will Get You Fired
Melih Yumak
Melih Yumak




Member-only

Node.js Developer Roadmap 2023
In this article, we will explore nodejs developer roadmap for 2023. A step-by-step guide to how to become nodejs developer or increase your knowledge. One of the most read articles of mine was the Nodejs developer roadmap in 2022. This article is a new version of the roadmap in 2023…

Nodejs




Never Type

The never type represents the type of values that never occur. For instance, never is the return type for a function expression or an arrow function expression that always throws an exception or one that never returns. Variables also acquire the type never when narrowed by any type guards that can never be true.

The never type is a subtype of, and assignable to, every type; however, no type is a subtype of, or assignable to, never (except never itself). Even any isn’t assignable to never