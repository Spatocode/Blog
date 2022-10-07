---
title: "Code Coverage from Cypress End-to-End Test"
date: 2021-06-19T00:59:54+01:00
categories: ["Software"]
tags: ["javascript", "frontend", "testing", "cypress"]
images: ["/cypress-cover.jpg"]
draft: false
---

{{< figure src="/cypress-cover.jpg" alt="cypress-code-coverage" >}}

Testing is an important part of software engineering. It helps shipping code faster while making sure our application works properly when changes are made. It makes sure unintended bugs are identified proactively before application moves into production. But how do we know when our tests are enough? Are there parts of the application still untested? That's where code coverage comes into play.

Code coverage is the measure which describes the degree of which a source code is executed during testing. This shows us which parts of the source code are executed when tests are run. Cypress as a testing library does not have code coverage out of the box. Some configurations and setup are needed to provide code coverage with cypress.

### Instrumentation
Instrumentation is the process of computing the source code lines executed during tests, parsing it to find all functions, statements, branches, and then inserting counters into the code. These counters are basically the number of times these functions, statements and branches are executed.

```js
// square.js
function square (num) {
  return num**2
}

module.exports = { square }
```

Instrumenting the above source code source code might look like this

```js
// this counts the number of times each functions and statements are executed
const c = (window.__coverage__ = {
  // we have just one function, thus [0]
  f: [0],
  // we have three statements, thus [0,0,0]
  s: [0, 0, 0],
})
```

Let's write a test for our program

```js
// square.spec.js
const { square } = require('./square')

it('squares a number', () => {
  expect(square(5)).to.equal(25)
})
```

Running the above test will add counters to the coverage object which might look like this

```js
const c = (window.__coverage__ = {
  f: [1],
  s: [1, 1, 1]
})

// the first statement defines our function and gets incremented
c.s[0]++
function square(num) {
  // when our function is called f gets incremented
  c.f[0]++
  // the second statement gets called and also incremented
  c.s[1]++

  return num**2
}

// 3rd statement is called and incremented as well
c.s[2]++
module.exports = { square }
```

In this case, our test has achieved 100% code coverage. This coverage object will be generated into a human-readable report and saved. Cypress does not instrument our code, we have to do it ourselves using [babel-plugin-istanbul](https://github.com/istanbuljs/babel-plugin-istanbul) as part of code transpilation pipeline or using [nyc](https://github.com/istanbuljs/nyc)(a command-line interface to [istanbul](https://istanbul.js.org/)).

### Setting up Cypress code coverage

To setup our code coverage we need to first install the dependencies ```npm i -D nyc babel-plugin-istanbul @cypress/code-coverage```

Add the plugin to .babelrc file. This instruments our code as part of it's transpilation.

```js
{
  "plugins": ["istanbul"]
}
```

If we don't want to use ```babel-plugin-istanbul``` for instrumentation we can use ```nyc```. To instrument the code located in our src folder using nyc, we can run the command below which generates an instrumented version of our src folder. We specify the ```--compact=false``` flag to generate human-friendly output.

```sh
npx nyc instrument --compact=false src instrumented
```

Add the following code in your cypress files

```js
// cypress/support/index.js
import '@cypress/code-coverage/support'
```

```js
// cypress/plugins/index.js
module.exports = (on, config) => {
  require('@cypress/code-coverage/task')(on, config)
  // include any other plugin code

  // return the config object with any changed environment variables
  return config
}
```

With our setup perfectly done, running our test will generate our final code coverage and saved on the ```.nyc_output folder```. This contains a json file which can be used to generate coverage report in different formats. You can generate a report using ```nyc``` by running

```sh
npx nyc report --reporter=text-summary
```

Also the plugin [@cypress/code-coverage](https://github.com/cypress-io/code-coverage) generates a code coverage report in html format when tests are run. This file is saved in coverage/lcov-report folder.

### Conclusion
Code coverage helps us identify the parts of our code not covered by test. With Cypress, code coverage is very easy to setup while providing a great value.