---
layout: post
category : jest
tags : jest, resct
tagline: Learn jest!
---

[Jest](http://facebook.github.io/jest) is a test runner for testing Javascript code.

A test runner is a piece of software that looks for tests on your codebase and runs them. It also takes care of displaying the result on the CLI interface. Jest boasts of having __very fast__ test execution as it runs tests in parallel.
In addition to this, jest provides an __easy configuration__, __auto mocking__, __snapshot testing__ and __coverage report__.  

### Test Runners

There are various test runners available for Javascript. I have used [Mocha] and [Karma]. 

A test runner provides the following functionality
  - Find tests in the code base.
  - Create proper environment for the tests to run in by mocking functions and so on.

In this post, we will look at installing and getting started with DOM testing a react application with jest. Theoretically these techniques could be used with other view frameworks like deku, preact and so on. But there may be challenges with finding proper rendering utils which are mature and feature rich.
### Setting Up Jest

Install `jest` for your project

```javascript
npm install --save-dev jest
```

Add `npm test` script

__package.json__
```javascript
{
    ...
    scripts: {
        ...
        test: 'jest'
    }
    ...
}
```

### Dom Testing

In the following examples we will test mostly trivial examples of general testing areas for a front end application.
This involves
 - Basic DOM testing
 - Event simulation and testing
 - Window events simulation
 - Async function testing
 - Snapshot testing

The sample code can be found at [jest-blog-samples](https://github.com/pksjce/jest-blog-samples/tree/master/dom-testing)

#### Basic DOM Test

The app itself consists of a react component `App` which prints out a div with text "Hello jest from react".

[__index.js__](https://github.com/pksjce/jest-blog-samples/blob/master/dom-testing/index.js)

```javascript
import React, {Component} from 'react';
import {render} from 'react-dom';

export default class App extends Component {
    render() {
        return <div>Hello jest from react</div>;
    }
}

render(<App/>, document.body)
```

The above app is bundled using [`webpack`](https://github.com/pksjce/jest-blog-samples/blob/master/dom-testing/webpack.config.js)

My first test is to check if the `App` component renders out the `div`.

[__index.test.js__](https://github.com/pksjce/jest-blog-samples/blob/master/dom-testing/__tests__/index.test.js)

```javascript
import React from 'react';
import App from '../index';
import {shallow} from 'enzyme';


describe('The main app', () => {
    it('the app should have text', () => {
        const app  = shallow(<App/>);
        expect(app.contains(<div>Hello jest from react</div>)).toBe(true);
    })
})
```

Lets break this down.

* We need to import `react`(for the purpose of using jsx) and the `App`(to instantiate and test) source.
* [`enzyme`](https://github.com/airbnb/enzyme) is JavaScript test utility that makes rendering `React` apps for testing much easier.We will use it to shallow render our `App` component and inspect the resulting tree...
* We describe a test suite named "The main app".
* Our first test is named "the app should have text"
* We [shallow render](https://github.com/airbnb/enzyme/blob/master/docs/api/shallow.md) our component and store it in a `const`...
* `expect` the app to contain the expected `div`.

#### Run the test with `jest`

Assuming jest is installed locally for the application, let's first start the test runner.

```bash
jest 
```

This automatically finds the tests to be run. If required, one can specify [testPathDirectories](https://facebook.github.io/jest/docs/configuration.html#testpathdirs-array-string) and [testIgnorePaths](https://facebook.github.io/jest/docs/configuration.html#testpathignorepatterns-array-string).

```javascript
 FAIL  __tests__/index.test.js
  ● Test suite failed to run

    SyntaxError: /Users/pavithra.k/Workspace/jest-blog-samples/dom-testing/__tests__/index.test.js: Unexpected token (8:29)
       6 | describe('The main app', () => {
       7 |     it('the app should have text', () => {
    >  8 |         const app  = shallow(<App/>);
         |                              ^
       9 |         expect(app.contains(<div>Hello jest from react</div>)).toBe(true);
      10 |     })
      11 | })
      
      at Parser.pp.raise (node_modules/babylon/lib/index.js:4215:13)

Test Suites: 1 failed, 1 total
Tests:       0 total
Snapshots:   0 total
Time:        1.07s
Ran all test suites.
```

We have our first failing test!

The error mainly points at a syntax error. This is because are test code is wriiten in ES2015 and it is not transpiled and ready for node.

This means `jest` needs to preprocess our test code and imported source code to ES5. For this, jest has great integration with [`babel`](https://babeljs.io/)

#### Add `babel` Support

Add a [`.babelrc`](https://github.com/pksjce/jest-blog-samples/blob/master/dom-testing/.babelrc) file to your application. In this you can specify the babel plugins required to transpile every ES2015 feature that your application uses.
`babel` provides excellent presets for [`react`](https://babeljs.io/docs/plugins/preset-react/) and [`es2015`](https://babeljs.io/docs/plugins/preset-es2015/). Presets are just a collection of relevant babel plugins for a logical set of transformations.

jest provides with it an inbuilt babel preprocessor, called `babel-jest`. Essentially, this just transpiles all the tests before running them, if there is a .babelrc file present in the application.

Now on running `jest`, we have

```javascript
 PASS  __tests__/index.test.js
  The main app
    ✓ the app should have text (8ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        2.175s
Ran all test suites.
```

#### Test an event execution

Using `enzyme` we can simulate events in our rendered components.

We intend to build an App Component that is clickable. It should change the text displayed after the click on it's body.

To test this component we need

 - Test for default content on first load.
 - Test for first toggle of click and expect the change in text.
 - Test for second toggle of click and again expect change in text to the default.

[src/app/index.test.js]()

```javascript
import React from 'react';
import App, {DEFAULT_TEXT, CLICKED_TEXT} from '../index';
import {shallow} from 'enzyme';

describe('The main app', () => {
    let app;
    beforeEach(() => {
        app  = shallow(<App/>);
    })

    it('the app should have text', () => {
        expect(app.text()).toBe(DEFAULT_TEXT);
    })
    it ('should change text on click', () => {
        app.simulate('click');
        expect(app.text()).toBe(CLICKED_TEXT);
        app.simulate('click');
        expect(app.text()).toBe(DEFAULT_TEXT);
    })
})
```

`jest` provides us with the following [global testing methods](http://facebook.github.io/jest/docs/api.html)

__`describe(name,fn), it(name, fn), test(name, fn)`__

These are the basic tools in a javascript testing environment's [arsenal](https://en.wikipedia.org/wiki/Arsenal_F.C.).
`describe` is used to group a set of related tests together into a __test suite__.
`it` or `test` is used to write your actual test where you can build an independent environment and test particular units or behaviour.

__`afterEach(fn)/beforeEach(fn)/afterAll(fn)/beforeAll(fn)`__

These functions run before/after each test or before/after the test suite. You can group all your initialization and tear down code in these hooks which are called during a test cycle.

Keeping the above info in context, we write a `beforeEach` function which creates a shallow rendering of the Button `App`.
After this we write our tests for each of the behaviour we expect out of the component. Enzyme has a [bunch of helper api](http://airbnb.io/enzyme/docs/api/shallow.html) to inspect a rendered object. We can use these to check rendered DOM for desired changes.

The corresponding react component would look like this.

[__index.js__](https://github.com/pksjce/jest-blog-samples/blob/master/event-testing/index.js)

```javascript
import React, {Component} from 'react';
import {render} from 'react-dom';

export const DEFAULT_TEXT = "Hello jest from react";
export const CLICKED_TEXT = "This has been clicked";

export default class App extends Component {
    constructor() {
        super();
        this.state = {
            clicked: false
        }
        this.handleClick = this.handleClick.bind(this);
    }
    render() {
        return <div onClick={this.handleClick}>{this.state.clicked ? CLICKED_TEXT : DEFAULT_TEXT}</div>;
    }
    handleClick() {
        this.setState({
            clicked: !this.state.clicked
        })
    }
}

render(<App/>, document.body)
```

#### `window` event testing





