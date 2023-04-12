---
title: "How to Test Props in React with Jest"
datePublished: Wed Apr 12 2023 07:59:07 GMT+0000 (Coordinated Universal Time)
cuid: clgdehc1r000109mjg8hugkb9
slug: how-to-test-props-in-react-with-jest
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1681286294040/0e66e0a2-afb1-45de-86ad-7f0ade05c7e9.png
tags: javascript, web-development, reactjs, testing, jest

---

In our [previous article](https://akoskm.com/how-to-test-props-in-react-testing-library), we explored how we can test React Component props by simply mocking the component and turning the props into strings:

```typescript
jest.mock("./Profile", () => ({ profileId }) =>
  `This is Profile profileId:${profileId}`
);
```

And then, in our tests, checking if that string is appearing in the DOM:

```typescript
  test("renders Container with the correct props", () => {
    renderProfile();
    expect(
      screen.getByText(
        "This is Profile profileId:1234-fake-5678-uuid"
      )
    ).toBeInTheDocument();
  });
```

This is one of the simplest ways to test components where the props are primitives (strings, numbers, booleans) or translate well into strings.

But how would this assert look like if the Profile component received an `Immutable.List` with hundreds of elements or a huge `Immutable.Map` with 150 keys?

The above strategy doesn't scale well and stops working for certain patterns.

We usually run into this when testing a container component:

The container component fetches data and receives an object that the container uses to render other components.

If you're new to this pattern, they usually look like this:

```typescript
import Profile from "./Profile";
import fetchUserData from './services';

export default function App() {
  const { data, loading } = fetchUserData();

  if (loading) return null;

  return (
    <div className="App">
      <h1>Hello CodeSandbox</h1>
      <h2>Start editing to see some magic happen!</h2>
      <Profile user={user} />
    </div>
  );
}
```

And let's assume that `fetchUserData` in `data` returns an `Immutable` object.

Small `Immutable` objects can be stringified, so if you mock the Profile component the same as we did earlier.

```typescript
jest.mock("./Profile", () => ({ user }) =>
  `This is Profile user:${user}`
);
```

and your profile only contains a few keys, in the DOM, we'll get the following:

```typescript
Map { "firstName": "John", "lastName": "Doe", "age": 35, "profileId": "1234-fake-4567-uuid" }
```

So far, so good. You'll have no issue asserting the presence of such text in the DOM.

But as we pointed out, this approach stops scaling as soon as your object grows in size (usually when you start using fixtures in your tests) or when you can't assert the stringified version of the prop in the DOM.

To solve this limitation of stringifying the props and swapping out the return value of `./Profile` for a simple string, you could return a *mock function* instead, that you can do assertions later on:

```typescript
const mockProfile = jest.fn().mockReturnValue(<>mock Profile</>);
jest.mock("./Profile", () => (props: any) => mockProfile(props));
```

With `mockProfile` now you can do checks beyond checking the stringified version of the prop that was passed to your component:

```typescript
test("renders app", () => {
  const user = Immutable.Map({
    firstName: "John",
    lastName: "Doe",
    age: 35,
    profileId: "1234-fake-4567-uuid"
  });

  jest.spyOn(fetchUserData).mockReturnValue(user);

  render(<App />);

  expect(screen.getByText("Hello CodeSandbox")).toBeInTheDocument();
  expect(mockProfile).toHaveBeenCalledWith({
    user
  });
});
```

In the above test, we spy on `fetchUserData` and return a specific user object using `mockProfile`.

Now we can assert that our component receives a user prop, that's an `Immutable.Map` that looks exactly like the one `fetchUserData` passed down to our component.

This is how you test props in React with Jest if the object you want to assert is too big to have the stringified version checked or it simply doesn't stringify well.

Thanks for reading!

Until next time,

Akos