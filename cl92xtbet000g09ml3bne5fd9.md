# How to solve React's "This synthetic event is reused for performance reasons" warning

React does many things under the hood to improve performance.

One of those things is passing synthetic event objects to event handlers instead of real event objects.

*This is only relevant for React 16 and React Native! In React 17, you don't have this problem anymore.*

When the event handler call ends, React nullifies the properties of the synthetic event object.

If you try to access any of the `e.target` properties after the event handler has exited, you will get `null`.

## Demo

Let me demonstrate this with a simple example:

```
  function handleChange(e) {
    console.log("enter", e.target.value);
    setTimeout(() => {
      console.log(e.target);
    }, 100);
    console.log("exit", e.target.value);
  }
```

![warning.gif](https://cdn.hashnode.com/res/hashnode/image/upload/v1665403497259/mZMi-tYbP.gif align="left")

You can also try the sandbox [here](https://codesandbox.io/s/happy-dust-x256lb?file=/src/App.js).

The warning in the console:

*Warning: This synthetic event is reused for performance reasons. If you see this, you're accessing the property `target` on a released/nullified synthetic event. This is set to null. If you must keep the original synthetic event around, use event.persist(). See https://fb.me/react-event-pooling for more information.*

## Real-world scenario

I wanted to add a new input to an existing form in a React 16 application.

The codebase used refs to capture input values instead of `onChange` handles. I thought: "We can do better than this." and added a proper `onChange` handle for my new input.

Because of the existing logic, I also had to merge the current state with the value of my input:

```
  function handleEmailInput(e) {
    setState((prevState) => ({
      ...prevState,
      userEmail: e.target.value,
    }));
  }
```
And this is where the problem surfaced:

*Warning: This synthetic event is reused for performance reasons.*

Alongside with an error on that line where I was setting the new state:

```
Uncaught TypeError: e.target is null
```

I was curious about what's happening under the hood, so I added a `console.log` to see what happens inside `setState`:

```
  function handleEmailInput(e) {
    setState((prevState) => {
      console.log(e.target);
      return {
        ...prevState,
        userEmail: e.target.value,
      };
    });
  }
```

On the first keypress inside the form, nothing unusual has happened. I received the target element on the console, as I expected:

```
<input id="email" class="form-control" style="max-width: 400px;" name="email" placeholder="eg. bar@example.com" type="email" aria-invalid="false" value="" data-com.bitwarden.browser.user-edited="yes">
```

However, on the next keypress, I received `null`.

As we explained at the beginning of this post and the [docs](https://reactjs.org/docs/legacy-event-pooling.html) also write, this was happening because the `SyntheticEvent` object that `handleEmailInput` received had its properties nullified after the event handler call ended.

And because `setState` actions are asynchronous and are batched for performance gains, the callback will be called after we have already left the `handleEmailInput` function.

## Solutions

There are two solutions to overcome this.

First is what the docs tell you:

Call `e.persist();` to persist the event that entered your input handler.

But I've found another way to deal with this problem. 

I'm simply copying the value of `e.target.value` contained in the Syntethic event, so I can access it even after the function has exited:

```
  function handleEmailInput(e) {
    const { value } = e.target;
    setState((prevState) => ({
      ...prevState,
      userEmail: value,
    }));
  }
```

I hope this post helped you to avoid the warning and fix your application's behavior.

Let me know if you have any comments or questions regarding this solution! 