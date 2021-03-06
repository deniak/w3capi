
# w3capi — A Node client for the W3C API

***WARNING***: This library is a client for an API that is currently in private testing. Using it
will break in all sorts of ways. There are bugs. There will be changes. Do not use this unless you
know what you're doing.

***YOU HAVE BEEN WARNED***

Ok, so, on to the useful bits.

This library provides a client for the W3C API, which exposes information about things such as
specifications, groups, users, etc. It follows a simple pattern in which one builds up a query, and
then causes the data to be fetched.

## Installation

The usual:

    npm install w3capi

## IMPORTANT

The API is only available if you have an API key. In order to obtain one, you need to apply through
your W3C account page.

If you wish to run the tests, you need to set an environment variable named `W3CAPIKEY` to that 
value, as in

    W3CAPIKEY=deadb33f mocha

## API

This documentation does not describe the fields that the various objects have; refer to the W3C 
API's documentation for that.

Everything always starts in the same way:

```js
var w3c = require("w3c");
w3c.apiKey = "your-api-key";
```

You *will* need an API key. Nothing will work without it. ***NOTE***: this library does not support
keys that were created with a list of origins. You should generate a key with no list of domains.

This gives you a client instance that's immediately ready to work. You then chain some methods to
specify what you want to get, and fetch with a callback. For example:

```js
var w3c = require("w3capi")
,   handler = function (err, data) {
        if (err) return console.error("[ERROR]", err);
        console.log(data);
    }
;

// just list all the groups
w3c.groups()
   .fetch(handler)
;

// get the editors for a specific version of a specification
w3c.specification("SVG11")
   .version("20030114")
   .editors
   .fetch(handler)
;
```

If you are familiar with the W3C API you know that it supports paging. This library hides that fact
and when it sees a paged list of results it *always* fetches the whole set. Typically that is a 
very reasonable number of items.

### `fetch([options], cb)`

All queries end with a call to `fetch()`. You can pass `{ embed: true }` as an option if you with
for the returned value to embed some of the content from the API (this matches `?embed=true`). At
this point that is the only option. You can do without the `options` altogether.

The `cb` receives the typical `err` and `data` parameters.

### Specifications

Usage summary:

```js
w3c.specifications().fetch()
w3c.specification("SVG").fetch()
w3c.specification("SVG").versions().fetch()
w3c.specification("SVG").version("19991203").fetch()
w3c.specification("SVG").version("19991203").deliverers().fetch()
w3c.specification("SVG").version("19991203").editors().fetch()
w3c.specification("SVG").version("19991203").previous().fetch()
w3c.specification("SVG").version("19991203").next().fetch()
w3c.specification("SVG11").latest().fetch()
w3c.specification("SVG").superseded().fetch()
w3c.specification("SVG11").supersedes().fetch()
```

You can list all specifications, or get a single one using its shortname. For a given specification,
you can list its versions and for a given version its editors and deliverers (the groups who shipped
it), as well as which versions were the previous or next. You can know which specification 
supersedes or was superseded by which other. You can use `latest()` to get the latest version 
without having to list them.

### Groups

Usage summary:

```js
w3c.groups().fetch()
w3c.group(54381).fetch()
w3c.group(54381).chairs().fetch()
w3c.group(54381).services().fetch()
w3c.group(54381).specifications().fetch()
w3c.group(54381).teamcontacts().fetch()
w3c.group(54381).users().fetch()
w3c.group(54381).charters().fetch()
w3c.group(46884).charter(89).fetch()
```

You can list all groups or get a specific one by its ID (this is the same ID used in IPP, if you're
familiar with that — also the same used in ReSpec for the group). There are several sublists that
can be obtained, that are hopefully self-explanatory. The charters can be listed, and a specific
charter can be fetched given its ID (an opaque number).

### Users

Usage summary:

```js
w3c.user("ivpki36ou94oo08osswccs80gcwogwk").fetch()
w3c.user("ivpki36ou94oo08osswccs80gcwogwk").affiliations().fetch()
w3c.user("ivpki36ou94oo08osswccs80gcwogwk").groups().fetch()
w3c.user("ivpki36ou94oo08osswccs80gcwogwk").specifications().fetch()
```

Users cannot be listed, and the ID used to fetch them is an opaque identifier (not the ID used 
internally in the system so as to make it harder to slurp them all in). Various sublists can be
obtained.

### Domains

Usage summary:

```js
w3c.domains().fetch()
w3c.domain(41481).fetch()
w3c.domain(41481).groups().fetch()
w3c.domain(41481).activities().fetch()
w3c.domain(41481).services().fetch()
w3c.domain(41481).users().fetch()
```

Domains are an organisational structure internal to the W3C, of little interest to the outside world
except to note that the Interaction Domain is the best.

### Services

Usage summary:

```js
w3c.services(2).fetch()
w3c.services(2).groups().fetch()
```

Services model tools that groups (or domains) can use, such as IRC, a bug tracker, a mailing list,
etc. At this point in time, the services database isn't well-maintained but it could become more
useful in future.
