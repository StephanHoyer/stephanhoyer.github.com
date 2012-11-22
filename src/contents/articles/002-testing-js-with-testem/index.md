title: Testing JavaScript with test'em
author: stephan-hoyer
date: 2012-11-22 11:11
template: article.jade
tags: javascript, testing

Frontend JavaScript testing has always been painfull, that's why most people
avoid it, although there writing tests for their backend code.

There are some pretty nice js-testing tools out there today but in most of
them, you need to reload the browser every time you change code or tests. If
you want to test on multiple browsers this can be awkward.

[**Test'em**](https://github.com/airportyh/testem) tries to solve this flaw. 
It's pretty much a meta testrunner. It enables you to run browser tests 
automatically from the commandline.

## Setup

So here is how to setup. First you have to install
[**node.js**](http://nodejs.org) as showed on the node.js
[homepage](http://nodejs.org). Then install Test'em via 
[**npm**](http://npmjs.org):

```
$ npm install testem -g
```

Now you should be able to run the test'em command:

```
$ testem
```

If you see something like the following, all is setup right:

```
TEST'EM 'SCRIPTS!                                                                                    
Open the URL below in a browser to connect.                                                          
http://localhost:7357
```

## Basics

For our first test we create a new directory called `testem`:

```
$ mkdir testem
$ cd testem
```

Now you can start testem and attach a browser to
[localost:7357](http://localhost:7357). You can now hide this window, because
all feedback of the testrunner is also given in the test'em command line
interface. If you open it up, you can see a tab for each browser you attached
to test'em.

Try to add at least one other browser to test'em.

Now it's time to write our first test. Create a file name `test_hello.js` in
the project root. Add the following code to the file:

```
// test_hello.js
describe('hello', function(){
  it('should say hello', function(){
    expect(hello).toBeDefined();
  });
});
```

If you save file, test'em will automatically reload the browser and run the
test. Since there is no `hello` variable defined, the tests fails as expected.

So lets create a new file with the implemantation. Therefore create a file
named `hello.js` in the root of our project that contains:

```
// hello.js
function hello() {
}
```

The test should be green. So let's complete this simple method be adding
another expectation:

```
// test_hello.js
describe('hello', function(){
  it('should say hello', function(){
    expect(hello).toBeDefined();
    expect(hello()).toEqual('hello');
  });
});
```

In save, the tests should again fail. So change the implementation according
to the expectation:

```
// hello.js
function hello() {
  return 'hello';
}
```

That's it, tests are green again.

## Extended usage

### Other testing framework

Frist thing we want to change is the testrunner. Since I use
[**mocha**](http://visionmedia.github.com/mocha/) and
[**expect.js**](https://github.com/LearnBoost/expect.js) on server side I 
want to be able to also use it in browser tests.

Therefore we have to create a test'em configuration file called `testem.json`:

```
// testem.json
{
    "framework": "mocha"
}
```

You have to restart the server so the new configuration is loaded. The tests
are failing now expect.js is not build in to mocha and have to be included
seperatly. Download expect.js to the projects root

```
$ wget https://raw.github.com/LearnBoost/expect.js/master/expect.js
```

Expect.js's syntax is a little bit different from jasmin's so we have to
change this:

```
// test_hello.js
describe('hello', function(){
  it('should say hello', function(){
    expect(hello).to.a('function');
    expect(hello()).to.be('hello');
  });
});
```

Ok, test green again!

### Start browsers automaticaly

It's odd to manually connect test'em to each browser, right? So test'em is
able to do this automatically for you be either giving a command line option
or, like we do now, changing the config:

```
// testem.json
{
    "launch_in_dev": ["Chrome", "Firefox"],
    "framework": "mocha"
}
```

If you restart test'em server now, chrome and firefox should be started
automatically.

### Collect files

Normally js-files are not in the project root, so we have to change this be
also altering the config file:

```
// testem.json
{
    "launch_in_dev": ["Chrome", "Firefox"],
    "framework": "mocha",
    "src_files": [
        "js/**/*.js"
    ]
}
```

So now we can move all the files to a new directory called `js` to clean up
our project root.

### Using phantom js

[PhantomJS](http://phantomjs.org/) is a Webkit-based headless browser. 
It's really fast, since it does not have any UI. So it's great as a first 
test target during development.

Test'em will automatically use it if you have PhantomJS installed. To do so
simply install it via npm:

```
$ npm install phantomjs
```

Then do a

```
$ testem launchers
```

and verify that it's in the list.

### Console.log

Try to make a `console.log()` in any js file.

### Preprocessors

It is also possible to let test'em compile your
[coffee](http://coffeescript.org/)/[type-script](http://www.typescriptlang.org/) 
files before sending them to the browser. You only need to specify this in the
config file:

```
// testem.json
{
    "framework": "mocha",
    "src_files": [
        "js/**/*.coffee"
    ],
    "before_tests": "coffee -c *.coffee",
    "serve_files": [
        "js/**/*.js"
    ],
}
```

That's it. Hope you are now testing all of your javascript code.
