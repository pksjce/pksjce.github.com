---
layout: post
category : jest
tags : jest, resct
tagline: Learn jest!
---

[Jest](http://facebook.github.io/jest) is a test runner for testing Javascript code.

A test runner is a piece of software that looks for tests on your codebase and runs them. It also takes care of displaying the result on the CLI interface. Jest boasts of having __very fast__ test execution as it runs tests in parallel.
In addition to this, jest provides an __easy configuration__, __auto mocking__, __snapshot testing__ and __coverage report__.  

## Case Study - Migrate tests in React + Redux App from karma to jest.

### Test Runners

There are various test runners available for Javascript. I have used [Mocha] and [Karma]. 

A test runner provides the following functionality
  - Find tests in the code base.
  - Create proper environment for the tests to run in by mocking functions and so on.

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

In this example, I have used [`React`](https://facebook.github.io/react/) as my view library. `Jest` and DOM testing tools are mostly good for `React` and challenging for other cases.

The sample code can be found at [jest-blog-samples](https://github.com/pksjce/jest-blog-samples/tree/master/dom-testing)

#### App Code

The app itself consists of a react component `App` which prints out a div with text "Hello jest from react".

[__index.js__](https://github.com/pksjce/jest-blog-samples/blob/master/dom-testing/index.js)

```javascript
import React, {Component} from 'react';
import {render} from 'react-dom';

export default class App extends Component {
    render() {
        return <div>This is in react</div>;
    }
}

render(<App/>, document.body)
```

The above app is bundled using [`webpack`](https://github.com/pksjce/jest-blog-samples/blob/master/dom-testing/webpack.config.js)

#### First Test

My first test is to check if the `App` component renders out the `div`.

[__index.test.js__](https://github.com/pksjce/jest-blog-samples/blob/master/dom-testing/__tests__/index.test.js)

```javascript
import React from 'react';
import App from '../index';
import {shallow} from 'enzyme';


describe('The main app', () => {
    it('the app should have text', () => {
        const app  = shallow(<App/>);
        expect(app.contains(<div>This is in react</div>)).toBe(true);
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

```bash
 FAIL  __tests__/index.test.js
  ● Test suite failed to run

    SyntaxError: /Users/pavithra.k/Workspace/jest-blog-samples/dom-testing/__tests__/index.test.js: Unexpected token (8:29)
       6 | describe('The main app', () => {
       7 |     it('the app should have text', () => {
    >  8 |         const app  = shallow(<App/>);
         |                              ^
       9 |         expect(app.contains(<div>This is in react</div>)).toBe(true);
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

jest provides with it an inbuilt babel preprocessor, called `babel-jest`. Essentially this just transpiles all the tests before running them, if there is a .babelrc file present in the application. 

Now on running `jest`, we have

```bash
 PASS  __tests__/index.test.js
  The main app
    ✓ the app should have text (8ms)

Test Suites: 1 passed, 1 total
Tests:       1 passed, 1 total
Snapshots:   0 total
Time:        2.175s
Ran all test suites.
```


