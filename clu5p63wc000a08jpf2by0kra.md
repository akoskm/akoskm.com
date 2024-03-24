---
title: "What's New in React 19: Action Hooks"
datePublished: Sun Mar 24 2024 15:52:02 GMT+0000 (Coordinated Universal Time)
cuid: clu5p63wc000a08jpf2by0kra
slug: whats-new-in-react-19-action-hooks
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1711294490434/deaac456-295a-4713-a99b-a2ed79faf3b0.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1711294507292/bd12bae4-43cd-476c-96ef-5c347fdc8a79.png
tags: web-development, reactjs, typescript, beginners, react-19

---

One reason React remains the top framework is its ability to stay updated, address current challenges developers encounter, and act on them.

Let's delve into the core features that are stirring excitement among developers.

For each hook, I will explain what it does and give a real-world use case.

I also made a video about me trying out all these features and explained how you can do the same, so if you prefer that version, click here:

%[https://www.youtube.com/watch?v=TB6bAl3nOA8] 

All right!

Now, let's dive into the new hooks.

## **🔍 useFormState: Interactivity Redefined**

`useFormState` will be key for simplifying server interactions.

1. **Capabilities**: This hook manages form submission states and captures server responses.
    
2. **Practicality in Action**: Imagine a login process. `useFormState` can immediately display server responses, like a "Login Failed" message, directly enhancing user engagement and feedback. No need for the usual `useEffect`+`setMessage` combo.
    
3. **Implementation Insight**: In use, `useFormState` can handle server communication during form actions, capturing and presenting server responses easily.
    

## **🔄 useFormStatus: Keeping Users Informed**

`useFormStatus` focuses on enhancing the form submission experience.

1. **Function**: It provides a `pending` flag, toggling between true or false to indicate submission progress.
    
2. **User Experience**: This flag will be a huge help in displaying loading animations or changing button texts during data submission, keeping users engaged and informed.
    

## **🌈 useOptimistic: Proactive Feedback**

React 19 introduces the `useOptimistic` hook, which adds a layer of dynamic user feedback to web applications.

1. **Understanding** `useOptimistic`: This hook is designed for scenarios where you want to anticipate a successful outcome. It allows developers to update the UI optimistically, assuming the best-case scenario post an action like a form submission.
    
2. **Real-World Use Case**: Imagine a login form. With `useOptimistic`, you can immediately show a message like "Loading your dashboard..." after the user hits submit, even before the server responds. This optimistic feedback can enhance the user experience by making interactions feel faster and more responsive.
    
3. **Balancing Optimism with Reality**: Once the actual server response arrives, the hook transitions from the optimistic state to the real outcome. For instance, if the login fails, it will replace the "Loading your dashboard..." message with an appropriate error message.
    

## **🚀 Leveraging Canary Versions in React 19**

Want to test out React 19? Here's how you can find the latest canary version and use it in your app:

1. **Finding Canary Versions**: Visit [`npmjs.com`](http://npmjs.com), search for "react", and navigate to the versions tab. Look for versions tagged as "canary".
    
    ![](https://cdn.hashnode.com/res/hashnode/image/upload/v1711294003367/fc8a3076-2837-407f-987e-fca4f6c3e9f7.png align="center")
    
2. **Choosing the Right Version**: Avoid versions released on the same day due to potential issues. Opt for a version released at least a day or two ago.
    
3. **Synchronizing Versions**: Ensure that your `react-dom` version matches your React version. Copy the version numbers and update your `package.json` file in your development environment.
    
4. **Installation**: Run `npm install` and `npm run dev` to start experiencing the latest features of React 19.
    

If you'd like to give React 19 a shot *now*, you can also use this [Stackblitz](https://stackblitz.com/edit/vitejs-vite-urqenp?file=src%2Fmain.tsx) I created.

---

React 19 will be an amazing release!

How are you planning to integrate these hooks into your work? Share your thoughts and strategies below! 👇

For a deeper dive, check out the full video [here](https://youtu.be/TB6bAl3nOA8) and stay on top of the latest in React development. Happy coding, and stay inspired! 🎉💻🌐

**Join the Discussion: What's your take on React 19's new features, and how will you use them in your projects? Comment below!** 💬🤔🚀

Sources:

* React blog: [https://react.dev/blog/2024/02/15/react-labs-what-we-have-been-working-on-february-2024](https://react.dev/blog/2024/02/15/react-labs-what-we-have-been-working-on-february-2024)