---
title: "Test complex DOM structures with React Testing Library"
seoTitle: "React Testing Library: Test Complex DOM"
seoDescription: "Optimize DOM Testing using React Testing Library for enhanced accuracy, maintainability, and precise UI targeting with specific queries and options"
datePublished: Tue Jun 13 2023 12:00:39 GMT+0000 (Coordinated Universal Time)
cuid: cliu8erwz047yrhnva1x86z1m
slug: test-complex-dom-structures-with-react-testing-library
cover: https://cdn.hashnode.com/res/hashnode/image/upload/v1686492258340/930953a9-2b83-4e6b-a2c9-8fe4f40e5bd9.png
ogImage: https://cdn.hashnode.com/res/hashnode/image/upload/v1686492267996/d079556f-e875-496c-8ab8-445713202779.png
tags: web-development, reactjs, testing, jest, testing-library

---

Establishing sound software engineering principles in teams is one thing, but enforcing them is an entirely different problem to solve.

Tools such as eslint, the TypeScritp compiler, or Prettier were the answers for coding conventions, types, and formatting.

But what about tests?

# Why did we switch from Enzyme to React Testing Library

With Enzyme, you had a gazillion (bad) ways to write tests. I've seen extremes, like testing a login form component but not typing into the input but setting state directly and not even clicking the submit button but triggering the button's prop. In other words, testing implementation details.

React Testing Library (RTL) was the answer we needed, and I was happy to move more than five projects to use RTL in tests.

What RTL did differently from Enzyme was that it simply made it impossible (or super hard) to test implementation details.

But at the same time, RTL didn't let you query DOM structures anymore as you could do in Enzyme:

```javascript
// JavaScript (React, Jest, Enzyme)

import React from 'react';
import { shallow } from 'enzyme';
import YourComponent from './YourComponent';

describe('YourComponent', () => {
  it('should find the .card-subtitle element and check if it has the text "Upcoming"', () => {
    const wrapper = shallow(<YourComponent />);
    const cardSubtitle = wrapper.find('.card-subtitle');
    expect(cardSubtitle.text()).toEqual('Upcoming');
  });
});
```

# How we used React Testing Library the wrong way

We always felt that the selectors in RTL were limited. Still, we found a very effective way that almost always worked and allowed us to move quickly – without upsetting the project leaders with our refactoring journeys. 😄

It was this:

```javascript
// JavaScript - Jest and React Testing Library

import React from 'react';
import { render, screen } from '@testing-library/react';
import App from './App';

test('checks if the screen contains the text "Upcoming"', () => {
  render(<App />);
  const upcomingElement = screen.getByText(/Upcoming/i);
  expect(upcomingElement).toBeInTheDocument();
});
```

Probably the simplest and most effective way to check hat something is present in the DOM.

This worked, but it also had quite a few drawbacks:

* if the screen contained more than one "Upcoming" texts
    
* you didn't actually verify where the "Upcoming" text appeared, only that it was *somewhere* in there.
    

What changed the game for us were the [options](https://testing-library.com/docs/queries/bytext) we could use with queryBy, findBy, and getBy.

```javascript
import { render, screen } from '@testing-library/react';
import YourComponent from './YourComponent';

test('finds the text "Upcoming" inside an element with class ".card-subtitle"', () => {
  render(<YourComponent />);
  const subtitleElement = screen.getByText(/Upcoming/i, { selector: '.card-subtitle' });
  expect(subtitleElement).toBeInTheDocument();
});
```

# How to use React Testing Library the right way

In short: write more specific queries. The solutions lies in the options argument I mentioned above. ByText, ByRole, ByTitle, and other selectors each have a specific set of options. Check out the previous link to see what those are.

Next I'lls how you the selector and options usage for the some of the structures you'll most often encounter in web apps:

## Hrefs with text and content

On login forms usually you have a Sign up link that takes you to the signup page if you haven't signed up yet.

Instead of simply checking if the Sign up text is in the document with `expect(screen.getByText('Sign up')).toBeInTheDocument()` if we use the options and different assertion we can check both the link text and the link itself:

```javascript
// JavaScript (React Testing Library with Jest)

import React from 'react';
import { render, screen } from '@testing-library/react';
import '@testing-library/jest-dom/extend-expect';
import { BrowserRouter as Router } from 'react-router-dom';
import MyComponent from './MyComponent';

test('finds a link with text "Sign up" and href "/signup"', () => {
  render(
    <Router>
      <MyComponent />
    </Router>
  );

  const linkElement = screen.getByRole('link', { name: /Sign up/i });
  expect(linkElement).toHaveAttribute('href', '/signup');
});
```

## Headings

Next up, lets say after a login we'll find ourself on the Dashboard page, and we want to check that. However, Dashboard might appear in the Side Menu, notification messages or even somewhere in a dynamic text on the page. Again, using `expect(screen.getByText('Sign up')).toBeInTheDocument()` would be problematic.

This is where the `level` option of the `getByRole` selector comes to help:

```javascript
// JavaScript (React Testing Library with Jest)

import React from 'react';
import { render, screen } from '@testing-library/react';
import '@testing-library/jest-dom/extend-expect';
import Dashboard from './Dashboard';

test('renders Dashboard with level 3 heading', () => {
  render(<Dashboard />);
  const heading = screen.getByRole('heading', { level: 3, name: /Dashboard/i });
  expect(heading).toBeInTheDocument();
});
```

## Table cells

If you weren't convinced that `getByText` isn't the right way to check if certain DOM elements are present on the page, seeing tests for tables will change your mind.

Take for example this user management table:

```typescript
export default function Table() {
  return (
    <table>
      <thead>
        <tr>
          <th>Name</th>
          <th>Role</th>
          <th>Action</th>
        </tr>
      </thead>
      <tbody>
        <tr>
          <td>John Doe</td>
          <td>admin</td>
          <td>Edit</td>
        </tr>
      </tbody>
    </table>
  );
}
```

It's not unusual that the tests for something like this is simply:

```javascript
// JavaScript (React Testing Library with Jest)

import React from 'react';
import { render, screen } from '@testing-library/react';
import '@testing-library/jest-dom/extend-expect';
import Table from './Table';

test('checks if table row content is "John DoeadminEdit"', () => {
  render(<Table />);
  const tableRowContent = screen.getByText(/John DoeadminEdit/i);
  expect(tableRowContent).toBeInTheDocument();
});
```

which is neadless to say, hard to read and maintain. Besides that it matches just about any place on the UI that has this text, which can lead to eventual false-positives.

[within](https://testing-library.com/docs/dom-testing-library/api-within/) is a special selector in RTL that will let you do a new query within an element. Using this helper we can pinpoint the table row we're interested in and inside that check the specific columns:

```javascript
// JavaScript (React Testing Library)

import React from 'react';
import { render, within } from '@testing-library/react';
import '@testing-library/jest-dom/extend-expect';
import Table from './Table';

function checkRowContents(row, name, role, action) {
  const columns = within(row).getAllByRole('cell');
  expect(columns).toHaveLength(3);
  expect(columns[0]).toHaveTextContent(name);
  expect(columns[1]).toHaveTextContent(role);
  expect(columns[2]).toHaveTextContent(action);
}

test('demonstrates the usage of within helper and getByRole query', () => {
  const { getByRole } = render(<Table />);
  const table = getByRole('table');
  // first rowgroup is for the thread second is for tbody
  const tbody = within(table).getAllByRole('rowgroup')[1];
  const rows = within(tbody).getAllByRole('row');

  checkRowContents(rows[0], 'John Doe', 'admin', 'edit');
});
```

This leaves us with a much clearer test.

# Conclusion

These were some of the ways how we're making our React Testing Library tests more readable and how we use query options to precisely pinpoint elements on the UI.

If you found this useful, let me know it in the comments.

Thanks for reading and have fun writing those tests!

\- Akos