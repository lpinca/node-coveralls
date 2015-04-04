#node-coveralls

[![Build Status][travis-image]][travis-url] [![Coverage Status][coveralls-image]][coveralls-url] [![Codeship Build Status][codeship-image]][codeship-url]

[Coveralls.io](https://coveralls.io/) support for node.js.  Get the great coverage reporting of coveralls.io and add a cool coverage button ( like the one above ) to your README.

Supported CI services:  [travis-ci](https://travis-ci.org/), [codeship](https://www.codeship.io/), [circle-ci](https://circleci.com/), [jenkins](http://jenkins-ci.org/)

##Installation: 
Add the latest version of `coveralls` to your package.json:
``` 
npm install coveralls --save 
```

If you're using mocha, add `mocha-lcov-reporter` to your package.json:
``` 
npm install mocha-lcov-reporter --save 
```

##Usage:

This script ( `bin/coveralls.js` ) can take standard input from any tool that emits the lcov data format (including [mocha](http://visionmedia.github.com/mocha/)'s [LCov reporter](https://npmjs.org/package/mocha-lcov-reporter)) and send it to coveralls.io to report your code coverage there.

Once your app is instrumented for coverage, and building, you need to pipe the lcov output to `./node_modules/coveralls/bin/coveralls.js`.

This library currently supports [travis-ci](https://travis-ci.org/) with no extra effort beyond piping the lcov output to coveralls. However, if you're using a different build system, there are a few environment variables that are necessary:
* COVERALLS_SERVICE_NAME  (the name of your build system)
* COVERALLS_REPO_TOKEN (the secret repo token from coveralls.io)

There are optional environment variables for other build systems as well:
* COVERALLS_SERVICE_JOB_ID  (an id that uniquely identifies the build job)
* COVERALLS_RUN_AT  (a date string for the time that the job ran.  RFC 3339 dates work.  This defaults to your 
build system's date/time if you don't set it.)

### [Mocha](http://visionmedia.github.io/mocha/) + [Blanket.js](https://github.com/alex-seville/blanket)
- Install [blanket.js](http://blanketjs.org/)
- Configure blanket according to [docs](https://github.com/alex-seville/blanket/blob/master/docs/getting_started_node.md).
- Run your tests with a command like this:

```sh
NODE_ENV=test YOURPACKAGE_COVERAGE=1 ./node_modules/.bin/mocha \
  --require blanket \
  --reporter mocha-lcov-reporter | ./node_modules/coveralls/bin/coveralls.js
```
### [Mocha](http://visionmedia.github.io/mocha/) + [JSCoverage](https://github.com/fishbar/jscoverage)

Instrumenting your app for coverage is probably harder than it needs to be (read [here](http://www.seejohncode.com/2012/03/13/setting-up-mocha-jscoverage/)), but that's also a necessary step. 

In mocha, if you've got your code instrumented for coverage, the command for a travis build would look something like this:
```sh
YOURPACKAGE_COVERAGE=1 ./node_modules/.bin/mocha test -R mocha-lcov-reporter | ./node_modules/coveralls/bin/coveralls.js
```
Check out an example [Makefile](https://github.com/cainus/urlgrey/blob/master/Makefile) from one of my projects for an example, especially the test-coveralls build target.  Note: Travis runs `npm test`, so whatever target you create in your Makefile must be the target that `npm test` runs (This is set in package.json's 'scripts' property).

### [Istanbul](https://github.com/gotwarlost/istanbul)

**With Mocha:**

```sh
istanbul cover ./node_modules/mocha/bin/_mocha --report lcovonly -- -R spec && cat ./coverage/lcov.info | ./node_modules/coveralls/bin/coveralls.js && rm -rf ./coverage
```

**With Jasmine:**

```sh
istanbul cover jasmine-node --captureExceptions spec/ && cat ./coverage/lcov.info | ./node_modules/coveralls/bin/coveralls.js && rm -rf ./coverage
```

### [Nodeunit](https://github.com/caolan/nodeunit) + [JSCoverage](https://github.com/fishbar/jscoverage)

Depend on nodeunit, jscoverage and coveralls:

```sh
npm install nodeunit jscoverage coveralls --save-dev
```

Add a coveralls script to "scripts" in your `package.json`:

```javascript
"scripts": {
  "test": "nodeunit test",
  "coveralls": "jscoverage lib && YOURPACKAGE_COVERAGE=1 nodeunit --reporter=lcov test | coveralls"
}
```

Ensure your app requires instrumented code when `process.env.YOURPACKAGE_COVERAGE` variable is defined.

Run your tests with a command like this:

```sh
npm run coveralls
```

For detailed instructions on requiring instrumented code, running on Travis and submitting to coveralls [see this guide](https://github.com/alanshaw/nodeunit-lcov-coveralls-example).

### [Poncho](https://github.com/deepsweet/poncho)
Client-side JS code coverage using [PhantomJS](https://github.com/ariya/phantomjs), [Mocha](https://github.com/visionmedia/mocha) and [Blanket](https://github.com/alex-seville/blanket):
- [Configure](http://visionmedia.github.io/mocha/#browser-support) Mocha for browser
- [Mark](https://github.com/deepsweet/poncho#usage) target script(s) with `data-cover` html-attribute
- Run your tests with a command like this:

```sh
./node_modules/.bin/poncho -R lcov test/test.html | ./node_modules/coveralls/bin/coveralls.js
```

### [Lab](https://github.com/hapijs/lab)
```sh
lab -r lcov | ./node_modules/.bin/coveralls
```


## Running locally

If you're running locally, you must have a `.coveralls.yml` file, as documented in [their documentation](https://coveralls.io/docs/ruby), with your `repo_token` in it; or, you must provide a `COVERALLS_REPO_TOKEN` environment-variable on the command-line.

If you want to send commit data to coveralls, you can set the `COVERALLS_GIT_COMMIT` environment-variable to the commit hash you wish to reference. If you don't want to use a hash, you can set it to `HEAD` to supply coveralls with the latest commit data. This requires git to be installed and executable on the current PATH.

[travis-image]: https://travis-ci.org/cainus/node-coveralls.svg?branch=master
[travis-url]: https://travis-ci.org/cainus/node-coveralls

[codeship-image]: https://www.codeship.io/projects/de6fb440-dea9-0130-e7d9-122ca7ee39d3/status
[codeship-url]: https://www.codeship.io/projects/5622

[coveralls-image]: https://img.shields.io/coveralls/cainus/node-coveralls/master.svg
[coveralls-url]: https://coveralls.io/r/cainus/node-coveralls?branch=master

## Contributing

I generally don't accept pull requests that are untested, or break the build, because I'd like to keep the quality high (this is a coverage tool afterall!).

I also don't care for "soft-versioning" or "optimistic versioning" (dependencies that have ^, x, > in them, or anything other than numbers and dots).  There have been too many problems with bad semantic versioning in dependencies, and I'd rather have a solid library than a bleeding edge one.



