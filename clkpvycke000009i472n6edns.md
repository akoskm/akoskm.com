---
title: "A Full-stack Developer's Guide to Setting Up a New MacBook Pro"
seoTitle: "Full-stack Developer: New MacBook Pro Setup"
seoDescription: "Explore a full stack developer's guide for setting up MacBook Pro with the most essential tools"
datePublished: Sun Jul 30 2023 20:20:17 GMT+0000 (Coordinated Universal Time)
cuid: clkpvycke000009i472n6edns
slug: a-full-stack-developers-guide-to-setting-up-a-new-macbook-pro
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1690748351443/497717b0-5f3f-4f6d-8ff1-aedf33345c39.png
tags: web-development, developer, beginners, developer-tools, full-stack-development

---

I bought my first MacBook Pro 13" in 2015, only a few months after I started freelancing full-time. Before that, I mainly used Linux, but thanks to an unfortunate kernel upgrade, I couldn't work for days for my client. I didn't lose the client, but I lost a couple hundred dollars. Macs weren't cheap back then either, but the math was easy: one more downtime like this with my Linux machine, and I lose the price of a MacBook Pro 13" 2014 model.

At that time, I was the only one with a stable (as stable as freelancing gets) job at the table, so I knew I could never let this happen again.

Setting up a new device, whether a phone, a new laptop, or even a watch these days, is always work but also fun - to me, at least. 😅 So here's how I'm doing it:

# Migrate or start fresh

I tried the "Migration Assistant" several times and moved my old setup, but I always regretted it later because of the junk it transferred over.

Due to this, I prefer to start fresh. The last MacBook I owned, a 2018 model with the dreaded touch bar, had been involved in so many projects and had seen so many apps that, honestly, I wasn't even sure if I used half of the packages I had installed through Homebrew.

I also don't work with files that are only on the machine. This might sound strange, but I have nothing that wouldn't have a copy elsewhere.

Projects are always in sync with GitHub, company documents to go an external drive and into the cloud, and photos - I don't have pictures on my machine since I started using smartphones - everything is synced up with either Google Photos or iCloud.

This significantly streamlines the process when I transition to a new device. I am confident that I can wipe the contents of my old laptop, install the few applications I use, and simply link my iCloud account on the new machine, and I'm ready to go.

# New SSH key for the current machine

Because I'm erasing everything from my old Mac and using ssh keys to connect to GitHub and other services such as DigitalOcean, I like to start by generating a new SSH key and uploading it to these services.

[https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent](https://docs.github.com/en/authentication/connecting-to-github-with-ssh/generating-a-new-ssh-key-and-adding-it-to-the-ssh-agent)

Here's the official documentation on how to do it. Here's a spoiler: I'm using VSCode. When you're done with the above process of generating an SSH key and starting up VSCode to pull some projects, you'll notice this dialog popping up:

![sorry for the light theme 😃](https://cdn.hashnode.com/res/hashnode/image/upload/v1690012397548/0b74d89b-8c1d-4dd1-b3df-af68e3de5d0d.png align="center")

To avoid this, simply run `ssh-add` in your terminal with the path to the key you just generated (not the `.pub` file, but the one without the extension):

```plaintext
 ssh-add ~/.ssh/id_<some random string>
```

This works until the next restart. Because I never shut down my Mac, I have to do this occasionally, so it's not a big issue. There are probably ways to add the keys permanently - if you know any, please let me know in the comments.

# Apps I use

## Brave

I used Firefox for a long, long time because I couldn't find the level of privacy in any other browser. But it had a cost: some of the codebases I work on are so huge that Firefox Debugger simply can't handle them. The DevTools freezes, and I must close the tab or restart the browser.

Nearly a year ago, I began working on browser plugin development and wanted my plugins to be compatible with Chrome. However, I was never fond of their approach to privacy, so I sought alternatives. That's when I noticed Kent C. Dodds using Brave in his screencasts. I decided to give it a try and was thoroughly impressed. Once you remove the features from Brave that you likely don't need, such as the VPN, Crypto Wallet, and Tips feature, it essentially becomes Firefox with the capabilities of Chrome.

### Brave Plugins

* Bitwarden - this is my default password manager. It's free for personal use, and I know friends from big companies where this is the standard [password manager](https://cybernews.com/best-password-managers/). It's pretty good and integrates well with all your other devices.
    
* Grammarly - because I can't write without it 😅.
    
* Dark Reader - this plugin generates a dark theme for any website. Very useful for websites that don't have a dark theme.
    

### Cons of Brave

Sometimes, the aggressive privacy-first features cause websites to break, making tasks like flight check-ins and accessing government or insurance websites quite painful and sometimes even impossible. This is when I occasionally switch to Safari. In the future, I envision using Safari as my primary browser for both personal and work-related tasks, but not just yet - more on this later.

## Command Line Tools

I just run `xcode-select --install` which installs `git` and some other tools like `gcc` and `make`.

## Homebrew

The first thing I used Brave for was to look up how to install the package manager [Homebrew](https://brew.sh/) - I recalled the last time I installed it, I had to fetch and execute a remote script. Totally safe! 😃

## Node

I never install `node` directly on my machine. Our company works on different projects simultaneously, often with different Node.js versions. To be able to switch between versions easily, I'm using `nvm`, the Node Version Manager. I recommend you do the same. Here's how to install it: [https://github.com/nvm-sh/nvm#installing-and-updating](https://github.com/nvm-sh/nvm#installing-and-updating)

## VSCode

I couldn't run IntelliJ IDEA on the previous project I worked on (it's an 8GB Git repo 😬), so I was kind of stuck with VSCode.

IntelliJ IDEA is a much more professional IDE in many respects, but since I couldn't run it, I gradually discovered alternatives to my IntelliJ workflows in VSCode. As a result, I'll be sticking with it for a while.

All my plugins are synced through my GitHub account, so after installing VSCode on the new Mac, I simply log in, and everything magically appears:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1690006933319/c5344352-14a2-41ab-9436-7cb602fb51bd.png align="center")

You can configure what to sync by hitting ⌘ + ⬆ + P and typing **sync configure**, then from the following checkboxes, tick what you want to sync:

![](https://cdn.hashnode.com/res/hashnode/image/upload/v1690007225160/32fed1fb-9d51-4d67-bcf8-5197c9566e09.png align="center")

## Figma

I'm using Figma more and more recently for quite different tasks:

* creating visuals for my blog posts and social media
    
* wireframes for clients
    
* FigJam for brainstorming and mind maps
    

It's a great app, but it just got acquired by Adobe, so maybe I'll have to look for alternatives.

## Docker Desktop

Every full-stack developer must have this companion app. If you ever need to run five different versions of PostgreSQL for five distinct client projects, it's just so much easier to accomplish with Docker.

Of course, you can run Docker without its desktop app. Still, it gives you an excellent overview of running containers, images, and volumes and faster access to the logs of containers than the traditional command line tools.

## Discord/Slack

My go-to app for client and company communication. Clients mostly use Slack, which I'm not a big fan of, but internally we use Discord, that's much better.

I have the Badge and Sound notification turned off for all of them, so I'm sure they never interrupt my workflow.

# Apps I'm *not* using

Sometimes I feel I'm doing something wrong. Especially when you ask others what's the first app you would install, and 100 people recommend an app you never even heard about 😃

%[https://twitter.com/akoskm/status/1682197640895778816] 

## Spotlight alternatives

Spotlight is the thing you open with ⌘ + Space, and you can use it to search your files, app, or whatever. To my surprise, there are quite a few alternatives for the built-in Spotlight search, like Alfred or Raycast.

These were brought up quite a few times in the replies. But the thing is, I'm not using Spotlight that much. I never used it to find files on my machine, and I rarely used it to open apps.

Maybe because I never turn off my Mac. I simply close the lid or let it go to sleep. Brave, Visual Studio Code, Spotify, Slack, and Discord are always open and accessible when I do the three-finger swipe-up gesture on the touch bar.

The files I open are project files, but I'm accessing those from VSCode. I access company documents and PDFs from the Files app, but this is an infrequent use case.

## Chrome

As I pointed out earlier, I never was a big fan of Chrome.

I'd like to use Safari because at some point because how it integrates well with iPhone and other parts of the system (they have the only cross-device reading list that *actually* works), but right now, it doesn't offer much for web developers.

# Conclusion

This is my 8-minute guide for setting up a new MacBook Pro as a full-stack developer. I prefer to keep things simple and efficient. Install only the essential apps and start working as soon as possible.

This is often Brave, Homebrew, Node, VSCode, Figma, Docker Desktop, and communication apps like Discord and Slack.

There are a bunch of popular and cool apps I don't use because I haven't found a place for them yet in my workflow.

Enjoy your new machine!

\- Akos