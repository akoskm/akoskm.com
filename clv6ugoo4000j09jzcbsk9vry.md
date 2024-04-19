---
title: "How to test React apps with Vitest and Vite"
seoTitle: "Testing React Apps with Vitest & Vite"
seoDescription: "Learn how to set up and use Vitest for testing React apps with Vite, making your testing process simple and efficient"
datePublished: Fri Apr 19 2024 15:47:42 GMT+0000 (Coordinated Universal Time)
cuid: clv6ugoo4000j09jzcbsk9vry
slug: how-to-test-react-apps-with-vitest-and-vite
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1713541579358/a59be8eb-b92c-4d8b-aa81-e3a9bea884b2.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1713541588447/d0c05cd1-6a71-4ad4-8382-9e223a4ef0f3.png
tags: unit-testing, web-development, reactjs, testing, beginners, vite, testing-library, vitest

---

A few weeks ago, I began creating small applications for my upcoming course on Testing React applications. I aimed to make the setup simple and easy for students to follow.

I started with the Babel/Jest setup, which I've used for years, but people have asked why I'm not using Vitest already.

So I tried *Vitest, and it blew my mind.*

If I have to summarize in one sentence why Vitest is so good compared to Jest, it is that you don't need a PhD to configure all these things. You use the existing Vite configuration file and add a couple of lines to it, and that's it. Your tests are working.

In this blog post, I will walk you through setting up a new Vitest project inside a new Vite project. If you have an existing Vite project, that's fine because the steps will be pretty much the same. Just skip *Set up Vite* and jump straight to **Set up Vitest**.

If you're looking for the video version of this, you can watch it here:

%[https://youtu.be/nvkRBHXIi2M] 

Let's jump into it!

# Set up Vite

Let's get started by creating a new Vite project:

```plaintext
$ npm create vite@latest vitest-demo-1 --template react-ts
```

This command will generate a new Vite project with React and TypeScript set up. Now jump into the folder that the previous command created:

```plaintext
$ cd vitest-demo-1
```

Install the dependencies and finally start the scaffolded app:

```plaintext
$ npm i
$ npm run dev
```

The app should be running on http://localhost:5173, and you should be seeing something like this:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713535319914/840e468a-bfbf-4e2f-987d-6ed858898409.png align="center")

# Set up Vitest

Go to your project's root folder and install [Vitest](https://vitest.dev/) as a development dependency:

```plaintext
npm install -D vitest
```

To create a smoke test and verify that the test runner works, create a `sum.ts` file in your project's root:

```typescript
// sum.ts
export function sum(a: number, b: number) {
  return a + b
}
```

and also create a `sum.test.ts` file:

```typescript
// sum.test.ts
import { expect, test } from 'vitest'
import { sum } from './sum'

test('adds 1 + 2 to equal 3', () => {
  expect(sum(1, 2)).toBe(3)
})
```

Now open the `package.json` file:

and inside `scripts` add a new script `test:`

```typescript
{
  "name": "vitest-demo-1",
  ....
  "scripts": {
    ....
    "test": "vitest"
  }
}
```

If you set up everything correctly, you should be able to run:

```typescript
npm run test
```

in your terminal, and you should see something like this:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713511399083/43204427-cd1e-46d7-bc56-d453dd53bb44.png align="center")

# Set up testing-library/react

We'll use [`testing-library/react`](https://testing-library.com/docs/react-testing-library/intro/) to test the `App.tsx` component so let's install all the dependencies needed:

```bash
npm i -D @testing-library/jest-dom @testing-library/react @testing-library/user-event jsdom
```

To be able to do testing library assertions inside our test files, we have to `import '@testing-library/jest-dom'`. We'll do this in a `./src/test/setup.ts` file. Go ahead and create it:

```typescript
// ./src/test/setup.ts

import '@testing-library/jest-dom'
```

## Update tsconfig.json

Now open `tsconfig.json` and make the following changes:

```json
{
  "compilerOptions": {
    // existin compiler options, no change

    // this is the new configuration
    "types": ["vitest/globals"],
  },

  // also include the file we just created:
  "include": ["src", "./src/test/setup.ts"]
}
```

## Update vite.config.ts

As we mentioned in the beginning, the Vitest configuration will happen in the existing file, so open `vite.config.ts` and add the triple-slash imports at the top of the file:

```typescript
// vite.config.ts
/// <reference types="vitest" />
/// <reference types="vite/client" />
```

and also add the necessary Vitest configuration to `defineConfig`. Here's the final `vite.config.ts` for reference:

```typescript
/// <reference types="vitest" />
/// <reference types="vite/client" />

import react from '@vitejs/plugin-react'
import { defineConfig } from 'vite'

// https://vitejs.dev/config/
export default defineConfig({
  plugins: [react()],

  // <add this part>
  test: {
    globals: true,
    environment: 'jsdom',
    setupFiles: './src/test/setup.ts',
    css: true,
  },
})
```

# Add a unit test

Now we have everything ready to write and run React component tests with Vitest.

Create a file named `App.test.tsx` with the following contents:

```typescript
import App from './App'
import { render, screen } from '@testing-library/react'
import userEvent from '@testing-library/user-event'

describe('Simple working test', () => {
  it('the title is visible', () => {
    render(<App />)
    expect(screen.getByText(/Vite \+ React/i)).toBeInTheDocument()
  })

  it('should increment count on click', async () => {
    render(<App />)
    userEvent.click(screen.getByRole('button'))
    expect(await screen.findByText(/count is 1/i)).toBeInTheDocument()
  })
})
```

Use the `npm test` or `npm run test` command we used earlier to run all tests. If you followed the tutorial, this is what you should be seeing:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1713513194574/17b1426a-3ff9-479f-a557-d2241f5459ee.png align="center")

Congratulations!

You have Vitest handling your React unit tests! 👏

If you want to write more and better tests, check out my series on unit testing React components with `testing-library/react` here: [https://akoskm.com/series/react-testing](https://akoskm.com/series/react-testing)