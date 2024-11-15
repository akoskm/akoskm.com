---
title: "How to Fix React Hydration Error in Astro"
seoTitle: "Fixing React Hydration Error in Astro"
seoDescription: "Learn to fix React Hydration Errors in Astro by using the client:only directive"
datePublished: Wed Sep 25 2024 05:27:28 GMT+0000 (Coordinated Universal Time)
cuid: cm1hfbij3004909ihapj10zjg
slug: how-to-fix-react-hydration-error-in-astro
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1727241941781/f2aef0ed-cf2a-40ca-a2d1-6bacbb4e20d5.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1727241955678/4106ba47-8a80-4d96-ae63-aa13f606a1b0.png
tags: javascript, web-development, reactjs, static-website, astro

---

Hi friends 👋

I’ve launched a custom Astro front-end for my Hashnode blog. I wanted to have **static pages** that work **without JavaScript**, while still having access to Hashnode’s best-in-class Neptune editor ❤️. Astro generates static pages by rendering your components on the server side. This is a big plus but it introduces the possibility of **React Hydration Errors**.

A React Hydration Error occurs when the HTML generated by React on the server is different from the HTML generated by React on the client.

If you prefer to watch the video version where I also walk you through **how to fix React Hydration Errors** in Astro, here’s a video I made for you!

%[https://youtu.be/WaVIeduz8Vg] 

So while static pages worked for 99% of my site, there was a problem…

I wanted a *Countdown* component that shows you the current promotion I’m running on my React Custom Hooks Course. This component had to be dynamic, counting down the *days, hours, minutes, and seconds* you have left until the promotion ends.

The new time is set in the `useEffect` below:

```typescript
// Countdown.tsx
const Countdown = () => {
  const [time, setTime] = useState(() => formatTime(timeLeft));

  useEffect(() => {
    const interval = setInterval(() => {
      timeLeft = PROMOTION_END - Date.now();
      setTime(formatTime(timeLeft));
    }, 1000);
    return () => clearInterval(interval);
  }, []);

  return <span id="countdown">Time left: {time}</span>;
};
```

To get the time changing I had to make this component client-side rendered with Astro, using the `client:load` directive:

```typescript
// header.astro
<Countdown client:load />
```

Because I made Countdown client-side render, but it was already server-side rendering, I’ve run into the famous **React Hydration Error**.

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727158310509/8cf5225a-2297-4bd3-8d47-68799f344f6b.png align="center")

When the Countdown component was rendered on the server side, it had one date. However, when it was rendered on the client side a few seconds later, it had a different date.

This is the perfect recipe for a **React Hydration Error**:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1727158392420/309b49c0-d1de-4d9c-b368-47380915ecf9.png align="center")

Let’s see two ways to fix this hydration error for our dynamic React component in Astro.

# Return Null on Server Side rendering

We know that `useEffect` doesn’t run while the React component is rendered on the server side. We can use this knowledge to track when the component is client-side rendered by having a boolean state that changes to false when `useEffect` runs during mount:

```typescript
const Countdown = () => {
  const [time, setTime] = useState(() => formatTime(timeLeft));
  // true, because we're on the server by default
  const [isLoading, setIsLoading] = useState(true);

  useEffect(() => {
    setIsLoading(false); // Okay, now we're on the client
    const interval = setInterval(() => {
      timeLeft = PROMOTION_END - Date.now();
      setTime(formatTime(timeLeft));
    }, 1000);
    return () => clearInterval(interval);
  }, []);

  if (isLoading) return null; // while on the server return null

  return <span id="countdown">Time left: {time}</span>;
};
```

This solution fixes the hydration error, but it makes our component a bit more complicated with an extra state, due to how Astro renders our component.

Not the best solution… Let’s see if Astro can fix the problem!

# Use client:only=”react”

Astro gives you [*Directives*](https://docs.astro.build/en/reference/directives-reference/#client-directives) *that* control how Astro hydrates the components it renders.

One of these directives is `client:only={string}`.

When this directive is applied, Astro skips the server rendering of a component and renders it only on the client. It works like `client:load` by loading, rendering, and hydrating the component right away when the page loads.

Applying this directive in my case looked like this:

```typescript
// header.astro
<Countdown client:only="react" />
```

This also made the hydration error disappear, without changing the complexity of the React component itself!

Win-win!

# Conclusion

In conclusion, dealing with React Hydration Errors in Astro can be challenging, but with the right approach, it is manageable. By **understanding the root cause** of these errors and utilizing Astro's directives effectively, you can ensure your dynamic components work seamlessly.

Whether you choose to return null on server-side rendering or use the `client:only="react"` directive, both methods offer viable solutions to maintain the integrity of your static and dynamic content.

Happy coding!