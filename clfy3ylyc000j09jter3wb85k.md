---
title: "How to solve ”variable is possibly undefined” in TypeScript - even when it’s defined"
seoTitle: "Fix "variable possibly undefined" - even if defined in TypeScript"
seoDescription: "Learn how to solve the "variable is possibly undefined" error in TypeScript and avoid nesting with ternary operators in React"
datePublished: Sat Apr 01 2023 15:08:04 GMT+0000 (Coordinated Universal Time)
cuid: clfy3ylyc000j09jter3wb85k
slug: how-to-solve-variable-is-possibly-undefined-in-typescript-even-when-its-defined
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1680360220310/2fd2579e-dec3-49ad-adab-6d11b9c8f453.png
tags: web-development, reactjs, typescript, beginners, code-quality

---

Readable code has always been one of the highest importance in my teams.

But it’s not reduced to ”correctly” naming variables.

We care about patterns, nesting, and interaction between different parts of the system - everything that makes navigating the codebase easier.

Nesting is one of those things that I always approach with:

*Ughhh, let’s go a level deeper*

It’s one of the overused things in React, and it usually appears as a byproduct of using the ternary operator.

It is not uncommon that we find components like this:

```typescript
interface Player {
  id: number;
  name: string;
}

interface BoardProps {
  players?: Array<Player>;
}

export default function Board({ players }: BoardProps) {
  return (
    <p>
      {players && players.length > 0
        ? players.length > 1
          ? `Welcome our new players: ${players
              .map(({ name }) => name)
              .join(", ")}!`
          : `Welcome our new player: ${players[0].name}!`
        : "No Players"}
    </p>
  );
}
```

This code works perfectly fine yet makes you question many things. And it's also super ugly...

The ternary operator inevitably leads to nesting, and more ternary operators lead to more nesting.

We decide to refactor the above code and create separate functions to avoid using the ternary operators:

```typescript
function renderWelcome() {
  if (players.length > 1) {
    const names = players.map(({ name }) => name).join(", ");
    return `Welcome our new players ${names}!`;
  }
  return `Welcome our new player: ${players[0].name}!`;
}
```

But there's one problem with this:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1680359362863/b176909b-1cba-4a4a-bbb2-f01d12aebbea.png align="center")

Even though we check that `players` is defined before entering `renderWelcome` TypeScript still warns us about a possibly undefined value for `players`.

The behavior makes sense to me because nothing prevents `renderWelcome` from being called without wrapping it with a check - leading to a runtime error in your code.

There are a couple of ways to fix this:

1. move the `if (players)` check inside `renderWelcome`
    
2. create a `RenderWelcome` component
    

And there's a 3rd way to fix it, by adding `players!` , but let’s not talk about lazy TypeScript. 😄

I'll go with option 2) because, to me, it looks the best in React.

All you have to do is create a component where `players` is not optional (note the missing `?`):

```typescript
function PlayerWelcome({ players }: PlayerWelcomeProps) {
  if (players.length > 1) {
    const names = players.map(({ name }) => name).join(", ");
    return <p>Welcome our new players {names}!</p>;
  }
  return <p>Welcome our new player: {players[0].name}!</p>;
}
```

TypeScript will be happy with this code because `players` is guaranteed to be there:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1680359574461/4f948c42-a8d5-41a1-8e74-5a4e87e2346e.png align="center")

Congratulations!

You just learned how you could both improve the readability of your code and create a nice component separation in React when using TypeScript without dirty tricks such as the `!`.

Thanks for reading my article!

If you liked it, share it with someone who would find it helpful and give the article a ❤️!

Until next time,

Akos