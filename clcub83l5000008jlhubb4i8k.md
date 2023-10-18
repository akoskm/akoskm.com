---
title: "How to test props in React Testing Library"
datePublished: Fri Jan 13 2023 09:21:13 GMT+0000 (Coordinated Universal Time)
cuid: clcub83l5000008jlhubb4i8k
slug: how-to-test-props-in-react-testing-library
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1673600764582/dc292a95-26a5-4db0-9912-01ef290baeb7.png
tags: javascript, web-development, reactjs, testing, jest

---

In this post, we'll learn how to test the props a React Function Component receives with React Testing Library and Jest.

> 2023 October update: the article got popular and I also found new ways to test component props, especially ones that don't stringify well, such as big Immutable.Map. If you're looking for a better way to test your props while also keeping the component functioning, check out Testing Objects

Before React Testing Library appeared, most of us were writing tests with Enzyme.

Enzyme had a completely different approach to testing React components.

We could play with the React components' internal state and test some implementation details.

After finding a component in the DOM, we could do pretty much anything with it.

Call `state()` to modify its state (super bad idea) or `props()` to check what props it received:

```javascript
// This is most likely what we did in Enzyme
const wrapper = mount(
  <div>
    <MyComponent
      includedProp="Success!"
      excludedProp="I'm not included"
    />
  </div>
);
const myComponent = wrapper.find(MyComponent);
expect(myComponent.props().includedProp).to.equal('Success!');
```

The power provided by Enzyme got out of control, and many of us started using it to [test implementation details](https://akoskm.com/testing-implementation-details), which should be avoided at all costs. It makes the tests fragile and the codebase more difficult to change.

We won't review the Jest and React Testing Library setup here because I provided a working [Codesandbox](https://codesandbox.io/s/distracted-cdn-lbdmk9?file=/src/Profile/index.test.tsx) and a GitHub repository where you can both see the setup and experiment with the code.

Let's jump into it and see how we could test the props on a simple component.

Here's a Profile component that displays some data about our user:

```typescript
import PermissionsContainer from "../PermissionsContainer";

interface Props {
  firstName: string;
  lastName: string;
  age: number;
  profileId: string;
}

const Profile = ({ firstName, lastName, age, profileId }: Props) => {
  return (
    <div>
      <span>
        {firstName} {lastName}, {age}
      </span>
      <PermissionsContainer profileId={profileId} />
    </div>
  );
};
export default Profile;
```

# Testing Simple Props and Mocking the Component

Let's say we want to make sure that the `PermissionsContainer` receives the same `profileId` as the `Profile` component, but without actually running any code from `PermissionsContainer`.

If we write a simple test like the one below, it will render the `PermissionsContainer` and run any code inside of it:

```javascript
import { render, screen } from "@testing-library/react";
import Profile from "./index";

describe("Profile", () => {
  const renderProfile = () =>
    render(
      <Profile
        firstName="John"
        lastName="Doe"
        age={35}
        profileId="1234-fake-5678-uuid"
      />
    );

  test("renders app", () => {
    renderProfile();
    expect(screen.getByText(/John Doe/i)).toBeInTheDocument();
  });
});
```

This is not what we want!

So, to mock the `PermissionContainer` module that `Profile` imports we're going to use [`jest.mock`](https://jestjs.io/docs/jest-object#jestmockmodulename-factory-options).

To mock the entire function component, so it's a no-op, we would do something like this:

```typescript
jest.mock("./PermissionsContainer");
```

But we can also tell the mock what `./PermissionsContainer` should return when it's imported by specifying the second parameter.

```typescript
jest.mock("./PermissionContainer",
  () => 'This is PermissionsContainer'
)
```

When `Profile` imports `./PermissionsContainer` instead of receiving the actual module, it will receive this function: `() => 'This is PermissionsContainer'`

See what happens in the DOM when we mock the module:

```typescript
import { render, screen } from "@testing-library/react";
import Profile from "./index";

jest.mock("./PermissionContainer",
  () => 'This is PermissionsContainer'
)

describe("Profile", () => {
  const renderProfile = () =>
    render(
      <Profile
        firstName="John"
        lastName="Doe"
        age={35}
        profileId="1234-fake-5678-uuid"
      />
    );

  test("renders app", () => {
    renderProfile();
    // screen.debug is going to print the current DOM into the console
    screen.debug();
    expect(screen.getByText(/John Doe/i)).toBeInTheDocument();
  });
});
```

We'll see in the console the output of debug:

```typescript
<body>
  <div>
    <div>
      <span>
        John
         
        Doe
        , 
        35
      </span>
      This is PermissionsContainer
    </div>
  </div>
</body>
```

We specified a simple function `() => 'This is PermissionsContainer'` as the default export of the `PermissionsContainer` module, and when the module got imported and was run by React, it produced a simple string.

We're on track!

The code inside `PermissionsContainer` isn't running anymore, but we'd still like to check what props it receives!

All we have to do is update our mock implementation to handle the parameters it receives:

```typescript
jest.mock("./PermissionsContainer", () => ({ profileId }) =>
  `This is PermissionsContainer profileId:${profileId}`
);
```

and we should see the following output after returning the test:

```typescript
<body>
  <div>
    <div>
      <span>
        John
         
        Doe
        , 
        35
      </span>
      This is PermissionsContainer profileId:1234-fake-5678-uuid
    </div>
  </div>
</body>
```

Now, we could write a test that checks if this specific prop value is printed in the DOM:

```javascript
  test("renders Container with the correct props", () => {
    renderProfile();
    expect(
      screen.getByText(
        "This is PermissionsContainer profileId:1234-fake-5678-uuid"
      )
    ).toBeInTheDocument();
  });
```

And this is how we check if `PermissionsContainer` receives the correct prop from its parent component without actually running `PermissionsContainer`.

# Testing Objects Props without Mocking the Component

The above approach is simple to implement, but it has two drawbacks when

* we actually want the underlying component to run
    
* the prop the component receives isn't a simple prop, such as a string, number, or a boolean, but an `Immutable.Map` with loads of properties.
    

Let's assume the profile component has changed a little bit, and it accepts an `Immutable.Map`:

```typescript
import type Immutable from "immutable";
import PermissionContainer from "../PermissionsContainer";

interface Props {
  user: Immutable.Map<string, any>;
}

function Profile({ user }: Props) {
  return (
    <div>
      <span>
        {user.get("firstName")} {user.get("lastName")}, {user.get("age")}
      </span>
      <PermissionContainer user={user} />
    </div>
  );
}
export default Profile;
```

Using the previous approach and simply stringifying the prop

```typescript
jest.mock("./PermissionsContainer", () => ({ user }) =>
  `This is PermissionsContainer user:${user}`
);
```

while also passing a fairly complex `user` object, we would get something like this in the DOM:

```typescript
  test("renders Container with the correct props", () => {
    renderProfile();
    expect(
      screen.getByText(
        "This is PermissionsContainer { 'lastName': 'Doe', 'skills': JavaScript,React,Node.js, 'profileId': '1234-fake-4567-uuid', 'interests': Hiking,Photography,Travel, 'phone': '+1-555-555-5555', 'awards': Employee of the Month,Outstanding Performance Award, 'certifications': AWS Certified Solutions Architect - Associate, 'languages': English,Spanish, 'jobTitle': 'Software Engineer', 'firstName': 'John', 'email': 'john.doe@example.com', 'company': 'Acme Inc.', 'age': 35 }"
      )
    ).toBeInTheDocument();
  });
```

If this is OK with you, keep it, but I think we can do better.

First, let's set up a mock function that will record the props we passed to `PermissionsContainer`:

```typescript
const profileFn = jest.fn();
jest.mock('../PermissionsContainer', () => (props:any) => profileFn(props));
```

We can already assert the props passed to `PermissionsContainer`, but we also want the original component to render.

To achieve this, we must mock the implementation of `permissionsContainerMock`, require the original component, and render it.

```typescript
const permissionsContainerMock = jest.fn();
jest.mock('./PermissionsContainer',
  () => (props:any) => permissionsContainerMock(props)
);

test("renders Container with the correct props", () => {
  profileFn.mockImplementation(props => {
    /**
     * At this point the mock was already called with the props.
     * To render the underlying component, we require the original
     * component and pass the same props that called our mock.
     */
    const Profile = jest.requireActual(
      '../components/Profile',
    ).default;
    return <Profile {...props} />;
  });
  
});
```

Finally, here's what the entire test looks like:

```typescript
import { Map } from "immutable";
import { render, screen } from "@testing-library/react";
import Profile from "./index";

const profileFn = jest.fn();
jest.mock('../PermissionsContainer', () => (props:any) => profileFn(props));

describe("Profile", () => {
  const user = Map({
    firstName: "John",
    lastName: "Doe",
    age: 35,
    profileId: "1234-fake-4567-uuid",
    email: "john.doe@example.com",
    phone: "+1-555-555-5555",
    company: "Acme Inc.",
    jobTitle: "Software Engineer",
  });

  const renderProfile = () => render(<Profile user={user} />);

  test("renders app", () => {
    profileFn.mockImplementation(props => {
      const Profile = jest.requireActual(
        '../PermissionsContainer',
      ).default;
      return <Profile {...props} />;
    });
    renderProfile();
    expect(profileFn).toHaveBeenCalledWith({
      user
    })
  });
});
```

Because `jest.mock` doesn't work in Codesandbox, I created a repository showing all this in action. Check it out:

[https://github.com/akoskm/test-react-props](https://github.com/akoskm/test-react-props)

Have you got any questions? Are you moving from Enzyme to jest?

Let me know in the comments below if you're facing any difficulties!