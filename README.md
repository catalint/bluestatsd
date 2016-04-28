# bluestatsd

[![Build Status](https://secure.travis-ci.org/catalint/bluestatsd.png)](http://travis-ci.org/catalint/bluestatsd)
[![Coverage Status](https://coveralls.io/repos/github/catalint/bluestatsd/badge.svg?branch=master)](https://coveralls.io/github/catalint/bluestatsd?branch=master)
[![Code Climate](https://codeclimate.com/github/catalint/bluestatsd.png)](https://codeclimate.com/github/catalint/bluestatsd)
[![NPM version](https://badge.fury.io/js/bluestatsd.png)](http://badge.fury.io/js/bluestatsd)
[![Dependency Status](https://david-dm.org/catalint/bluestatsd.png)](https://david-dm.org/catalint/bluestatsd)

[![NPM](https://nodei.co/npm/bluestatsd.png?downloads=true&stars=true)](https://nodei.co/npm/bluestatsd/)

## Installation

```bash
npm install bluestatsd
```

## Usage

To install this plugin on your Hapi server, do something similar to this:

```js
const Hapi = require('hapi');
const server = new Hapi.Server();

const statsdConfig = {};

server.register({ register: require('bluestatsd'), options: statsdConfig }, (err) => {

	if (err) {
		console.log('error', 'Failed loading plugin: bluestatsd');
	}
});
```

## Plugin Options

### `template`

A template to use for the stat names to send to statsd. This can be any string that could include the following tokens that get replaced with their actual values:

* `{path}` - the path that the request was routed to (e.g `'/users/{id}'`)
* `{method}` - the HTTP verb used on the request (e.g. `'GET'`)
* `{statusCode}` - the numerical status code of the response that the server sent back to the client (e.g. `200`)

Defaults to `'{path}.{method}.{statusCode}'`

### `statsdClient`

An instance of a particular statsd client that you prefer to use for sending metrics to statsd. If this is used, then the `statsdHost` and `prefix` options are ignored. Defaults to `null`

### `host`

The host athat an instance of statsd is running on. An instance of the `statsd-client` NPM module will be created and will be configured to use this host. Defaults to `'localhost'`

### `port`

The port that an instance of statsd is listening on. An instance of the `statsd-client` NPM module will be created and will be configured to use this port. Defaults to `8125`

### `prefix`

The prefix to add to every stat collected. Usually used for grouping a set of stats under one hierarchy in graphite. Defaults to `'hapi'`

### `pathSeparator`

A character or set of characters to replace the '/' (forward slash) characters in your URL path since forward slashes cannot be used in stat names. Defaults to `'_'`

### `dotReplacer`
A character or set of characters to replace the '.' (dot) characters in your URL path since dots have other meanings. Defaults to `'-'`


## Example

A Hapi route configured like this:

```js
server.route({
	method: 'GET',
	path: '/test/{param}',
	handler: (request, reply) => {
		reply('Success!');
	}
});
```

would send an increment and timing stat to statsd with the following stat name (assuming all options are set to their defaults):

	hapi.test_{param}.GET.200

As the [statsd client](https://github.com/msiebuhr/node-statsd-client) is also exposed to the hapi server, you can use any of its methods, e.g.:

```js
server.statsd.increment('systemname.subsystem.value');
server.statsd.gauge('what.you.gauge', 100);
server.statsd.set('your.set', 200);
```

