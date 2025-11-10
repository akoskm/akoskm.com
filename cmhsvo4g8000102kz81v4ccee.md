---
title: "Next.js 16 is Here – Turbopack is Stable and the Default Bundler"
seoTitle: "Next.js 16: Turbopack stable, Cache Components, and why you should upg"
seoDescription: "Turbopack is now stable in Next.js 16, bringing 2–5× faster builds. Here's what changed, how to migrate, and my quick test on a real project."
datePublished: Mon Nov 10 2025 08:25:56 GMT+0000 (Coordinated Universal Time)
cuid: cmhsvo4g8000102kz81v4ccee
slug: nextjs-16-turbopack-stable
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1762762030526/f53a61e2-a1be-4a6c-bc8e-fd5c512236cd.png
tags: performance, web-development, reactjs, nextjs, turbopack

---

I still remember the day I tried Turbopack for the first time in a Next.js 13 app.

It was experimental, I had to flip a flag, and honestly?

It crashed more than it compiled.

Fast-forward to today – Next.js 16 just dropped, and Turbopack is **stable**, **default**, and already powering over 50% of dev sessions on recent versions. 

No more `--turbo` or `experimental` flags. Just `next dev` and you're on Turbopack.

Before we dive in, you can support me by subscribing to my newsletter. It's all about sustainable growth, writing, and the occasional Next.js rant.

%%[substack] 

## Turbopack: From Beta to Default

Turbopack was announced back in 2022 as Vercel's Rust-powered answer to Webpack's slowness. It promised:

- **700× faster incremental builds** (in theory)
- **10× faster Fast Refresh**
- **Zero-config** experience

Now in Next.js 16, it's **stable for both dev and production**, and it's the **default bundler** for all new projects.

> If you have a custom Webpack config, you can still opt out with `next dev --webpack` or `next build --webpack`.

### Real Numbers from My Test

I took a mid-sized Next.js app (App Router, 40+ pages, Tailwind, tRPC, Prisma) and compared:

| Bundler     | Cold Start (dev) | HMR (Fast Refresh) | Production Build |
|-------------|------------------|--------------------|------------------|
| Webpack     | 4.2s             | ~800ms             | 42s              |
| **Turbopack** | **1.1s**         | **~90ms**          | **18s**          |

That's **3.8× faster cold start** and **2.3× faster production build**. HMR felt *instant*.

## How to Upgrade to Next.js 16

The Vercel team made this surprisingly smooth.

### 1. Use the Automated CLI (Recommended)

```bash
npx @next/codemod@canary upgrade latest
```

This handles:
- Removing deprecated `experimental.ppr`
- Renaming `middleware.ts` → `proxy.ts`
- Updating cache APIs
- Fixing parallel routes `default.js` requirements

Docs for `proxy.ts`: [https://nextjs.org/docs/app/api-reference/file-conventions/proxy](https://nextjs.org/docs/app/api-reference/file-conventions/proxy)

### 2. Manual Upgrade

```bash
npm install next@latest react@latest react-dom@latest
```

Or start fresh:

```bash
npx create-next-app@latest
```

> The new `create-next-app` now defaults to:
> - App Router
> - TypeScript
> - Tailwind CSS
> - ESLint
> - Turbopack

## Key New Features in Next.js 16

### Cache Components (The Big One)

This is the evolution of Partial Prerendering (PPR). Enable it in `next.config.ts`:

```ts
// next.config.ts
const nextConfig = {
  cacheComponents: true,
};
 
export default nextConfig;
```

Now you can cache **pages**, **components**, or **functions** with `"use cache"`:

```tsx
'use cache'
export async function getUserPosts(userId: string) {
  return db.posts.findMany({ where: { userId } });
}
```

- Explicit caching (opt-in)
- Automatic cache keys
- Works with PPR for instant navigation

> `experimental.ppr` is **gone**. Use `cacheComponents` instead.

### `proxy.ts` Replaces `middleware.ts`

```ts
// proxy.ts
export default function proxy(request: NextRequest) {
  return NextResponse.redirect(new URL('/home', request.url));
}
```

- Runs on Node.js (predictable)
- `middleware.ts` still works but **deprecated**

Docs: [https://nextjs.org/docs/app/api-reference/file-conventions/proxy](https://nextjs.org/docs/app/api-reference/file-conventions/proxy)

### Better Logging

Dev logs now show **where time is spent**:

```
✓ Compiled in 615ms
✓ TypeScript in 1114ms
✓ Page data in 208ms
✓ Static pages in 239ms
```

Build output is similarly detailed.

### React Compiler (Stable)

```ts
// next.config.ts
const nextConfig = {
  reactCompiler: true,
};
 
export default nextConfig;
```

Zero-config memoization. Install the plugin:

```bash
npm install babel-plugin-react-compiler@latest
```

> Note: Increases compile time. Test in CI first.

## Breaking Changes You’ll Hit

| Change | Fix |
|-------|-----|
| `revalidateTag(tag)` | → `revalidateTag(tag, 'max')` |
| `middleware.ts` | → rename to `proxy.ts` |
| Parallel routes without `default.js` | Add `default.js` with `notFound()` or `null` |
| Node.js 18 | Upgrade to **20.9+** |

Run the codemod – it catches 90% of these.

## Should You Upgrade?

**Yes**, if:
- You're starting a new project
- You want faster builds
- You're okay with Node.js 20+

**Wait**, if:
- You're on a tight deadline
- You have complex Webpack plugins
- You're still on Node.js 18

## My Migration Checklist

1. Run the codemod
2. Test in dev with Turbopack
3. Check production build size (`next build --webpack` vs default)
4. Verify caching behavior
5. Update CI to Node.js 20+

## Conclusion

Next.js 16 isn't revolutionary – it's **evolutionary**. Turbopack stable, better caching, cleaner APIs. It's the kind of release that makes you go *"finally"* instead of *"whoa"*.

I’ve already upgraded two client projects. One saw a **40% drop in build times** on Vercel. The other? HMR so fast the designer thought the app was broken.

Try it. Run `npx create-next-app@latest` and feel the difference.

If you hit any snags, drop a comment or find me on [𝕏 @akoskm](https://twitter.com/akoskm).

If you liked this, give it some emojis, share it with a colleague, so more people see it. 🙇‍♂️

Happy building! 👋