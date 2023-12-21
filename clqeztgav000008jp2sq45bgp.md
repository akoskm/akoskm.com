---
title: "Your Guide to Testing React Components"
seoTitle: "Testing React Components: Ultimate Guide"
seoDescription: "Master React component testing with this guide on Enzyme-RTL conversion, prop testing, hooks, DOM structures, and optimization"
datePublished: Thu Dec 21 2023 09:21:13 GMT+0000 (Coordinated Universal Time)
cuid: clqeztgav000008jp2sq45bgp
slug: your-guide-to-testing-react-components
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1702799317019/2686336d-4523-4738-b7c5-188194bbc2af.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1702803382618/3fa4d02e-ce25-48c3-aaab-8496e4c91e18.png
tags: web-development, reactjs, testing, testing-library, react-testing-library

---

This blog post gives you a bird's eye view of the different ways to test your React components and some ideas for converting your tests from Enzyme to React Testing Library.

One of my most significant projects this year was moving the Enzyme tests of a gigantic fintech application to React Testing Library (that's a mouthful, so I'll use **RTL** in the text).

While there are tools to automate this process, we didn't want to simply write the equivalent of the enzyme tests in RTL but also improve the existing tests and cover previously untested behaviors. We learned a lot about how to find alternatives to the "Enzyme way" of testing, like prop assertions.

So this year, I wrote mainly about testing React Components.

As a result, the Testing React series was born, containing eight articles so far:

%[https://akoskm.com/series/react-testing] 

So, let's dive into it and see what to use and when!

## Component Props

If you read only the first article of the series, you're already good to go. [How to test props in React Testing Library](https://akoskm.com/how-to-test-props-in-react-testing-library) presents you the simplest way to prop testing: mocking your component and, instead, the real thing returning a string that prints out the props, something like this:

```typescript
jest.mock("./PermissionsContainer", () => ({ profileId }) =>
  `This is PermissionsContainer profileId:${profileId}`
);
```

While this works great for most components, sometimes you run into two issues with this approach:

* the prop is an object that's huge when stringified
    
* the rest of the test relies on the behavior of the component that you mocked
    

This is why I edited the blog post and provided an alternative to this approach that lets you assert prop values but also call the actual component:

```typescript
const permissionsContainerMock = jest.fn();
jest.mock('./PermissionsContainer',
  () => (props:any) => permissionsContainerMock(props)
);

test("renders Container with the correct props", () => {
  permissionsContainerMock.mockImplementation(props => {
    const Profile = jest.requireActual('../components/Profile').default;
    return <Profile {...props} />;
  });

  // expect(permissionsContainerMock).toHaveBeenCalledWith(your props);
});
```

These two approaches will get you covered for prop testing and turning those Enzyme `component.prop` tests into RTL.

## Component Hooks

Hooks are incredible mechanisms in React that help you abstract and share some logic between components.

Usually, you want to test them with your component code, but some hooks are too complex to be verified with the component.

This is why the RTL team gave us `renderHook` that lets you render your hook and verify how it behaves while loading, receiving new arguments, etc.

```typescript
    const { result } = renderHook(() =>
      usePermissionsHook("1234-fake-4567-uuid")
    );
    expect(result.current.isLoading).toEqual(true);
    expect(result.current.permissions).toEqual([]);
```

[How to Test Custom Hooks with React Testing Library and Jest](https://akoskm.com/how-to-test-custom-hooks-with-react-testing-library-and-jest) gives you a complete walkthrough on testing React Hooks.

## Accessing the DOM

While finding DOM elements based on CSS queries with Enzyme was easy, this is not so simple because of RTL's philosophy.

### Classnames

But sometimes, you want to ensure that an alert has the `warning` or `success` styling.

In [How to test a className with Jest and React testing library](https://akoskm.com/how-to-test-a-classname-with-jest-and-react-testing-library), I present you two ways to do this, but my preferred method is finding the text inside the alert using the `selector` argument of the RTL query:

```typescript
render(<Alert type="error" message="Something went wrong" />);

expect(
  screen.getByText(
    'Something went wrong',
    { selector: '.alert.alert-error' }
  )
).toBeInTheDocument();
```

This differs from what you used to do with Enzyme because RTL won't let you look only for the CSS selectors. First, you must specify a piece of text that will be visible to the user – which is aligned with RTL's philosophy:

> React Testing Library aims to test the components how users use them. Users see buttons, headings, forms and other elements by their role, not by their id, class, or element tag name.

### Complex structures

Testing the DOM becomes increasingly more complex when you move on to headings or table structures because RTL supports many roles.

[Test complex DOM structures with React Testing Library](https://akoskm.com/test-complex-dom-structures-with-react-testing-library) provides you with examples for testing headings and table cells.

### Snapshots

If none of these methods work, you can still use Snapshot testing; that's a great way to assert complex DOM structures that rarely change. I found an excellent application: checking if the component rendered the right SVG.

SVGs are sometimes too long to put into a test, and once you know you have the SVG for your "Home" icon, it rarely changes.

[Jest Snapshots: Beginner's Guide to Snapshot Testing](https://akoskm.com/jest-snapshot-testing) helps you get started with snapshot testing.

## Getting Better

Not exclusive to RTL or React Component testing, but after seeing so many tests and good/bad patterns, I felt the need for an article discussing strong vs. weak tests.

[How to Write Stronger Unit Tests with Jest](https://akoskm.com/how-to-write-stronger-unit-tests-with-jest) gives you hints on how to write better tests with almost no extra effort.

## Getting Faster

On the bright side, if you've reached this point, you have many tests! Handling their slowness might seem like a good problem to have.

Well-known testing mechanisms such as beforeEach/all can encapsulate the repeated parts of your tests, but remember, they add to the runtime of each test.

[How to speed up your integration tests](https://akoskm.com/how-to-speed-up-your-integration-tests), through a practical example, shows you how we speeded up our CI time.

## Conclusion

Finally, I'd like to balance out all this testing with one of my favorite tweets:

%[https://twitter.com/rauchg/status/807626710350839808] 

Don't dive straight into writing unit tests if you don't have an automated method for verifying the essential functionality of your app.

Spend your and your team's time where it makes the most sense.

Do everything with moderation, including unit tests.