---
title: "How to test a className with Jest and React testing library"
seoTitle: "Test className: Jest & React Testing Library"
seoDescription: "How to test className with react testing library's container, selector option, and role selectors to enhance codebase resiliency"
datePublished: Sat Jul 08 2023 14:53:06 GMT+0000 (Coordinated Universal Time)
cuid: clju4ku0m000309mbbhj42qdi
slug: how-to-test-a-classname-with-jest-and-react-testing-library
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1688827860920/4cb750ae-30de-4beb-91a8-ac343d5f2d7a.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1688827751522/f2dcd681-6088-49e6-806d-aeb3129275fd.png
tags: reactjs, testing, jest, testing-library, react-testing-library

---

When testing JavaScript applications, one of the most common checks I see is if an element has a specific class or an element with some class has some text.

Think of an Alert like this:

```xml
<div role="alert" className="alert alert-error">
  Something went wrong
</div>
```

Checks like these with Enzyme were pretty straightforward to accomplish:

```typescript
const component = mount(
  <Alert type="error" message="Something went wrong" />
);

expect(
  component.find('.alert.alert-error').text()
).toBe('Something went wrong');
```

But what if you're using React Testing Library?

One of the core philosophies behind [React Testing Library](https://testing-library.com/docs/react-testing-library/intro) is to prevent you from testing implementation details. In other words, you can't easily test things the user won't see. `.alert` is such an implementation detail because the user can't see it.

There's a nice paragraph explaining why this is the reason in the docs:

> React Testing Library aims to test the components how users use them. Users see buttons, headings, forms and other elements by their role, not by their `id`, `class`, or element tag name. Therefore, when you use React Testing Library you should avoid accessing the DOM with the `document.querySelector` API. (You *can* use it in your tests, but it's not recommended for the reasons stated in this paragraph.)
> 
> [https://testing-library.com/docs/react-testing-library/migrate-from-enzyme](https://testing-library.com/docs/react-testing-library/migrate-from-enzyme)

Considering this philosophy, you would write a test like this in React Testing Library:

```typescript
render(<Alert type="error" message="Something went wrong" />);

expect(screen.getByText('Something went wrong')).toBeInTheDocument();
```

But this isn't as accurate as the enzyme version of the test. Here's why:

1. What if the text due for some reason appears in multiple places in the document, but you're only interested in the alert
    
2. What if the error class is applied dynamically based on some constraint, and you want to make sure, in this specific case, it gets applied
    

So sometimes, you want to ensure that when an error-type alert is displayed, it has the correct error class.

There are 3 different ways to accomplish this:

# Using render's container

[`container`](https://testing-library.com/docs/react-testing-library/api/#container-1) contains the result of your rendering, and you can run queries like `querySelector` or `getElementsByClassName` on them. To check if the alert appeared with the correct class and message, you could do something like this:

```typescript
const { container } = render(
  <Alert type="error" message="Something went wrong" />
);
expect(
  container.querySelector('.alert.alert-error')
).toHaveTextContent('Something went wrong');
```

This approach should be used as a last resort. To quote the docs again:

> Users see buttons, headings, forms and other elements by their role, not by their `id`, `class`, or element tag name. Therefore, when you use React Testing Library you should avoid accessing the DOM with the `document.querySelector` API.

# Using the selector option

I prefer not to use queries on containers, and thankfully [`getByText`](https://testing-library.com/docs/bs-react-testing-library/examples/#getbytext) lets us choose text in the DOM by matching both the text itself and the surrounding element using `selector`:

```typescript
render(<Alert type="error" message="Something went wrong" />);

expect(
  screen.getByText(
    'Something went wrong',
    { selector: '.alert.alert-error' }
  )
).toBeInTheDocument();
```

# Using role

This is the best solution, but in my experience with React/enzyme codebases `role` attributes aren't always used correctly, and some apps lack them entirely.

If you're working on an app that uses roles correctly, congrats! You can write the nicest possible React Testing Library test to check if the alert appeared and if it has the correct classes without breaking the library's convention:

```typescript
render(<Alert type="error" message="Something went wrong" />);

expect(
  screen.getByRole(
    'alert',
    { name: 'Something went wrong' }
  )
).toHaveClass('alert', 'alert-error');
```

# Conclusion

In this article, we explore three different ways to check if an element contains the expected text and if the element itself has the correct classes.

Which type of check you'll apply highly depends on the quality of the current codebase, but as a rule of thumb, try to go with the `[get|query|find]By` selectors with the combination of the `selector` options and the `toHaveClass` function.

Try to avoid testing implementation details as much as possible. Doing so will improve the resiliency of your codebase and will result in a much better development experience.

I hope you learned something new today!

If you have questions, please share them in the comments below.

Thank you for reading this post, and see you in the next one.

Bye! 👋