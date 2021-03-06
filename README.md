ic-ajax
=======

[![Build Status](https://travis-ci.org/instructure/ic-ajax.png)](https://travis-ci.org/instructure/ic-ajax)

Ember-friendly `jQuery.ajax` wrapper.

- returns RSVP promises
- makes apps more testable (resolves promises with `Ember.run`)
- makes testing ajax simpler with fixture support

Installation
------------

`bower install ic-ajax`

... or ...

`npm install ic-ajax`

Module Support
--------------

Note the `dist` directory has multiple module formats, use whatever
works best for you.

- AMD

  `define(['ic-ajax'], function(ajax) {});`

- Node.JS (CJS)

  `var ajax = require('ic-ajax')`

- Globals

  `var ajax = ic.ajax;`

  All instructure canvas stuff lives on the `ic` global.

API
---

This lib simply wraps `jQuery.ajax` with two exceptions:

- success and error callbacks are not supported
- does not resolve three arguments like $.ajax (real promises only
  resolve a single value). `request` only resolves the response data
  from the request, while `raw` resolves an object with the three
  "arguments" as keys if you need them.

Other than that, use `request` exactly like `$.ajax`.

```js
var ajax = ic.ajax;

App.ApplicationRoute = Ember.Route.extend({
  model: function() {
    return ajax.request('/foo');
  }
}

// if you need access to the jqXHR or textStatus, use raw
ajax.raw('/foo').then(function(result) {
  // result.response
  // result.textStatus
  // result.jqXHR
});
```

Simplified Testing
------------------

In order to test newly added code you must rebuild the distribution.

```bash
broccoli build dist
```

### defineFixture
Adding fixtures with `defineFixture` tells ic-ajax to resolve the promise
with the fixture matching a url instead of making a request. This allows
you to test your app without creating fake servers with sinon, etc.

Example:

```js
ic.ajax.defineFixture('api/v1/courses', {
  response: [{name: 'basket weaving'}],
  jqXHR: {},
  textStatus: 'success'
});

ic.ajax.request('api/v1/courses').then(function(result) {
  deepEqual(result, ic.ajax.lookupFixture('api/v1/courses').response);
});
```

To test failure paths, set the `textStatus` to anything but `success`.

To set a fixture that will match every url with a matching path, regardless of the query string, add an options object as a parameter to `defineFixture` with a property of `fallback` set to true. A fixture will be located for the specific url with a query string, and if no fixture is found, the fallback that matches the path (not considering the query string) will be used.

Example:

```js
ic.ajax.defineFixture('api/v1/courses', {
  response: [{name: 'basket weaving'}],
  jqXHR: {},
  textStatus: 'success'
}, {
  fallback: true
});

ic.ajax.request('api/v1/courses?this=that').then(function(result) {
  deepEqual(result, ic.ajax.lookupFixture('api/v1/courses').response);
});
```

### lookupFixture
Lookup a fixture. If successful, the fixture will be returned, otherwise `undefined` will be returned.

```js
var coursesFixture = ic.ajax.lookupFixture('api/v1/courses');
```

### removeFixture
Remove a specific fixture. Pass in the url, the fixture that matches that url, if any, will be removed.

```js
ic.ajax.removeFixture('api/v1/courses');
```

### removeAllFixtures

```js
ic.ajax.removeAllFixtures();
```


Contributing
------------

Install dependencies and run tests with the following:

```sh
npm install
npm test
```

For those of you with release privileges:

```sh
npm run-script release
```

Special Thanks
--------------

Inspired by [discourse ajax][1].

License and Copyright
---------------------

MIT Style license

(c) 2014 Instructure, Inc.


  [1]:https://github.com/discourse/discourse/blob/master/app/assets/javascripts/discourse/mixins/ajax.js#L19

