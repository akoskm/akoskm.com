---
title: "Go is Great!"
seoTitle: "Why you'll love Go as a JavaScript developer"
seoDescription: "Explore Go's software development simplicity: minimal syntax, robust tools, programming fundamentals. Improve skills through real-world coding challenges"
datePublished: Thu Feb 22 2024 14:42:11 GMT+0000 (Coordinated Universal Time)
cuid: clsxc0vrh00030ak0hslpcz99
slug: go-is-great
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1708612075158/e6b4c3f5-19b5-4c6c-8514-d10f9f7a721a.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1708612067405/aa044f0a-a942-4455-8905-933b68ab0030.png
tags: programming-blogs, go, web-development, beginners, programming-tips

---

As you grow as a software engineer, whether to satisfy your curiosity or meet market demands, you'll pick up a new language at some point.

In my career, one major language change happened.

I quit my full-time Java Full-stack Developer job a decade ago to start freelancing.

To meet the market demands and serve my future clients best, I ditched Java and went with JavaScript (later TypeScript). I have an incredible decade behind me and didn't regret this change for a second.

Also, I recently wrote about the differences between freelancing and full-time employment and why I'm considering returning to full-time again:

%[https://akoskm.substack.com/p/full-time-employment-or-freelancing?r=twgob] 

Go showed up on my radar a couple of years ago.

As I looked at different positions, I noticed a few listing it as a "nice to have".

Perfect timing, right? The results:

Go blew my mind.

For the record, I worked about 10 hours with it, but I feel I could already code something more complex than the [two little projects](https://github.com/akoskm?tab=repositories&q=&type=&language=go&sort=) I've built.

*Also, 10 hours isn't much time, so consider that when reading this. :)*

But is this time enough to learn a language sufficiently to get by?

For Go, I think it is.

Here's why.

## No Syntax Fatigue

While working on the simple [word-count](https://github.com/akoskm/ccwc) tool, I ran into the first problem after 5 minutes.

If you're not familiar with it, wc is a built-in Unix command that gives you information about a file and can be used like this:

```php
$ ccwc -w -l test.txt
   58164     7145 test.txt
```

So, how do I check if `-l` is present in the array of arguments that you supply to ccwc?

As a seasoned JS/TS developer, I thought there must be something like MDN for JavaScript, and it'll probably just pop up when I Google *"go array includes".*

To my surprise, everything I looked at told me to use a `for`.

*FYI, there's a slice package with* [*contains*](https://pkg.go.dev/slices)*, but this doesn't change the overall philosophy of Go: write it yourself, which isn't entirely a bad thing...*

For a second, I questioned if I had made the right choice by going with Go because there are 5 different ways to do the same in JavaScript, which has been around forever.

Should I have picked Rust instead? Is it too late?

![](https://media.giphy.com/media/v1.Y2lkPTc5MGI3NjExdmJ2YTlkaDJ0bmMycjUwZ2l6Z2J6Zm00MWM0MnM1c2EwdTZvcmI4ZiZlcD12MV9pbnRlcm5hbF9naWZfYnlfaWQmY3Q9Zw/CXAPW8vCQzYkg/giphy.gif align="center")

Gladly, I stopped for a second to think through.

JavaScript has been expanding the language for 30 years, and that's the reason why there are 15-minute-long blog posts on Array Syntax.

## Basic of Programming Revisited

> Want an indexOf? Write a for loop!
> 
> Want to see if an array includes an item, write a for loop!
> 
> Need a while loop? Write a for loop! I'm being serious.

Rich syntax solves some problems and creates others, but it always brings compatibility issues.

That's why we have tools such as [Babel.js](https://babeljs.io/) in the first place.

But Go has taken a more conservative approach and tried to look more like C.

The downside is no syntax for something common like `Array.includes`.

Besides having less stuff to learn, the upside is that you're returning to the roots of programming, where you were manually writing similar, small algorithms.

I don't know how this affects applications at scale, but I haven't written any in Go. 🤷‍♂️

## Developer Tooling

Babel.js is just one thing that's part of every modern web application.

But then you have eslint, all the eslint plugins for the flavor of your favorite client library, TypeScript, and all the eslint-typescript plugins.

The code has to look pretty, so you add prettier.

And we haven't done any coding yet!

These are the dependencies to check the validity and format of your code.

![](https://grafikart.fr/uploads/attachments/2022/node-modules-meme-63906eeae9581738123027.png align="left")

Go needs 0 dependencies for syntax and formatting to work.

Quite a change!

Excited already?

## How to Get Started?

You can do some easy Leetcode problems to get the hang of it.

The array manipulations don't require any external packages, so it's a great place to start. But if you're like me, you'll get bored quickly.

That's where @[John Crickett](@JohnCrickett) 's [Coding Challenges](https://codingchallenges.fyi/) come into play. It's a free collection of coding problems where you have to build real-world applications. I find it quite refreshing, and I think it gives you a better sense of how Go feels in production than Leetcode problems.

Good luck getting started!